<script lang="ts">
	import { T, useFrame, useThrelte } from '@threlte/core';
	import { UniformsLib, UniformsUtils, Vector3 } from 'three';

	const { clock } = useThrelte();
	let uniformsDefaults = {
		u_time: { value: clock.getElapsedTime() },
        diffuse: {
            value: new Vector3(0.2,0,0)
        }
	};

    // What is going on here? Nothing??
	let uniforms = UniformsUtils.merge([UniformsLib['lights'], uniformsDefaults]);

	const vShader = `
    varying vec3 vPos;
    varying vec3 vNormal;
    void main() {
      vPos = (modelMatrix * vec4(position, 1.0 )).xyz;
      vNormal = normalMatrix * normal;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position,1.0);
    }`;


	const fShader = `
    uniform vec3 diffuse;
    varying vec3 vPos;
    varying vec3 vNormal;

    struct PointLight {
        vec3 position;
        vec3 color;
    };
    uniform PointLight pointLights[ NUM_POINT_LIGHTS ];

    void main() {
        vec4 addedLights = vec4(vec3(0.0), 1.0);
        for(int l = 0; l < NUM_POINT_LIGHTS; l++) {
            vec3 adjustedLight = pointLights[l].position + cameraPosition;
            vec3 lightDirection = normalize(vPos - adjustedLight);
            // addedLights.rgb += clamp(dot(-lightDirection, vNormal), 0.0, 1.0) * pointLights[l].color;
        }
        gl_FragColor = mix(vec4(diffuse.x, diffuse.y, diffuse.z, 1.0), addedLights, addedLights);
    }`;

	useFrame(({ clock }) => {
		uniforms['u_time']['value'] = clock.getElapsedTime();
	});
</script>

<T.ShaderMaterial vertexShader={vShader} fragmentShader={fShader} {uniforms} />
