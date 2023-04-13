<!-- To nobody's surprise, a profile picture! -->

<script>
	export let icon = -1;
	export let alt = "You cannot view this PFP.";
	export let online = false;
	export let big = false;
	
	// only respond to `icon` changing
	let id;
	function setId(val) {
		id = val;
	}
	$: setId(icon);
</script>

<span class="pfp-container" class:big>
	{#if online}
		<span class="online"></span>
	{/if}
	<span class="pfp">
		<img
			alt="You cannot view this PFP."
			title={alt}
			src={new URL(
				`./../assets/avatars/icon_${
					id === -1 ? 21 : id === -2 ? "err" : id - 1
				}.svg`,
				import.meta.url
			).href}
			on:error|once={() => (id = -2)}
			class:loading={icon === -1}
			draggable={false}
			width="auto"
			height="100%"
		>
	</span>
</span>

<style>
	.pfp-container {
		display: inline-block;
		position: relative;
	}
	.big .pfp {
		width: 4em;
		height: 5em;
	}
	.pfp {
		width: 3em;
		height: 3.75em;

		background-color: black;
		border: solid 2px pink;
		border-bottom-width: 1px;
	}

	.loading {
		animation: spin 0.5s linear infinite;
		filter: saturate(0) brightness(1.5);
	}
	@keyframes spin {
		from {transform: rotate(0turn);}
		to {transform: rotate(1turn);}
	}

	.online {
		display: inline-block;

		width: 1em;
		height: 1em;
		border-radius: 100%;

		background-color: limegreen;

		position: absolute;
		bottom: -0.2em;
		right: -0.2em;
		z-index: 1;
	}
	.big .online {
		width: 1.25em;
		height: 1.25em;
	}
</style>
