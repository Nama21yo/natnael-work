<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "Thomas Tsoru",
			slug: "thomas-tsoru",
			href: "https://github.com/dice-research"
		},
		{
			label: "Wikidata UI",
			slug: "wikidata-ui",
			href: "https://www.wikidata.org"
		},
		{
			label: "LangGraph",
			slug: "langgraph",
			href: "https://langchain-ai.github.io/langgraph/"
		},
		{
			label: "Afro-XLM-R",
			slug: "afro-xlmr",
			href: "https://huggingface.co/Davlan/afro-xlmr-base"
		},
		{
			label: "DBpedia Mappings",
			slug: "dbpedia-mappings",
			href: "https://mappings.dbpedia.org/"
		}
	];

	const mindMapNodes = [
		{ label: "Thomas Tsoru", slug: "thomas-tsoru", angle: 270 },
		{ label: "Wikidata", slug: "wikidata-ui", angle: 342 },
		{ label: "LangGraph", slug: "langgraph", angle: 54 },
		{ label: "Afro-XLM-R", slug: "afro-xlmr", angle: 126 },
		{ label: "Mappings", slug: "dbpedia-mappings", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 4
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			A split week — navigating final exams while still making progress on templates, the
			website's data display, and the first serious look at which multilingual models could power
			the mapping pipeline.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-4</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#deployment</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#mappings</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#open-source-models</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					4
				</span>
				Jun 12, 2026 – Jun 19, 2026
			</h2>
			<p class="mt-5">
				Week 4 landed in the middle of a demanding exam period, so I had to split time between
				coursework and the project. Even with less time available, it turned out to be one of the
				more important weeks: I had a real conversation about deployment, kept the ontology mapping
				work moving, redesigned the website's data display for a clearer information hierarchy, and
				started seriously looking into which multilingual models could power the pipeline.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Deployment discussions
			</h2>
			<p class="mt-5">
				I met with Thomas Tsoru, a DBpedia maintainer at DICE Research, to plan deploying the static
				Amharic DBpedia website. Up to this point the site had only ever run on localhost and in
				GitHub Actions preview builds. We talked through what it would actually take to get it on a
				dedicated domain — hosting requirements, who owns the DNS configuration.
			</p>
			<p class="mt-4">
				A stable public URL matters beyond looks: it's what lets Amharic-speaking contributors
				actually find and use the site, lets external links in Wikipedia articles point somewhere
				real, and gives the project an identity that survives a GitHub username change. This was the
				first concrete step toward making the work public instead of just technically done.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Templates, wiki pages, and mappings
			</h2>
			<p class="mt-5">
				I built three new Amharic infobox templates, each paired with a real Amharic Wikipedia page
				and a DBpedia mapping entry — same pattern as Week 2: build the template, verify it on a
				real article, register the mapping so the extraction framework can process it. Every mapped
				template is a direct contribution to the Amharic DBpedia graph, since the mapping is what
				turns a Wikipedia infobox into structured Linked Data triples. It's unglamorous work, but it
				compounds: each mapping adds to the previous ones and slowly grows the share of Amharic
				Wikipedia content DBpedia can actually represent.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Refactoring the website literals UI
			</h2>
			<p class="mt-5">
				I redesigned how statistics and literal values show up on the website. Before, it was just
				raw numbers — a triple count, an entity count — with no explanation of what they meant.
				Looking at how Wikidata presents its property values gave me a clearer model: show the value
				with context, so it's obvious what a number refers to and why it matters.
			</p>
			<p class="mt-4">
				Now every statistic is clickable and shows a tooltip explaining what the number actually
				means — hover over "6,412 triples" and you get "RDF triples extracted from the latest
				Amharic Wikipedia dump — each triple is a subject–predicate–object statement about a
				real-world entity." Small change to implement, but it makes a real difference for visitors
				who are curious about the project but don't know knowledge-graph terminology.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Exploring multilingual open-source models
			</h2>
			<p class="mt-5">
				My longer-term goal is a pipeline that can automatically suggest DBpedia ontology mappings
				for Amharic infobox fields, which means I need a model that understands Amharic text and can
				relate it to English-language ontology labels. This week I started searching systematically
				for open-source models with those capabilities.
			</p>
			<p class="mt-4">
				The criteria were clear: it has to support Amharic (a low-resource language in the Ge'ez
				script), produce embeddings or classifications that work cross-lingually (DBpedia property
				labels are in English), and be small enough to run without a proprietary API. A few
				candidates came up — Afro-XLM-R, LaBSE, multilingual-e5, and others — that I'd formally
				evaluate in Week 5. This week was reading the papers, understanding how each was trained,
				and building a shortlist.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				LangGraph agent workflow
			</h2>
			<p class="mt-5">
				I implemented the first end-to-end agent workflow: Afro-XLM-R as the retriever feeding into
				a local LLM for property mapping. It follows a retrieve-then-reason pattern — Afro-XLM-R
				encodes the Amharic infobox field into a dense vector, retrieves the most semantically
				similar DBpedia property labels from a pre-built index, and hands the shortlist to the LLM,
				which picks the best match and explains why.
			</p>
			<p class="mt-4">
				Building this in LangGraph was worth it because its node-and-edge model makes it easy to add
				steps later — a translation node, a re-ranker node, a confidence-scoring node — without
				restructuring the whole pipeline. The first version was rough, but it connected the
				embedding layer to the LLM reasoning layer for the first time, which was the hardest
				architectural step to get past. Everything in Weeks 5–13 builds on this.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-4#${link.slug}`}
				title={link.label}
				reason="Concept reference from this note."
				variant="backlink"
			/>
		{/each}
	</div>

	<div class="mt-6 border-t border-foreground/10 pt-6">
		<p class="blog-label">Mind map</p>
		<div
			class="relative mt-4 h-64 overflow-hidden rounded-3xl bg-zinc-950 text-white shadow-[inset_0_0_20px_rgba(0,0,0,0.5)]"
		>
			<svg
				class="pointer-events-none absolute inset-0 h-full w-full"
				viewBox="0 0 260 256"
				preserveAspectRatio="xMidYMid meet"
			>
				<circle
					cx="130"
					cy="128"
					r="40"
					fill="none"
					stroke="rgba(34,211,238,0.2)"
					stroke-width="1"
					class="animate-ping"
					style="animation-duration: 3s;"
				/>
				{#each mindMapNodes as node (node.label)}
					{@const rad = (node.angle * Math.PI) / 180}
					{@const cx = 130 + 85 * Math.cos(rad)}
					{@const cy = 128 + 85 * Math.sin(rad)}
					<line
						x1="130"
						y1="128"
						x2={cx}
						y2={cy}
						stroke="rgba(34,211,238,0.25)"
						stroke-width="1.5"
						stroke-dasharray="4 6"
					/>
				{/each}
			</svg>

			<div class="pointer-events-none absolute inset-0 flex items-center justify-center">
				<div
					class="flex h-16 w-16 items-center justify-center rounded-full border-2 border-cyan/40 bg-zinc-900/80 text-center text-[10px] font-black text-cyan shadow-[0_0_15px_rgba(34,211,238,0.25)] backdrop-blur-md"
				>
					WEEK 4
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-4#${node.slug}`}
					class="absolute flex h-[54px] w-[54px] -translate-x-1/2 -translate-y-1/2 items-center justify-center rounded-full border border-white/10 bg-zinc-900/90 p-1.5 text-center text-[8px] leading-tight font-bold text-white/80 shadow-lg backdrop-blur-sm transition-all duration-300 hover:z-20 hover:scale-125 hover:border-cyan hover:bg-cyan/10 hover:text-cyan hover:shadow-[0_0_20px_rgba(34,211,238,0.4)]"
					style={`left: ${(cx / 260) * 100}%; top: ${(cy / 256) * 100}%;`}
					aria-label={node.label}
					title={node.label}
				>
					<span class="line-clamp-3">{node.label}</span>
				</a>
			{/each}
		</div>
	</div>
</aside>
