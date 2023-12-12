<script lang="ts">
	import { T } from '@threlte/core';
	import { AutoColliders, CollisionGroups, Debug } from '@threlte/rapier';
	import { BoxGeometry, MeshStandardMaterial, Mesh, Group } from 'three';

	// import Door from '../../rapier/world/Door.svelte';
	import Player from './Player.svelte';

	import { OrbitControls, Environment } from '@threlte/extras';
	import { RigidBody } from '@threlte/rapier';


	import { GLTF, useGltf } from '@threlte/extras';
	import { derived } from 'svelte/store';
	import RAPIER, { Collider } from '@dimforge/rapier3d-compat';

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

<T.DirectionalLight castShadow position={[8, 20, -3]} />
<T.AmbientLight intensity={0.2} />

<Debug />

<T.GridHelper args={[50]} position.y={0.01} />

<CollisionGroups groups={[0, 15]}>
	<!-- <AutoColliders shape={'cuboid'}>
		<T.Mesh receiveShadow geometry={new BoxGeometry(1, 1, 100)} />
		<T.Mesh receiveShadow geometry={new BoxGeometry(100, 1, 1)} />
	</AutoColliders> -->

	{#if $MainGround && $MainGate && $MainSign}
		<RigidBody type={'fixed'}>
			<AutoColliders shape={'trimesh'}>
				<T.Mesh position={[0, -1, 0]} geometry={$MainGround.nodes.Plane.geometry}>
					<T.MeshStandardMaterial visible={true} />
				</T.Mesh>

				<T.Mesh position={[0, -1, 0]} geometry={$MainGate.nodes.Gate.geometry}>
					<T.MeshStandardMaterial visible={true} />
				</T.Mesh>

				<T.Mesh position={[0, -1, 0]} geometry={$MainSign.nodes.Plane.geometry}>
					<T.MeshStandardMaterial visible={true} />
				</T.Mesh>
			</AutoColliders>
		</RigidBody>
	{/if}
</CollisionGroups>

<Player />
