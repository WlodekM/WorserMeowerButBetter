<!--
	The home page!
	It features live post updates and a load more button which is pretty nice.
-->

<script>
	import {user, ulist, spinner} from "../lib/stores.js";
	import Post from "../lib/Post.svelte";
	import Container from "../lib/Container.svelte";
	import Loading from "../lib/Loading.svelte";
	import {link} from "../lib/clmanager.js";
	import {apiUrl, encodeApiURLParams} from "../lib/urls.js";

	import {fly} from "svelte/transition";
	import {flip} from 'svelte/animate';

	let id = 0;
	export let posts = [];

	let pagesLoaded = 0;
	let pageLoading = false;
	let numPages = null;
	let postErrors = "";

	// As we use a Load More button and the home is sorted newest-first,
	// we need an offset for posts to be continuous.
	let postOffset = 0;
	
	/**
	 * Loads a page, with offset and overflow calculations.
	 * 
	 * @param {number} [page] The page to load. If not present, simply clears the posts.
	 * @returns {Promise<array>} The posts array.
	 */
	
	const filter = {
		" ":"  ",
		"\n":\n,
		":BELL:":"🕭",
		":G1:":"░",
		":G2:":"▒",
		":G3:":"▓",
		":MAZE:":"🐭\n┃┣━━━┳━━━━┳━━┓\n┃┗┓┏┛┃╻╺━━┛╺┓┃\n┣┓┃┗┓┗┻━━━┳╸┃┃\n┃┃┣╸┣━━┳━┓┗━┛┃\n┃┃┃┏┛┏╸┃╻┣━┳╸┃\n┃┗━┫╻┣━━┫┗╸┃┏┫\n┃┏━┫┃┃╺┓┗━┓┃┃┃\n┃┃┃┃┃┗┓┗━┓┗┻╸┃\n┗━┫┏┻━━━━┻━━━┛\n　🧀",
		"CLIPPY:":"░░░░░░▄████▄\n░░░░░▐▌░░░░▐▌\n░░▄▀▀█▀░░░░▐▌\n░░▄░▐▄░░░░░▐▌▀▀▄\n▐▀░▄▄░▀▌░▄▀▀░▀▄░▀\n▐░▀██▀░▌▐░▄██▄░▌\n░▀▄░▄▄▀░▐░░▀▀░░▌\n░░░░█░░░░▀▄▄░▄▀\n░░░░█░█░░░░█░▐\n░░░░█░█░░░▐▌░█\n░░░░█░█░░░▐▌░█\n░░░░▐▌▐▌░░░█░█\n░░░░▐▌░█▄░▐▌░█\n░░░░░█░░▀▀▀░░▐▌\n░░░░░▐▌░░░░░░█\n░░░░░░█▄░░░░▄█\n░░░░░░░▀████▀",
		":UP:":"🖒",
		":+1:":"🖒",
		":DOWN:":"🖓",
		":-1:":"🖓",
		"B":"👌︎",
		"M":"N",
		"I":"l",
		":":";",
		"?":"/",
		",":".",
		"+":"=",
		"|":"I",
		"!":"?",
		"@":"#",
		"$":"%",
		"^":"&",
		"*":"(",
		")":"(",
		"L":"I",
		"A":"∀"
	}
	function applyFilters(text,filter) {
	    let result = text
	    for (const key in filter) {
		if (Object.hasOwnProperty.call(filter, key)) {
		    const CurrentFilter = filter[key];
		    result = result.replaceAll(key,CurrentFilter)
	    	    console.log(`REPLACING: ${key} - ${CurrentFilter}`)
		}
	    }
	    console.log(`${text} - ${result}`)
		
	    return(result)
	}
	applyFilters("BB",filter)
	async function loadPage(page) {
		pageLoading = true;
		if (page === undefined) {
			posts = [];
		} else {
			// 25 posts per page...
			let realPage = page + Math.floor(postOffset / 25);
			let realOffset = postOffset % 25;

			try {
				let path = `home?autoget&page=`;
				if (encodeApiURLParams) path = encodeURIComponent(path);
				const resp = await fetch(
					`${apiUrl}${path}${realPage}`
				);
				if (!resp.ok) {
					throw new Error("Response code is not OK; code is " + resp.status);
				}
				const json = await resp.json();

				let realPosts = json.autoget;
				realPosts.splice(0, realOffset);

				numPages = json.pages;

				let overflowResp, overflowJson;
				if (realOffset > 0 && pagesLoaded < numPages) {
					overflowResp = await fetch(
						`${apiUrl}${path}${realPage+1}`
					);
					if (!resp.ok) {
						throw new Error("Overflow response code is not OK; code is " + resp.status);
					}
					overflowJson = await overflowResp.json();

					realPosts = realPosts.concat(
						overflowJson.autoget.slice(
							0, realOffset
						)
					);
				}

				for (const post of realPosts) {
					posts.push({
						id: id++,
						post_id: post.post_id,
						user: post.u,
						content: post.p,
						date: post.t.e,
					});
				}
				pagesLoaded = page;
			} catch(e) {
				pageLoading = false;
				throw e;
			}
		}
		posts = posts;
		pageLoading = false;
		return posts;
	}

	/**
	 * Adds a post to the list.
	 * 
	 * @param {object} post
	 */
	function addPost(post) {
		posts.unshift({
			id: id++,
			...post
		});
		posts = posts;
	}

	function addad() {
		posts.unshift({
			id: id++,
			ad: "ad"
		});
		posts = posts;
	}

	/**
	 * Adds events to listen for live post updates.
	 */
	function listenOnLink() {
		link.on("direct", cmd => {
			if (!(cmd.val.post_origin === "home")) return;
			addPost({
				post_id: cmd.val.post_id,
				user: cmd.val.u,
				content: cmd.val.p,
				date: cmd.val.t.e,
			});
			postOffset++;
			if (postOffset % 5 == 0) {
				addad();
			}
			//TODO: Add comic sans to worsemeower
			posts = posts;
		});
	}
	if (link.ws.readyState === 1) {
		listenOnLink();
	} else {
		link.ws.addEventListener("open", listenOnLink);
	}

	let _ulist = $ulist;
	ulist.subscribe(val => {
		_ulist = val;
	})
</script>

<div class="home">
	{#await loadPage(1)}
		<div class="fullcenter">
			<Loading />
		</div>
	{:then}
		<Container>
			<div>
			I would put some fairytales here but nah
			<br /><br />
			Have a <a href="https://t.ly/xzLR">Fifty ways to die in minecraft fairytale</a>
			<br /><br />
			Anyways enjoy meower
			<br /><br />
			Why are there {_ulist.length} people online? hm............ AAAAAAAAA{#if _ulist.length}{_ulist.join(", AA, ")}{/if}.
			</div>
		</Container>
		{#if $user.name}
			<form 
				id="createpost"
				on:submit|preventDefault={e => {
					postErrors = "";
					if (!e.target[0].value.trim()) {
						postErrors = "Hi :)))))) I'm from the CCP and I'm here to tell you that your post is illegal 🔫🔫🔫";
						return false;
					};

					spinner.set(true);
					
					link.send({
						cmd: "direct",
						val: {
							cmd: "post_home",
							val: (applyFilters(e.target[0].value.substring(0, 50).toUpperCase(),filter) + "?????? (This post was made with https://rickroll.it/)"),
						},
						listener: "post_home",
					});
					const postListener = link.on("statuscode", cmd => {
						if (cmd.listener !== "post_home") return;
						link.off(postListener);
						spinner.set(false);

						if (cmd.val === "I:100 | OK") {
							e.target[0].value = "";
						} else if (cmd.val === "E:106 | Too many requests") {
							postErrors = "spammer >:C";
						} else {
							postErrors = "Unexpected " + cmd.val + " error!";
						}
					});
					return false;
				}}
			>
				<input
					type="text"
					class="white"
					placeholder="Posting costs $5"
					maxlength="4000"
				>
				<button>Send SID picture</button>
			</form>
			<div class="post-errors">{postErrors}</div>
		{/if}
		{#if posts.length < 1}
			{#if $user.name}
				No posts here. Check back later or be the first to post!
			{:else}
				No posts here. Check back later!
			{/if}
		{:else}
			{#each posts as post (post.id)}
				<div
					transition:fly|local="{{y: -50, duration: 250}}"
					animate:flip="{{duration: 250}}"
				>
					<Post post={post} />
				</div>
			{/each}
		{/if}
		<div class="center">
			{#if pageLoading}
				<Loading />
			{:else}
				{#if numPages && numPages > pagesLoaded}
					<button 
						class="load-more"
					>
						No more posts available :DDDDDDD
					</button>
				{/if}
			{/if}
		</div>
	{:catch error}
		<Container>
			<div>
			<h1>woops</h1>
			Error loading posts. Please try again.
			<pre><code>{error}</code></pre>
			</div>
		</Container>
	{/await}
</div>

<style>
	.home {
		height: 100%;
	}
	.center {
		text-align: left;
	}
	.fullcenter {
		text-align: center;
		display: flex;
		justify-content: center;
		align-items: center;

		width: 100vw;
		height: 100vh;

		position: fixed;
		top: 0;
		left: 0;
	}

	.post-errors {
		color: pink;
		font-size: 75%;
		font-weight: bold;
		margin: 0.25em 0;
	}
</style>
