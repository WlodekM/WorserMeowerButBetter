<!-- Meower Svelte, the app itself. -->

<script>
	import Main from "./screens/Main.svelte";
	import Setup from "./screens/Setup.svelte";
	import Spinner from "./lib/Spinner.svelte";
	import {apiUrl} from "./lib/urls.js";
	import {link} from "./lib/clmanager.js";

	import {
		screen, setupPage,
		disconnected, disconnectReason,
		user, spinner
	} from "./lib/stores.js";
	import {tick} from "svelte";
	screen.set("setup");
	setupPage.set("start");
</script>

<main
	id="main"
	class:theme-orange={$user.theme==="orange"}
	class:theme-blue={$user.theme==="blue"}
	class:mode-light={!($user.mode === false)}
	class:mode-dark={$user.mode === false}
	class:layout-old={$user.layout === "old"}
>
	{#if $disconnected}
		<div class="disconnected">
			<div class="disconnected-inner">
				<h1>Me-OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW</h1>
				{#if $disconnectReason === ""}
					Mikedev hates you LOLOLOL
					<br />
					<iframe id="BetterAd"></iframe>
					<script src="https://adservice.bettermeower.app/assets/scripts/embed.js"></script>
					<br />
					DONT Reconnect using the below button, Mikedev will kick you again anyways:
				{:else if $disconnectReason === "E:119 | IP Blocked"}
					The server hates you, good job..
					<br />
					<iframe id="BetterAd"></iframe>
					<script src="https://adservice.bettermeower.app/assets/scripts/embed.js"></script>
					<br />
					This might be due to a reason that will make us sound sketchy
					<br />
					or The server hates you 100%
					<br />
					DONT reconnect
				{:else}
					We killed you
					<pre><code>{$disconnectReason}</code></pre>
					<iframe id="BetterAd"></iframe>
					<script src="https://adservice.bettermeower.app/assets/scripts/embed.js"></script>
					DONT Reconnect using the ahead button:
				{/if}
				<br /><br />
				<button 
					on:click={async () => {
						screen.set("setup");
						disconnected.set(false);
						await tick();
						setupPage.set("reconnect");
					}}
				>
					SHUT THE HECK UP YOU PEICE OF CRAP!!!
				</button>
			</div>
		</div>
	{:else}
		{#if $screen === "setup"}
			<Setup />
		{:else}
			<Main />
		{/if}
	{/if}

	{#if $spinner}
		<div class="spinner-container">
			<Spinner />
		</div>
	{/if}
</main>

<style>
	.disconnected {
		background-color: var(--orange-light);
		color: var(--orange);

		width: 100%;
		height: 100%;

		position: absolute;
		top: 0;
		left: 0;
		z-index: 1000000;

		text-align: center;
		font-size: 100%;
	}

	#main {
		width: 100%;
		height: 100%;

		font-family: Simvoni, sans-serif;

		--orange: hotpink;
		--orange-button: var(--orange);
		--orange-light: pink;
		--orange-dark: salmon;
		--orange-scrollbar-back: maroon;
		--background: white;
		--foreground: rgb(0, 57, 0);
		--foreground-orange: white;

		--pfp-bg: white;
		--pfp-outline: #d9d9d9;

		background-color: var(--background);
		color: var(--foreground);
		scrollbar-color: var(--orange) var(--orange-scrollbar-back);
		font-size: 10pt;
	}
	p {
		display: none;
	}
</style>
