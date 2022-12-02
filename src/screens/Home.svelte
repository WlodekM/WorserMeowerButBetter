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
			<h1>The place to chat, meower, a free social media platform for 5 year olds, really nice and cool, anyways, Once upon a time, there was a little girl named Goldilocks. She went for a walk in the forest. Pretty soon, she came upon a house. She knocked and, when no one answered, she walked right in.

				At the table in the kitchen, there were three bowls of porridge. Goldilocks was hungry. She tasted the porridge from the first bowl.
				
				"This porridge is too hot!" she exclaimed.
				
				So, she tasted the porridge from the second bowl.
				
				"This porridge is too cold," she said.
				
				So, she tasted the last bowl of porridge.
				
				"Ahhh, this porridge is just right," she said happily and she ate it all up.
				
				After she'd eaten the three bears' breakfasts, she decided she was feeling a little tired. So, she walked into the living room where she saw three chairs. Goldilocks sat in the first chair to rest.
				
				"This chair is too big!" she exclaimed.
				
				So she sat in the second chair.
				
				"This chair is too big, too!" she whined.
				
				So she tried the last and smallest chair.
				
				"Ahhh, this chair is just right," she sighed. But just as she settled down into the chair to rest, it broke into pieces!
				
				Goldilocks and the Three Bears: sleeping-goldilocksGoldilocks was very tired by this time, she went upstairs to the bedroom. She lay down in the first bed, but it was too hard. Then she lay in the second bed, but it was too soft. Then she lay down in the third bed and it was just right. Goldilocks fell asleep.
				
				As she was sleeping, the three bears came home.
				
				"Someone's been eating my porridge," growled the Papa bear.
				
				"Someone's been eating my porridge," said the Mama bear.
				
				"Someone's been eating my porridge and they ate it all up!" cried the Baby bear.
				
				"Someone's been sitting in my chair," growled the Papa bear.
				
				"Someone's been sitting in my chair," said the Mama bear.
				
				"Someone's been sitting in my chair and they've broken it to pieces," cried the Baby bear.
				
				They decided to look around some more and when they got upstairs to the bedroom, Papa bear growled,
				
				"Someone's been sleeping in my bed.”
				
				"Someone's been sleeping in my bed, too" said the Mama bear.
				
				"Someone's been sleeping in my bed and she's still there!" exclaimed the Baby bear.
				
				Just then, Goldilocks woke up. She saw the three bears. She screamed, "Help!" And she jumped up and ran out of the room. Goldilocks ran down the stairs, opened the door, and ran away into the forest. She never returned to the home of the three bears.
				
				Once upon a time there was an old mother pig who had three little pigs and not enough food to feed them. So when they were old enough, she sent them out into the world to seek their fortunes.

				The first little pig was very lazy. He didn't want to work at all and he built his house out of straw. The second little pig worked a little bit harder but he was somewhat lazy too and he built his house out of sticks. Then, they sang and danced and played together the rest of the day.

				The third little pig worked hard all day and built his house with bricks. It was a sturdy house complete with a fine fireplace and chimney. It looked like it could withstand the strongest winds.

				The next day, a wolf happened to pass by the lane where the three little pigs lived; and he saw the straw house, and he smelled the pig inside. He thought the pig would make a mighty fine meal and his mouth began to water.

				So he knocked on the door and said:

				Little pig! Little pig!
				Let me in! Let me in!
				But the little pig saw the wolf's big paws through the keyhole, so he answered back:

				No! No! No! 
				Not by the hairs on my chinny chin chin!
				Three Little Pigs, the straw houseThen the wolf showed his teeth and said:

				Then I'll huff 
				and I'll puff 
				and I'll blow your house down.
				So he huffed and he puffed and he blew the house down! The wolf opened his jaws very wide and bit down as hard as he could, but the first little pig escaped and ran away to hide with the second little pig.

				The wolf continued down the lane and he passed by the second house made of sticks; and he saw the house, and he smelled the pigs inside, and his mouth began to water as he thought about the fine dinner they would make.

				So he knocked on the door and said:

				Little pigs! Little pigs!
				Let me in! Let me in!
				But the little pigs saw the wolf's pointy ears through the keyhole, so they answered back:

				No! No! No!
				Not by the hairs on our chinny chin chin!
				So the wolf showed his teeth and said:

				Then I'll huff 
				and I'll puff 
				and I'll blow your house down!
				So he huffed and he puffed and he blew the house down! The wolf was greedy and he tried to catch both pigs at once, but he was too greedy and got neither! His big jaws clamped down on nothing but air and the two little pigs scrambled away as fast as their little hooves would carry them.

				The wolf chased them down the lane and he almost caught them. But they made it to the brick house and slammed the door closed before the wolf could catch them. The three little pigs they were very frightened, they knew the wolf wanted to eat them. And that was very, very true. The wolf hadn't eaten all day and he had worked up a large appetite chasing the pigs around and now he could smell all three of them inside and he knew that the three little pigs would make a lovely feast.

				Three Little Pigs at the Brick House

				So the wolf knocked on the door and said:

				Little pigs! Little pigs!
				Let me in! Let me in!
				But the little pigs saw the wolf's narrow eyes through the keyhole, so they answered back:

				No! No! No! 
				Not by the hairs on our chinny chin chin!
				So the wolf showed his teeth and said:

				Then I'll huff 
				and I'll puff 
				and I'll blow your house down.
				Well! he huffed and he puffed. He puffed and he huffed. And he huffed, huffed, and he puffed, puffed; but he could not blow the house down. At last, he was so out of breath that he couldn't huff and he couldn't puff anymore. So he stopped to rest and thought a bit.

				But this was too much. The wolf danced about with rage and swore he would come down the chimney and eat up the little pig for his supper. But while he was climbing on to the roof the little pig made up a blazing fire and put on a big pot full of water to boil. Then, just as the wolf was coming down the chimney, the little piggy pulled off the lid, and plop! in fell the wolf into the scalding water.

				So the little piggy put on the cover again, boiled the wolf up, and the three little pigs ate him for supper.
				<br>
				Ok im done with my rant on fairytales, now enjoy meower
			</h1>
			Why are there {_ulist.length} person online, theres: AAAAAAAAA{#if _ulist.length}{_ulist.join("AAAAAA")}{/if}.
		</Container>
		{#if $user.name}
			<form 
				id="createpost"
				on:submit|preventDefault={e => {
					postErrors = "";
					if (!e.target[0].value.trim()) {
						postErrors = "You cannot send an empty post!";
						return false;
					};

					spinner.set(true);

					link.send({
						cmd: "direct",
						val: {
							cmd: "post_home",
							val: e.target[0].value,
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
							postErrors = "You're posting too fast!";
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
					placeholder="Write something..."
					maxlength="250"
				>
				<button>Post</button>
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
						on:click={() => loadPage(pagesLoaded + 1)}
					>
						Load More
					</button>
				{/if}
			{/if}
		</div>
	{:catch error}
		<Container>
			<h1>Home</h1>
			Error loading posts. Please try again.
			<pre><code>{error}</code></pre>
		</Container>
	{/await}
</div>

<style>
	#createpost {
		display: flex;
		margin-bottom: 0.5em;
	}
	#createpost input {
		flex-grow: 1;
		margin-right: 0.25em;
	}
	.home {
		height: 100%;
	}
	.center {
		text-align: center;
	}
	.load-more {
		width: 100%;
		margin-bottom: 1.88em;
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
		color: red;
		font-size: 75%;
		font-weight: bold;
		margin: 0.25em 0;
	}
</style>