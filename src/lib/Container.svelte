<!--
	The typical Meower container, used literally everywhere*.
	*in the homepage, settings and profile
-->

<div><marquee><slot /><marquee></div>

<style>
	div {
		background-color: var(--background);
		border: solid 40px var(--orange);
		border-radius: 10%;
		position: relative;
	}
</style>
