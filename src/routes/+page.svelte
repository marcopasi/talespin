<script lang="ts">
	import { fade } from 'svelte/transition';
	import { goto } from '$app/navigation';
	import { getToastStore } from '@skeletonlabs/skeleton';

	import { nameStore } from '$lib/store';
	import { get } from 'svelte/store';
	import { http_host } from '$lib/gameServer';

	let name = get(nameStore) || '';
	let roomCode = '';
	let joinGameClicked = false;
	let toastStore = getToastStore();

	$: nameStore.set(name);

	async function createGame() {
		if (roomCode !== '') {
			return joinGame();
		}

		let res = await fetch(`${http_host}/create`, {
			method: 'POST'
		});
		res = await res.json();

		if ((<any>res).RoomState) {
			goto(`/game/${(<any>res).RoomState.room_id}`);
		}
	}

	async function joinGame() {
		if (joinGameClicked) {
			let res = await fetch(`${http_host}/exists`, {
				method: 'POST',
				body: JSON.stringify(roomCode),
				headers: {
					'Content-Type': 'application/json'
				}
			});
			res = await res.json();

			if (res) {
				goto(`/game/${roomCode}`);
			} else {
				toastStore.trigger({
					message: '😭 Room does not exist',
					autohide: true,
					timeout: 2500
				});
			}
		} else {
			joinGameClicked = true;
		}
	}
</script>

<div class="max-w-md mx-auto p-4 mt-5">
	<h1 class="h1">
		<span
			class="bg-gradient-to-br from-red-500 to-yellow-500 bg-clip-text text-transparent box-decoration-clone"
			>Al confine!</span
		>
	</h1>
	<h4 class="h4"
        style="line-height:1.0rem"
    >
		<span
			class="bg-gradient-to-br from-yellow-500 to-red-500 bg-clip-text text-transparent box-decoration-clone"
			>...del celibato</span
		>
	</h4>
	<h5 class="h5"
        style="line-height:1.0rem"
    >
		<span
			class="bg-gradient-to-b from-indigo-100 to-indigo-950 bg-clip-text text-transparent box-decoration-clone"
            style="font-size:0.75rem"
			>Storie di vita ai confini della realtà.</span
		>
	</h5>

	<div class="card p-4 mt-8">
		<div class="mb-4">
			<label for="name">Name:</label>
			<input
				type="text"
				id="name"
				bind:value={name}
				class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
			/>
		</div>

		{#if joinGameClicked}
			<div transition:fade>
				<label for="roomCode">Room code:</label>
				<input
					type="text"
					id="roomCode"
					bind:value={roomCode}
					class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-4 leading-tight focus:outline-none focus:shadow-outline"
				/>
			</div>
		{/if}

		<div class="flex justify-between mb-4">
			<button on:click={() => createGame()} class="btn variant-filled">New game</button>
			<button on:click={() => joinGame()} class="btn variant-filled">Join game</button>
		</div>
	</div>
	<div class="flex justify-center mt-4">
		<a href="/how-to-play" class="link text-center underline">Instructions</a>
	</div>
</div>
