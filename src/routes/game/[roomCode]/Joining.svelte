<script lang="ts">
	import type GameServer from '$lib/gameServer';
	import type { PlayerInfo } from '$lib/types';
	import { Avatar } from '@skeletonlabs/skeleton';

	export let players: { [key: string]: PlayerInfo } = {};
	export let roomCode: string = '';
	export let gameServer: GameServer;
	export let name = '';

	function getInitialsFromString(name: string) {
		return name
			.split(' ')
			.map((n) => n[0])
			.join('');
	}
</script>

<div class="m-auto w-80/10">
	<div class="max-w-md mx-auto">
		<h1 class="text-3xl text-center">Hi {name}, sei <u>al Confine!</u></h1>
		<h2 class="text-xl text-center">
		    Room
			<code class="code text-lg">{roomCode}</code>
		</h2>
		<div class="container flex flex-wrap justify-center gap-4 mt-10">
			{#each Object.entries(players) as [key, value]}
				<div class=" p-5">
					<div>
						<Avatar
							initials={getInitialsFromString(key)}
							background={value.ready ? 'bg-success-500' : 'bg-error-500'}
						/>
					</div>

					<div class="font-bold text-center">{key}</div>
				</div>
			{/each}
		</div>

		<div class="flex flex-col gap-2 mt-10">
			<button
				disabled={players && players[name] ? players[name].ready : false}
				class="btn variant-filled"
				on:click={() => gameServer.ready()}>Ready</button
			>
		</div>
	</div>
</div>
