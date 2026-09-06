<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "LangGraph",
			slug: "langgraph",
			href: "https://langchain-ai.github.io/langgraph/"
		},
		{
			label: "FastAPI",
			slug: "fastapi",
			href: "https://fastapi.tiangolo.com/"
		},
		{
			label: "Playwright",
			slug: "playwright",
			href: "https://playwright.dev/"
		},
		{
			label: "Tentris",
			slug: "tentris",
			href: "https://tentris.dice-research.org/"
		},
		{
			label: "Extraction Framework",
			slug: "extraction-framework",
			href: "https://github.com/dbpedia/extraction-framework"
		},
		{
			label: "DBpedia Mappings",
			slug: "dbpedia-mappings",
			href: "https://mappings.dbpedia.org/"
		}
	];

	const summaryCards = [
		{
			label: "Backend foundation",
			value: "LangGraph-ready",
			detail:
				"Initialized the backend infrastructure that the mapping agent and dump workflows can build on."
		},
		{
			label: "API surface",
			value: "Sanitize + preview",
			detail:
				"Added sanitizer and dump preview endpoints so users can inspect input before extraction."
		},
		{
			label: "Quality gate",
			value: "Chromium + mobile",
			detail:
				"Added Playwright coverage for core website components across desktop and mobile layouts."
		},
		{
			label: "Ontology work",
			value: "3 domains",
			detail:
				"Created Amharic templates, example wiki pages, and DBpedia mappings for three entity types."
		}
	];

	const implementationSections = [
		{
			title: "Backend infrastructure for the mapping workflow",
			body: "This week I started turning the project from a static UI into an application that can support real Amharic DBpedia workflows. The backend now has a foundation for LangGraph-based orchestration, which gives us a place to coordinate future steps such as dump loading, candidate extraction, ontology lookup, mapping validation, and human review. I also added sanitizer and dump preview endpoints, because before we can ask users or agents to process a dump, we need a safe way to inspect and clean what is coming in.",
			tags: ["LangGraph", "FastAPI", "sanitizer", "dump preview"]
		},
		{
			title: "Statistics API backed by the extraction framework",
			body: "I implemented a FastAPI endpoint that calculates statistics for a selected Amharic dump. Internally, this endpoint connects to the DBpedia extraction framework instead of inventing a separate parser. That matters because the website should reflect the same extraction behavior that the DBpedia ecosystem already uses. The endpoint gives the UI a practical way to answer questions like how much content exists in a dump and what kind of extraction output we can expect.",
			tags: ["FastAPI", "statistics", "dump analysis", "extraction framework"]
		},
		{
			title: "Website confidence through browser tests",
			body: "I added Playwright tests for the website so that every important component is exercised in Chromium and in a mobile viewport. The mobile pass is important for this project because community contributors and readers may not always use a desktop machine. The goal is not only to catch broken routes, but also to protect the usability of the Amharic DBpedia UI as new sections are added.",
			tags: ["Playwright", "Chromium", "mobile", "regression tests"]
		},
		{
			title: "New Amharic DBpedia UI sections",
			body: "I added a news section to the new Amharic DBpedia interface so project updates can be surfaced directly inside the site. I also integrated a Tentris UI entry point, which is useful because Tentris is part of the RDF and knowledge graph exploration ecosystem. Together, these additions move the website closer to being a practical project hub rather than only a landing page.",
			tags: ["news", "Tentris", "UI", "knowledge graph exploration"]
		}
	];

	const mappingContributions = [
		{
			domain: "Scientist",
			description:
				"Created the Amharic scientist information template, added it to a real wiki page for Timnit Worku, and created the matching DBpedia mapping page.",
			links: [
				{
					label: "New template",
					href: "https://am.wikipedia.org/w/index.php?title=%E1%88%98%E1%88%88%E1%8C%A0%E1%8D%8A%E1%8B%AB:%E1%8B%A8%E1%88%B3%E1%8B%AD%E1%8A%95%E1%89%B2%E1%88%B5%E1%89%B5_%E1%88%98%E1%88%A8%E1%8C%83"
				},
				{
					label: "Wiki page: Timnit Worku",
					href: "https://am.wikipedia.org/wiki/%E1%89%B5%E1%88%9D%E1%8A%90%E1%89%B5_%E1%8C%88%E1%89%A5%E1%88%A9"
				},
				{
					label: "DBpedia mapping",
					href: "https://mappings.dbpedia.org/index.php/Mapping_am:%E1%8B%A8%E1%88%B3%E1%8B%AD%E1%8A%95%E1%89%B2%E1%88%B5%E1%89%B5_%E1%88%98%E1%88%A8%E1%8C%83"
				}
			]
		},
		{
			domain: "Sportsmen",
			description:
				"Created the Amharic athlete information template, tested it on the Abebe Bikila page, and mapped the template fields to DBpedia ontology properties.",
			links: [
				{
					label: "New template",
					href: "https://am.wikipedia.org/wiki/%E1%88%98%E1%88%88%E1%8C%A0%E1%8D%8A%E1%8B%AB:%E1%8B%A8%E1%8A%A0%E1%89%B5%E1%88%8C%E1%89%B5_%E1%88%98%E1%88%A8%E1%8C%83"
				},
				{
					label: "Wiki page: Abebe Bikila",
					href: "https://am.wikipedia.org/wiki/%E1%8A%A0%E1%89%A0%E1%89%A0_%E1%89%A2%E1%89%82%E1%88%8B"
				},
				{
					label: "DBpedia mapping",
					href: "https://mappings.dbpedia.org/index.php/Mapping_am:%E1%8B%A8%E1%8A%A0%E1%89%B5%E1%88%8C%E1%89%B5_%E1%88%98%E1%88%A8%E1%8C%83"
				}
			]
		},
		{
			domain: "Writer",
			description:
				"Created the Amharic writer information template, added it to the Bealu Girma wiki page, and created the corresponding DBpedia mapping.",
			links: [
				{
					label: "New template",
					href: "https://am.wikipedia.org/wiki/%E1%88%98%E1%88%88%E1%8C%A0%E1%8D%8A%E1%8B%AB:%E1%8B%A8%E1%8B%B0%E1%88%AB%E1%88%B2_%E1%88%98%E1%88%A8%E1%8C%83"
				},
				{
					label: "Wiki page: Bealu Girma",
					href: "https://am.wikipedia.org/wiki/%E1%89%A0%E1%8B%93%E1%88%89_%E1%8C%8D%E1%88%AD%E1%88%9B"
				},
				{
					label: "DBpedia mapping",
					href: "https://mappings.dbpedia.org/index.php/Mapping_am:%E1%8B%A8%E1%8B%B0%E1%88%AB%E1%88%B2_%E1%88%98%E1%88%A8%E1%8C%83"
				}
			]
		}
	];

	const mindMapNodes = [
		{ label: "Backend", slug: "fastapi", angle: 270 },
		{ label: "LangGraph", slug: "langgraph", angle: 330 },
		{ label: "Playwright", slug: "playwright", angle: 30 },
		{ label: "Tentris", slug: "tentris", angle: 90 },
		{ label: "Mappings", slug: "dbpedia-mappings", angle: 150 },
		{ label: "Extraction", slug: "extraction-framework", angle: 210 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 2: Backend, Testing, and First Amharic Mappings
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			This week moved the Amharic DBpedia project from planning into implementation: backend
			infrastructure, dump tooling, browser tests, UI additions, and the first three concrete
			template-to-ontology mapping contributions.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-2</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#fastapi</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#dbpedia-mappings</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					2
				</span>
				May 30, 2026 - June 5, 2026
			</h2>
			<p class="mt-5">
				The main theme of Week 2 was building usable foundations. I worked on the backend services
				that will support future mapping automation, added early API endpoints for dump
				inspection, improved the site with test coverage and new UI sections, and also contributed
				real Amharic Wikipedia templates and DBpedia mappings. This combination was important
				because the project needs both engineering infrastructure and real ontology content to
				move forward.
			</p>
		</section>

		<section class="grid gap-4 md:grid-cols-2">
			{#each summaryCards as card (card.label)}
				<div class="rounded-2xl border border-foreground/10 bg-muted/30 p-5">
					<p class="blog-label">{card.label}</p>
					<h3 class="mt-2 text-xl font-black text-foreground">{card.value}</h3>
					<p class="mt-2 text-sm leading-6 text-muted-foreground">{card.detail}</p>
				</div>
			{/each}
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Implementation work
			</h2>
			<div class="mt-5 space-y-5">
				{#each implementationSections as section (section.title)}
					<div class="rounded-2xl border border-foreground/10 bg-background/70 p-5">
						<h3 class="font-mono text-xl font-black">{section.title}</h3>
						<p class="mt-3">{section.body}</p>
						<div class="mt-4 flex flex-wrap gap-2">
							{#each section.tags as tag (tag)}
								<span
									class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
								>
									#{tag}
								</span>
							{/each}
						</div>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Templates, wiki pages, and DBpedia mappings
			</h2>
			<p class="mt-5">
				The most concrete ontology progress this week was creating three Amharic template families
				and connecting them to real example pages and DBpedia mapping pages. I chose Scientist,
				Sportsmen, and Writer because they are understandable entity types and each one gives us a
				useful pattern for mapping person-related infobox fields into DBpedia.
			</p>

			<div class="mt-5 space-y-5">
				{#each mappingContributions as contribution (contribution.domain)}
					<div class="rounded-2xl border border-foreground/10 bg-muted/25 p-5">
						<h3 class="font-mono text-xl font-black">{contribution.domain}</h3>
						<p class="mt-2">{contribution.description}</p>
						<div class="mt-4 flex flex-wrap gap-2">
							{#each contribution.links as link (link.href)}
								<a
									href={link.href}
									target="_blank"
									rel="noreferrer"
									class="rounded-full bg-brand-subtle/50 px-3 py-1 text-xs font-bold text-brand-muted transition hover:bg-brand hover:text-background"
								>
									{link.label}
								</a>
							{/each}
						</div>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Why this week mattered
			</h2>
			<p class="mt-5">
				By the end of Week 2, the project had a stronger technical base and a clearer content
				path. The backend can begin to support agentic workflows, the UI has more practical
				project surfaces, the browser tests reduce regression risk, and the mapping work gives us
				real examples to validate against. That is the bridge I wanted for this week: connect
				infrastructure work with visible Amharic DBpedia contributions.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-2#${link.slug}`}
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
					WEEK 2
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-2#${node.slug}`}
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
