<!-- Where all the actual chatting action is. -->

<script>
	import Home from "./Home.svelte";
	import Profile from "./Profile.svelte";
	import Settings from "./Settings.svelte";

	import Sidebar from "./Sidebar.svelte";

	import {mainPage as page} from "../lib/stores.js";

	let pagesset = ["home","profile","settings","blank","error"] // for random page changing
	function getRandomInt(max) {
		return Math.floor(Math.random() * max);
	}
	setInterval(function() {page.set(pagesset[getRandomInt(pagesset.length)])},15000)

	page.set("home");
</script>

<div class="main-screen">
	<div class="sidebar">
		<Sidebar />
	</div>
	<div class="view"> 
		{#if $page === "home"}
			<Home />
		{:else if $page === "profile"}
			<Profile />
		{:else if $page === "settings"}
			<Settings />
		{:else if $page === "blank"}
			<div></div>
		{:else}
			H O W
			<br />
			(Current page: {$page})
			<br />
			<iframe id="BetterAd"></iframe>
			<script src="https://adservice.bettermeower.app/assets/scripts/embed.js"></script>
		{/if}
	</div>
</div>

<style>
	.main-screen {
		box-sizing: border-box;
	}

	.sidebar {
		position: fixed;
		left: 0;
		top: 0;
		width: 3.5em;
		height: 100%;
		z-index: 100;
	}
	.view {
		margin-left: 3.5em;
		padding: 0.33em;
	}

	@media (max-aspect-ratio: 1/1) {
		.view {
			margin-left: 0;
			margin-top: 3.5em;
		}
		.sidebar {
			width: 100%;
			height: 3.5em;
		}
	}
	:global(main.layout-old) .view {
		margin-left: 0;
		margin-top: 3.5em;
	}
	:global(main.layout-old) .sidebar {
		width: 100%;
		height: 3.5em;
	}
</style>
