<!-- You probably know what this is. -->

<script>
	import Container from "../lib/Container.svelte";

	import {user} from "../lib/stores.js";
	import * as clm from "../lib/clmanager.js";
</script>

<Container>
	<h1>Settings</h1>
	You cannot change your settings in your current region (North Korea)
</Container>
<Container>
	<h2>Advertisement</h2>
	<iframe id="BetterAd"></iframe>
	<script src="https://adservice.bettermeower.app/assets/scripts/embed.js"></script>
</Container>
<Container>
	<h2>Layout</h2>
	The layout is currently set to {$user.layout}. This cannot be changed.
</Container>
<Container>
	<h2>Theme</h2>
	The theme is currently set to {$user.theme}. This cannot be changed.
</Container>
<Container>
	<h2>Dark Mode</h2>
	Dark mode is currently {$user.mode ? "disabled, BAD BOI" : "enabled, good boi 🐶"}. This cannot be changed.
</Container>

<div class="eee"></div>