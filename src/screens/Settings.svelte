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
<Container>
	<h2>ad</h2>
	To get this feature, Download our sponsor NordVPN, Staying safe online is an ever growing difficulty and you could be exploited by hackers. NordVPN allows you to change your IP address, making you harder to track, securing your privacy. Go to nordvpn.com/yourmom to get 4389230948230948239048230% off your first purcase
</Container>

<div class="eee"></div>