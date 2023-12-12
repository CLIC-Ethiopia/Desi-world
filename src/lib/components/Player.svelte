<script lang="ts">
	import { Euler, MeshStandardMaterial, SphereGeometry, Vector3 } from 'three';
	import { T, useFrame, useThrelte } from '@threlte/core';
	import { RigidBody, CollisionGroups, Collider } from '@threlte/rapier';
	import { createEventDispatcher } from 'svelte';
	import Controller from './ThirdPersonControls.svelte';
	import Character2 from './Character2.svelte';


	import { GLTF, useGltfAnimations } from '@threlte/extras';
	import { backward, forward, left, right, up } from '$lib/stores/characterControlMovement';

	const { gltf, actions } = useGltfAnimations();

	export let position = [0, 3, 0];
	export let radius = 0.3;
	export let height = 1.7;
	export let speed = 6;

	let capsule;
	let capRef;
	$: if (capsule) {
		capRef = capsule;
	}
	let rigidBody;

	const { renderer } = useThrelte();
	if (!renderer) throw new Error();

	const temp = new Vector3();
	const dispatch = createEventDispatcher();

	let grounded = false;
	$: grounded ? dispatch('groundenter') : dispatch('groundexit');

	useFrame(() => {
		if (!rigidBody || !capsule) return;
		// get direction
		const velVec = temp.fromArray([0, 0, $forward - $backward]); // left - right

		// sort rotate and multiply by speed
		velVec.applyEuler(new Euler().copy(capsule.rotation)).multiplyScalar(speed);

		// don't override falling velocity
		const linVel = rigidBody.linvel();
		temp.y = linVel.y;

		temp.y += $up; // Add the desired jump height to the y component of the velocity vector

		// finally set the velocities and wake up the body
		rigidBody.setLinvel(temp, true);

		// when body position changes update camera position
		const pos = rigidBody.translation();
		position = [pos.x, pos.y, pos.z];

		// make the jump if we press space
	});

	function onKeyDown(e: KeyboardEvent) {
		switch (e.key) {
			case 's':
				$backward = 1;
				break;
			case 'w':
				$forward = 1;
				break;
			case 'a':
				$left = 1;
				break;
			case 'd':
				$right = 1;
				break;
			case ' ':
				$up = 1;
				break;
			default:
				break;
		}
	}

	function onKeyUp(e: KeyboardEvent) {
		switch (e.key) {
			case 's':
				$backward = 0;
				break;
			case 'w':
				$forward = 0;
				break;
			case 'a':
				$left = 0;
				break;
			case 'd':
				$right = 0;
				break;
			case ' ':
				$up = 0;
				break;
			default:
				break;
		}
	}
</script>

<svelte:window on:keydown|preventDefault={onKeyDown} on:keyup={onKeyUp} />

<T.PerspectiveCamera makeDefault fov={90}>
	<Controller bind:object={capRef} />
</T.PerspectiveCamera>

<T.Group bind:ref={capsule} {position} rotation.y={Math.PI}>
	<RigidBody bind:rigidBody enabledRotations={[false, false, false]}>
		<CollisionGroups groups={[0]}>
			<Collider shape={'capsule'} args={[height / 2 - radius, radius]} />
			<!-- <T.Mesh geometry={new BoxGeometry(0.3, 1.8 - 0.3 * 2)}>
					<T.MeshBasicMaterial color={'red'} />
				</T.Mesh> -->
		</CollisionGroups>
	</RigidBody>

	<Character2 />
</T.Group>
