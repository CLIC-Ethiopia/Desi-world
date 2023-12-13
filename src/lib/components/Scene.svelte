<script lang="ts">
	import { T } from '@threlte/core';
	import { AutoColliders, CollisionGroups, Debug } from '@threlte/rapier';

	// import Door from '../../rapier/world/Door.svelte';
	import Player from './Player.svelte';

	import { OrbitControls, Environment, Sky, SoftShadows } from '@threlte/extras';
	import { RigidBody } from '@threlte/rapier';

	import { GLTF, useGltf } from '@threlte/extras';
	import { derived } from 'svelte/store';
	import RAPIER, { Collider } from '@dimforge/rapier3d-compat';
	import { Color, MeshBasicMaterial } from 'three';

	let nsubdivs = 10;
	let heights = [];
	const scale = new RAPIER.Vector3(10.0, 1, 10);

	const MainGround = useGltf('./models/desi-world/ground-transformed.glb', {
		useDraco: true
	});

	const MainGate = useGltf('./models/desi-world/gate-transformed.glb', {
		useDraco: true
	});

	const MainSign = useGltf('./models/desi-world/sign-transformed.glb', {
		useDraco: true
	});
</script>

<!-- <T.DirectionalLight castShadow position={[8, 20, -3]} /> -->
<!-- <T.AmbientLight intensity={0.2} /> -->
<!-- sky -->
<!-- <Environment
	path="/"
	files="hdr2.hdr"
	isBackground={true}
	groundProjection={{ radius: 200, height: 5, scale: { x: 100, y: 100, z: 100 } }}
/> -->

<Sky setEnvironment={true} elevation={1} />

<!-- <Debug /> -->

<!-- <T.GridHelper args={[50]} position.y={0.01} /> -->

<CollisionGroups groups={[0, 15]}>
	<!-- <AutoColliders shape={'cuboid'}>
		<T.Mesh receiveShadow geometry={new BoxGeometry(1, 1, 100)} />
		<T.Mesh receiveShadow geometry={new BoxGeometry(100, 1, 1)} />
	</AutoColliders> -->

	{#if $MainGround && $MainGate && $MainSign}
		<RigidBody type={'fixed'}>
			<AutoColliders shape={'trimesh'}>
				<T.Mesh
					position={[0, -3, 0]}
					geometry={$MainGround.nodes.Plane.geometry}
					material={$MainGround.materials.Grass}
					receiveShadow
					castShadow
				></T.Mesh>

				<T.Mesh
					position={[0, -3, 0]}
					geometry={$MainGround.nodes.Plane_1.geometry}
					material={$MainGround.materials.Brown}
					receiveShadow
					castShadow
				></T.Mesh>

				<T.Mesh
					position={[0, -3, 0]}
					geometry={$MainGate.nodes.Gate.geometry}
					material={$MainGate.materials.WHITE}
					receiveShadow
					castShadow
				>
					<T.MeshStandardMaterial visible={true} />
				</T.Mesh>

				<T.Mesh
					position={[0, -3, 0]}
					scale={0.75}
					geometry={$MainSign.nodes.Plane.geometry}
					material={$MainSign.materials.Brown}
				>
					<T.MeshStandardMaterial visible={true} />
					<T.MeshBasicMaterial color={0x000000} />
				</T.Mesh>
				<T.Mesh position={[0, -3, 0]} scale={0.75} geometry={$MainSign.nodes.Plane_1.geometry}>
					<T.MeshStandardMaterial visible={true} />
				</T.Mesh>
			</AutoColliders>
		</RigidBody>
	{/if}
</CollisionGroups>

<Player />
