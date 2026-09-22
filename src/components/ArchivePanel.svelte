<script lang="ts">
import { onMount } from "svelte";
import { getPostUrlBySlug } from "../utils/url-utils";

export let tags: string[];
export let categories: string[];
export let sortedPosts: Post[] = [];

interface Post {
	slug: string;
	data: { title: string; tags: string[]; category?: string; published: Date };
}

const unitTypes = ["IRIS", "Contest", "Project", "Private Study"] as const;
type UnitType = (typeof unitTypes)[number];

let archivePosts: Post[] = [];
const years = [2026, 2025];
let activeYear = 2026;
let activeType: "all" | UnitType = "all";
let query = "";

const params = new URLSearchParams(window.location.search);
tags = params.has("tag") ? params.getAll("tag") : [];
categories = params.has("category") ? params.getAll("category") : [];
const uncategorized = params.get("uncategorized");

onMount(() => {
	archivePosts = sortedPosts.filter((post) => {
		if (tags.length && !post.data.tags?.some((tag) => tags.includes(tag))) return false;
		if (categories.length && (!post.data.category || !categories.includes(post.data.category))) return false;
		if (uncategorized && post.data.category) return false;
		return true;
	});
});

$: needle = query.trim().toLowerCase();
$: tracks = archivePosts.filter((post) => {
	const inYear = post.data.published.getFullYear() === activeYear;
	const inType = activeType === "all" || post.data.category?.toLowerCase() === activeType.toLowerCase();
	const text = [post.data.title, post.data.category ?? "", ...(post.data.tags ?? [])].join(" ").toLowerCase();
	return inYear && inType && (!needle || text.includes(needle));
});
$: sideLabel = activeType === "all" ? `SESSION ${activeYear}` : `${activeType.toUpperCase()} · ${activeYear}`;

function switchYear() {
	activeYear = activeYear === 2026 ? 2025 : 2026;
}

function shortDate(date: Date) {
	return new Intl.DateTimeFormat("en", { month: "2-digit", day: "2-digit" }).format(date);
}
</script>

<section class="deck card-base overflow-hidden">
	<div class="topbar">
		<div class="brand"><span class="live-dot"></span><strong>ARCHIVE.FM</strong><small>ON AIR</small></div>
		<div class="frequency">EST. 2025 <i></i> FWR 101.7</div>
		<div class="counter"><span>{String(tracks.length).padStart(2, "0")}</span> UNITS</div>
	</div>

	<div class="year-dial" aria-label="Choose archive year">
		{#each years as year}
			<button class:active={activeYear === year} on:click={() => activeYear = year}><span>{String(year).slice(2)}</span> {year}</button>
		{/each}
	</div>

	<nav class="unit-types" aria-label="Filter units by category">
		<span>UNIT TYPE</span>
		<button class:active={activeType === "all"} on:click={() => activeType = "all"}>All Units</button>
		{#each unitTypes as type}
			<button class:active={activeType === type} on:click={() => activeType = type}>{type}</button>
		{/each}
	</nav>

	<div class="player">
		<div class="turntable">
			<div class="corner-label">SELECTED<br />RECORD</div>
			<div class="record-wrap">
				<button class="record" class:spinning={tracks.length > 0} on:click={switchYear} aria-label={`Switch to ${activeYear === 2026 ? 2025 : 2026} archive`}>
					<div class="groove groove-one"></div><div class="groove groove-two"></div>
					<div class="record-label">
						<small>FUWARI</small>
						<strong>{activeYear}</strong>
						<span>{tracks.length} UNITS</span>
					</div>
					<div class="spindle"></div>
				</button>
				<div class="tonearm"><i></i><b></b></div>
			</div>
			<div class="now-playing">
				<div class="equalizer" aria-hidden="true"><i></i><i></i><i></i><i></i><i></i></div>
				<div><small>NOW BROWSING</small><strong>{sideLabel}</strong></div>
			</div>
		</div>

		<div class="track-panel">
			<header class="track-header">
				<div><span>UNIT COLLECTION</span><h1>Units<span>.</span></h1></div>
				<label class="search">
					<span>⌕</span><input type="search" bind:value={query} placeholder="Find a track" aria-label="Search posts" />
					{#if query}<button on:click={() => query = ""} aria-label="Clear search">×</button>{/if}
				</label>
			</header>

			<div class="column-labels"><span>NO.</span><span>TITLE / GENRE</span><span>DATE</span><span></span></div>
			<div class="tracks">
				{#each tracks as post, index}
					<a class="track" href={getPostUrlBySlug(post.slug)}>
						<span class="track-no">{String(index + 1).padStart(2, "0")}</span>
						<div class="track-info">
							<strong>{post.data.title}</strong>
							<div><span>{post.data.category ?? "MISC"}</span>{#each (post.data.tags ?? []).slice(0, 2) as tag}<small>#{tag}</small>{/each}</div>
						</div>
						<time datetime={post.data.published.toISOString()}>{shortDate(post.data.published)}</time>
						<span class="play"><i></i></span>
					</a>
				{/each}
				{#if tracks.length === 0}
					<div class="empty"><span>NO SIGNAL</span><strong>No units found in this selection.</strong><button on:click={() => { query = ""; activeType = "all"; }}>RESET FILTER</button></div>
				{/if}
			</div>
		</div>
	</div>

	<div class="ticker" aria-hidden="true">
		<div>FUWARI ARCHIVE <span>✦</span> STORIES ON REPEAT <span>✦</span> KEEP THE GOOD PARTS <span>✦</span> FUWARI ARCHIVE <span>✦</span> STORIES ON REPEAT <span>✦</span> KEEP THE GOOD PARTS <span>✦</span></div>
	</div>
</section>

<style>
	.deck { --line: color-mix(in oklab, var(--text-30) 19%, transparent); border: 1px solid var(--line); background: color-mix(in oklab, var(--card-bg) 96%, var(--primary)); }
	.topbar { display: grid; grid-template-columns: 1fr auto 1fr; align-items: center; min-height: 4rem; padding: 0 1.5rem; border-bottom: 1px solid var(--line); }
	.brand { display: flex; align-items: center; gap: .55rem; color: var(--text-90); }
	.brand strong { font-size: .76rem; letter-spacing: .08em; }
	.brand small { padding: .18rem .4rem; border: 1px solid color-mix(in oklab, var(--primary) 45%, transparent); border-radius: .25rem; color: var(--primary); font-size: .42rem; font-weight: 900; letter-spacing: .12em; }
	.live-dot { width: .45rem; height: .45rem; border-radius: 50%; background: var(--primary); box-shadow: 0 0 0 .3rem color-mix(in oklab, var(--primary) 12%, transparent); animation: pulse 1.8s ease-in-out infinite; }
	.frequency { display: flex; align-items: center; gap: .7rem; color: var(--text-30); font-size: .51rem; font-weight: 800; letter-spacing: .13em; }
	.frequency i { width: 2.5rem; height: 1px; background: var(--line); }
	.counter { justify-self: end; color: var(--text-30); font-size: .52rem; font-weight: 800; letter-spacing: .1em; }
	.counter span { margin-right: .3rem; color: var(--primary); font-size: .8rem; }
	.year-dial { display: flex; gap: 0; overflow-x: auto; border-bottom: 1px solid var(--line); scrollbar-width: none; }
	.year-dial::-webkit-scrollbar { display: none; }
	.year-dial button { position: relative; min-width: 7.2rem; height: 4.1rem; padding: 0 1rem; border: 0; border-right: 1px solid var(--line); background: transparent; color: var(--text-30); cursor: pointer; font-size: .52rem; font-weight: 850; letter-spacing: .09em; transition: .25s ease; }
	.year-dial button span { display: block; margin-bottom: .15rem; color: var(--text-75); font-size: 1.05rem; letter-spacing: -.03em; }
	.year-dial button::after { content: ""; position: absolute; right: 50%; bottom: 0; left: 50%; height: 3px; background: var(--primary); transition: .25s ease; }
	.year-dial button:hover { background: color-mix(in oklab, var(--primary) 6%, transparent); color: var(--primary); }
	.year-dial button.active { background: color-mix(in oklab, var(--primary) 10%, transparent); color: var(--primary); }
	.year-dial button.active span { color: var(--primary); }
	.year-dial button.active::after { right: .75rem; left: .75rem; }
	.unit-types { display: flex; align-items: center; gap: .4rem; overflow-x: auto; padding: .7rem 1rem; border-bottom: 1px solid var(--line); background: color-mix(in oklab, var(--text-30) 3%, transparent); scrollbar-width: none; }
	.unit-types::-webkit-scrollbar { display: none; }
	.unit-types > span { flex: 0 0 auto; margin-right: .45rem; color: var(--text-30); font-size: .44rem; font-weight: 900; letter-spacing: .16em; }
	.unit-types button { flex: 0 0 auto; padding: .45rem .75rem; border: 1px solid transparent; border-radius: 999px; background: transparent; color: var(--text-30); cursor: pointer; font-size: .52rem; font-weight: 800; letter-spacing: .04em; transition: .2s ease; }
	.unit-types button:hover { border-color: color-mix(in oklab, var(--primary) 28%, transparent); color: var(--primary); }
	.unit-types button.active { border-color: color-mix(in oklab, var(--primary) 45%, transparent); background: color-mix(in oklab, var(--primary) 11%, transparent); color: var(--primary); }
	.player { display: grid; grid-template-columns: minmax(20rem, .87fr) minmax(25rem, 1.13fr); min-height: 40rem; }
	.turntable { position: relative; display: flex; align-items: center; flex-direction: column; justify-content: center; min-width: 0; overflow: hidden; border-right: 1px solid var(--line); background-image: linear-gradient(var(--line) 1px, transparent 1px), linear-gradient(90deg, var(--line) 1px, transparent 1px); background-size: 3.5rem 3.5rem; }
	.turntable::after { content: ""; position: absolute; inset: 0; background: radial-gradient(circle at center, transparent 20%, color-mix(in oklab, var(--card-bg) 70%, transparent) 75%); pointer-events: none; }
	.corner-label { position: absolute; z-index: 3; left: 1.25rem; top: 1.2rem; color: var(--text-30); font-size: .48rem; font-weight: 850; line-height: 1.5; letter-spacing: .14em; }
	.record-wrap { position: relative; z-index: 2; width: min(78%, 22rem); aspect-ratio: 1; }
	.record { position: absolute; inset: 0; display: grid; place-items: center; padding: 0; border: 0; border-radius: 50%; appearance: none; background: repeating-radial-gradient(circle, color-mix(in oklab, var(--text-90) 87%, black) 0 2px, color-mix(in oklab, var(--text-90) 75%, black) 3px 4px); box-shadow: 0 1.5rem 3rem color-mix(in oklab, black 30%, transparent), inset 0 0 0 1px color-mix(in oklab, white 15%, transparent); cursor: pointer; }
	.record:focus-visible { outline: .25rem solid color-mix(in oklab, var(--primary) 55%, transparent); outline-offset: .35rem; }
	.record::before { content: ""; position: absolute; inset: 3%; border: 1px dashed color-mix(in oklab, white 13%, transparent); border-radius: 50%; }
	.record.spinning { animation: spin 18s linear infinite; }
	.groove { position: absolute; border: 1px solid color-mix(in oklab, white 12%, transparent); border-radius: 50%; }
	.groove-one { inset: 15%; }
	.groove-two { inset: 26%; }
	.record-label { position: relative; display: flex; align-items: center; flex-direction: column; justify-content: center; width: 38%; aspect-ratio: 1; border-radius: 50%; background: var(--primary); color: var(--card-bg); text-align: center; box-shadow: inset 0 0 0 .35rem color-mix(in oklab, var(--card-bg) 13%, transparent); }
	.record-label small { font-size: .43rem; font-weight: 900; letter-spacing: .18em; }
	.record-label strong { font-size: clamp(1.2rem, 4vw, 2rem); line-height: 1.2; letter-spacing: -.06em; }
	.record-label span { font-size: .4rem; font-weight: 850; letter-spacing: .12em; }
	.spindle { position: absolute; width: .65rem; height: .65rem; border: .15rem solid color-mix(in oklab, var(--card-bg) 80%, white); border-radius: 50%; background: var(--text-90); }
	.tonearm { position: absolute; z-index: 4; right: -5%; top: 2%; width: 22%; height: 63%; border-right: .35rem solid color-mix(in oklab, var(--text-50) 55%, silver); border-radius: 0 999px 999px 0; transform: rotate(-15deg); transform-origin: top right; filter: drop-shadow(.3rem .5rem .35rem color-mix(in oklab, black 25%, transparent)); }
	.tonearm i { position: absolute; right: -.75rem; top: -.6rem; width: 1.25rem; height: 1.25rem; border: .25rem solid var(--text-30); border-radius: 50%; background: var(--card-bg); }
	.tonearm b { position: absolute; right: -.85rem; bottom: -1.2rem; width: 1.35rem; height: 2rem; border-radius: .25rem; background: var(--primary); transform: rotate(9deg); }
	.now-playing { position: relative; z-index: 4; display: flex; align-items: center; align-self: stretch; gap: .9rem; margin: 2rem 2.2rem 0; padding-top: 1rem; border-top: 1px solid var(--line); }
	.now-playing small { display: block; color: var(--text-30); font-size: .43rem; font-weight: 850; letter-spacing: .16em; }
	.now-playing strong { color: var(--text-75); font-size: .7rem; letter-spacing: .06em; }
	.equalizer { display: flex; align-items: end; gap: 2px; height: 1rem; }
	.equalizer i { width: 2px; height: 35%; background: var(--primary); animation: bounce .8s ease-in-out infinite alternate; }
	.equalizer i:nth-child(2) { animation-delay: -.4s; }.equalizer i:nth-child(3) { animation-delay: -.2s; }.equalizer i:nth-child(4) { animation-delay: -.6s; }.equalizer i:nth-child(5) { animation-delay: -.1s; }
	.track-panel { min-width: 0; padding: 2.5rem 2.2rem 2rem; background: color-mix(in oklab, var(--card-bg) 98%, var(--text-90)); }
	.track-header { display: flex; align-items: end; justify-content: space-between; gap: 1rem; margin-bottom: 2rem; }
	.track-header > div > span { color: var(--primary); font-size: .5rem; font-weight: 900; letter-spacing: .17em; }
	h1 { margin: .3rem 0 0; color: var(--text-90); font-size: clamp(2.5rem, 6vw, 4.2rem); line-height: .9; letter-spacing: -.07em; }
	h1 span { color: var(--primary); }
	.search { display: flex; align-items: center; width: 12rem; height: 2.5rem; padding: 0 .75rem; border: 1px solid var(--line); border-radius: .55rem; color: var(--text-30); transition: border-color .2s ease; }
	.search:focus-within { border-color: color-mix(in oklab, var(--primary) 55%, transparent); }
	.search > span { color: var(--primary); font-size: 1.1rem; }
	.search input { width: 100%; min-width: 0; padding: 0 .45rem; border: 0; outline: 0; background: transparent; color: var(--text-75); font-size: .65rem; }
	.search input::placeholder { color: var(--text-30); }
	.search button { border: 0; background: transparent; color: var(--text-30); cursor: pointer; font-size: 1rem; }
	.column-labels { display: grid; grid-template-columns: 2.5rem minmax(0, 1fr) 3rem 2.1rem; gap: .6rem; padding: 0 .65rem .55rem; border-bottom: 1px solid var(--line); color: var(--text-30); font-size: .43rem; font-weight: 850; letter-spacing: .13em; }
	.tracks { max-height: 29rem; overflow-y: auto; padding-right: .25rem; scrollbar-color: color-mix(in oklab, var(--primary) 35%, transparent) transparent; scrollbar-width: thin; }
	.track { position: relative; display: grid; grid-template-columns: 2.5rem minmax(0, 1fr) 3rem 2.1rem; align-items: center; gap: .6rem; min-height: 4.65rem; padding: .55rem .65rem; border-bottom: 1px solid var(--line); color: inherit; text-decoration: none; transition: background .22s ease, transform .22s ease; }
	.track::before { content: ""; position: absolute; left: 0; width: 3px; height: 0; background: var(--primary); transition: height .22s ease; }
	.track:hover { z-index: 1; background: color-mix(in oklab, var(--primary) 7%, transparent); transform: translateX(.3rem); }
	.track:hover::before { height: 60%; }
	.track-no { color: var(--text-30); font-family: Georgia, serif; font-size: .78rem; font-style: italic; }
	.track-info { min-width: 0; }
	.track-info strong { display: block; overflow: hidden; color: var(--text-75); font-size: .77rem; line-height: 1.3; text-overflow: ellipsis; white-space: nowrap; transition: color .2s ease; }
	.track:hover .track-info strong { color: var(--primary); }
	.track-info div { display: flex; gap: .45rem; margin-top: .3rem; overflow: hidden; color: var(--text-30); font-size: .48rem; white-space: nowrap; }
	.track-info div > span { color: var(--primary); font-weight: 800; letter-spacing: .05em; }
	.track-info small { font-size: inherit; }
	time { color: var(--text-30); font-size: .5rem; font-weight: 700; }
	.play { display: grid; place-items: center; width: 1.75rem; height: 1.75rem; border: 1px solid var(--line); border-radius: 50%; transition: .2s ease; }
	.play i { width: 0; height: 0; margin-left: .12rem; border-top: .25rem solid transparent; border-bottom: .25rem solid transparent; border-left: .38rem solid var(--text-30); transition: .2s ease; }
	.track:hover .play { border-color: var(--primary); background: var(--primary); transform: rotate(360deg); }
	.track:hover .play i { border-left-color: var(--card-bg); }
	.empty { display: flex; align-items: center; flex-direction: column; gap: .7rem; padding: 5rem 1rem; text-align: center; }
	.empty > span { color: var(--primary); font-size: .5rem; font-weight: 900; letter-spacing: .2em; }
	.empty strong { color: var(--text-75); font-size: .8rem; }
	.empty button { padding: .5rem .8rem; border: 1px solid var(--primary); border-radius: .35rem; background: transparent; color: var(--primary); cursor: pointer; font-size: .5rem; font-weight: 850; letter-spacing: .12em; }
	.ticker { overflow: hidden; padding: .65rem 0; border-top: 1px solid var(--line); background: var(--primary); color: var(--card-bg); font-size: .5rem; font-weight: 900; letter-spacing: .14em; white-space: nowrap; }
	.ticker div { width: max-content; animation: marquee 24s linear infinite; }
	.ticker span { padding: 0 1.5rem; }
	@keyframes spin { to { transform: rotate(360deg); } }
	@keyframes bounce { to { height: 100%; } }
	@keyframes pulse { 50% { opacity: .45; box-shadow: 0 0 0 .5rem color-mix(in oklab, var(--primary) 5%, transparent); } }
	@keyframes marquee { to { transform: translateX(-50%); } }
	@media (prefers-reduced-motion: reduce) { .record, .ticker div, .equalizer i, .live-dot { animation: none !important; } }
	@media (max-width: 850px) {
		.topbar { grid-template-columns: 1fr auto; }.frequency { display: none; }
		.player { grid-template-columns: 1fr; }
		.turntable { min-height: 32rem; border-right: 0; border-bottom: 1px solid var(--line); }
		.record-wrap { width: min(70%, 20rem); }
		.track-panel { padding: 2rem 1rem 1.5rem; }
	}
	@media (max-width: 520px) {
		.topbar { padding: 0 .9rem; }.brand small { display: none; }
		.year-dial button { min-width: 5.5rem; }
		.turntable { min-height: 26rem; }
		.record-wrap { width: min(72%, 17rem); }
		.now-playing { margin: 1.5rem 1rem 0; }
		.track-header { align-items: stretch; flex-direction: column; }
		.search { width: 100%; }
		.track-info div small { display: none; }
	}
</style>
