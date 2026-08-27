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

<div class="grid gap-5 lg:grid-cols-[17rem_minmax(0,1fr)_18rem]">
	<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
		<p class="blog-label">GSoC 2026</p>
		<div class="mt-4 space-y-1">
			<a
				href={`${base}/blog/gsoc/2026`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Overview
			</a>
			<a
				href={`${base}/blog/gsoc/2026/pre-coding`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Pre-Coding
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-1`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 1
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-2`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 2
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-3`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 3
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-4`}
				class="block rounded-xl bg-cyan/15 px-3 py-2 text-sm font-bold text-cyan"
			>
				Week 4
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-5`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 5
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-6`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 6
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-7`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 7
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-8`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 8
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-9`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 9
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-10`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 10
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-11`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 11
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-12`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 12
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-13`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 13
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-14`}
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 14
			</a>
		</div>
	</aside>

	<article class="obsidian-panel min-h-[42rem]">
		<div class="border-b border-foreground/10 pb-5">
			<p class="blog-label">gsoc-2026</p>
			<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
				GSoC 2026 Week 4: Deployment Planning, Mappings, and Model Exploration
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
					Week 4 arrived in the middle of a demanding exam period, which meant the work had to be
					split between academic obligations and the project. Despite that, it turned out to be one
					of the most directionally important weeks: a real conversation about deployment happened,
					the ontology mapping work continued, the website's data display was redesigned with a
					clearer information hierarchy, and the first serious investigation of which multilingual
					models could power the pipeline began.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
					Deployment discussions
				</h2>
				<p class="mt-5">
					This week included a meeting with Thomas Tsoru, a DBpedia maintainer at DICE Research, to
					plan the deployment of the static Amharic DBpedia website. Up until this point the website
					had only ever existed on localhost and in GitHub Actions preview builds. The conversation
					with Thomas shifted that — we discussed what it would actually take to have the project
					accessible at a dedicated domain name, what the hosting requirements were, and who would
					own the DNS configuration.
				</p>
				<p class="mt-4">
					Having a domain name matters for reasons beyond aesthetics. A stable, public URL means that
					Amharic-speaking contributors can actually find and use the site, that external links in
					Wikipedia articles can point somewhere real, and that the project has an identity that
					survives a GitHub username change. This conversation was the first concrete step toward
					making the work publicly accessible rather than just technically complete.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
					Templates, wiki pages, and mappings
				</h2>
				<p class="mt-5">
					Three new Amharic infobox templates were built this week, each paired with a corresponding
					Amharic Wikipedia page and a DBpedia mapping entry. The pattern — create the template,
					verify it on a real article, then register the mapping so the extraction framework can
					process it — continued the approach established in Week 2. The mapping entries are what
					turn a Wikipedia infobox into structured Linked Data triples, so every new template that
					gets mapped is a direct contribution to the Amharic DBpedia graph. The work is unglamorous
					but cumulative: each mapping compounds with previous ones, gradually increasing the
					fraction of Amharic Wikipedia content that DBpedia can represent.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
					Refactoring the website literals UI
				</h2>
				<p class="mt-5">
					The way statistics and literal values were displayed on the Amharic DBpedia website
					underwent a significant redesign this week. The previous approach showed raw numbers — a
					count of triples, a count of entities — without any explanation of what those numbers
					meant. Looking at how Wikidata structures its property values gave a clearer model: values
					are presented with context, and the interface makes it obvious what a number refers to and
					why it matters.
				</p>
				<p class="mt-4">
					The key improvement introduced this week is that statistics are now clickable and show a
					tooltip or popover explaining what the number actually means. A user hovering over "6,412
					triples" now sees a short explanation like "RDF triples extracted from the latest Amharic
					Wikipedia dump — each triple is a subject–predicate–object statement about a real-world
					entity." This is a small change in implementation terms but a large change in
					comprehensibility, especially for visitors who are curious about the project but unfamiliar
					with knowledge graph terminology.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
					Exploring multilingual open-source models
				</h2>
				<p class="mt-5">
					The longer-term goal of the project is to build a pipeline that can automatically suggest
					DBpedia ontology mappings for Amharic infobox fields. To do that, the system needs a model
					capable of understanding Amharic text and relating it to English-language ontology labels.
					This week marked the beginning of a systematic search for which open-source models have
					the capabilities needed.
				</p>
				<p class="mt-4">
					The criteria were clear: the model must support Amharic (a relatively low-resource language
					written in the Ge'ez script), must be able to produce embeddings or classifications that
					work cross-lingually (since DBpedia property labels are in English), and must be small
					enough to run without proprietary API access. Several candidates emerged — Afro-XLM-R,
					LaBSE, multilingual-e5, and others — and the formal evaluation of these would begin in
					Week 5. This week was about reading the papers, understanding the training approaches, and
					building a shortlist.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
					LangGraph agent workflow
				</h2>
				<p class="mt-5">
					The first end-to-end agent workflow was implemented this week: Afro-XLM-R as the retriever
					feeding into a local LLM for property mapping. The architecture follows the
					retrieve-then-reason pattern — Afro-XLM-R encodes the Amharic infobox field into a dense
					vector, retrieves the most semantically similar DBpedia property labels from a pre-built
					index, and passes the shortlist to the LLM, which selects the best match and explains its
					reasoning.
				</p>
				<p class="mt-4">
					Building this initial workflow in LangGraph was valuable because LangGraph's node-and-edge
					model makes it easy to add new steps — a translation node, a re-ranker node, a
					confidence-scoring node — without restructuring the whole pipeline. The first version was
					rough, but it connected the semantic embedding layer with the LLM reasoning layer for the
					first time, which is the hardest architectural step. Everything that follows in Weeks 5–13
					is refinement on this foundation.
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
					{#each mindMapNodes as node (node.slug)}
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

				{#each mindMapNodes as node (node.slug)}
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
</div>
