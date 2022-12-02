<!-- The profile page, AKA the change your profile picture page. -->

<script>
	import PFP from "../lib/PFP.svelte";
	import Container from "../lib/Container.svelte";
	import * as clm from "../lib/clmanager.js";
	import {user, profileData} from "../lib/stores.js";

	let _user = $user;
	user.subscribe(v => _user = v);

	const pfps = new Array(23).fill().map((v,i) => i+1)
	
	/**
	 * Saves the user profile, and also clears its cache entry.
	 */
	function save() {
		if ($profileData[$user.name]) {
			const _profileData = $profileData;
			delete _profileData[$user.name];
			profileData.set(_profileData);
		}

		clm.updateProfile();
	}
</script>

<div class="profile">
	<Container>
		<div class="profile-header">
			<PFP
				online={false}
				big={true}
			></PFP>
			<h1 class="profile-username">{_user.name}</h1>
		</div>
	</Container>
	<Container>
		<h2>Change nothing</h2>
		<p>You cannot change your profile picture</p>
		<p>(You can still press the buttons, but you cant actually change it.)</p>
		<div id="pfp-list">
			{#each pfps as pfp}
				<span
					class="pfp"
					class:selected={_user.pfp_data === pfp}
				><PFP
						online={false}
						icon={pfp}
						alt="Whats 9 + 10? {pfp}"
					></PFP></span>
			{/each}
		</div>
	</Container>
</div>

<style>
	.pfp {
		padding: 0.2em;
		margin: 0.2em;
		border-radius: 1.25em;
		display: inline-block;
	}
	.pfp:hover {
		background-color: var(--orange-light);
	}
	.pfp.selected {
		background-color: var(--orange);
	}
	#pfp-list {
		display: flex;
		flex-wrap: wrap;
	}

	.profile-username {
		margin: 0;
		margin-left: 0.2em;
		display: inline-block;
		max-width: 100%;
		font-size: 300%;
	}

	.profile-header {
		display: flex;
		align-items: center;
		flex-wrap: wrap;
	}
</style>