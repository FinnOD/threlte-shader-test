<script lang="ts">
    import { T, useFrame } from '@threlte/core';
    import { OrbitControls } from '@threlte/extras';
	import ShaderMaterial from './ShaderMaterial.svelte';
	import { SphereGeometry, Vector3 } from 'three';

    $: pos = new Vector3(1, 0, 0);
    useFrame(({clock}) => {
        let t = clock.getElapsedTime();
        pos.set(10*Math.sin(t), 3+(3*Math.sin(t/4)), 10*Math.cos(t))
        pos = pos;

    });

    let geom = new SphereGeometry( 1, 100, 100 ); 

</script>

<T.PerspectiveCamera makeDefault position={[-10, 10, 10]} fov={45}>
    <OrbitControls />
</T.PerspectiveCamera>

<!-- moving light -->
<T.Mesh position={[pos.x, pos.y, pos.z]} rotation.x={Math.PI/2}>
    <T.SphereGeometry />
    <T.MeshStandardMaterial color="white"/>
    <T.PointLight></T.PointLight>
</T.Mesh>

<T.Mesh rotation.x={-Math.PI/2.0002342} scale={10}>
    <T.PlaneGeometry />
    <T.MeshStandardMaterial color="grey" />
</T.Mesh>

<T.Mesh position.y={1.1} position.x={2} geometry={geom}>
    <ShaderMaterial ></ShaderMaterial>
</T.Mesh>

<T.Mesh position.y={1.1} position.x={-2} geometry={geom}>
    <T.MeshLambertMaterial color="white" wireframe={false}/>
</T.Mesh>


