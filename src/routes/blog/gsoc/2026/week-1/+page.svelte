<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{ label: "GSoC 2026", slug: "gsoc-2026", href: "https://summerofcode.withgoogle.com/programs/2026/organizations/dbpedia" },
		{ label: "Andargachew", slug: "mentor" },
		{ label: "LangGraph Paper", slug: "langgraph-paper", href: "https://papers.academic-conferences.org/index.php/icair/article/view/4142/3966" },
		{ label: "Vanilla Typescript", slug: "amharic-site" },
		{ label: "Knowledge Graphs Course", slug: "kg-course", href: "https://open.hpi.de/courses/knowledgegraphs2023/items/4GIRFuJmmJIQyXTs4muG0h" },
		{ label: "SPARQL", slug: "sparql", href: "https://www.w3.org/TR/sparql11-query/" }
	];

	const bodyParagraphs = [
		"Scheduled a meeting with one of my Mentors ([[Andargachew]]) and discussed what has been done on the previous GSOC 2025, what were the challenges, what things I should focus on and overall roadmap for the next weeks. I also got some advice on how I should document and organize things.",
		"Read a paper on how to orchestrate Agents using Langgraph and built a Knowledge Graph with it ([[LangGraph Paper]]).",
		"Finished refactoring the previous website with [[Vanilla Typescript]] (trying to use minimal libraries so that it won’t be difficult for contributors).",
		"Started a new course on knowledge graphs by Prof. Dr. Harald Sack so that my understanding is comprehensive ([[Knowledge Graphs Course]]).",
		"Also started poking around with [[SPARQL]] against the Amharic DBpedia endpoint -- a few basic property-coverage queries, some of which turned up issues specific to the Amharic side. Early groundwork for the deeper audit that became Week 10."
	];

	const firstSparqlQuery = `PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT (COUNT(?entity) AS ?count)
WHERE {
  ?entity rdfs:label ?label .
  FILTER(LANG(?label) = "am")
}`;

	function renderParagraph(paragraph: string) {
		const segments: { text: string; link?: string }[] = [];
		let cursor = 0;
		let match: RegExpExecArray | null;
		const wikiPattern = /\[\[([^\]]+)\]\]/g;
		while ((match = wikiPattern.exec(paragraph))) {
			if (match.index > cursor) {
				segments.push({ text: paragraph.slice(cursor, match.index) });
			}
			const label = match[1];
			const wikiEntry = wikiLinks.find((item) => item.label === label);
			segments.push({
				text: label,
				link: wikiEntry?.href
			});
			cursor = match.index + match[0].length;
		}
		if (cursor < paragraph.length) {
			segments.push({ text: paragraph.slice(cursor) });
		}
		return segments;
	}

	const mindMapNodes = [
		{ label: "Mentorship", slug: "mentor", angle: 270 },
		{ label: "GSoC 2025", slug: "gsoc-2025", angle: 330 },
		{ label: "LangGraph", slug: "langgraph-paper", angle: 30 },
		{ label: "Knowledge Graphs", slug: "kg-course", angle: 90 },
		{ label: "Vanilla TS", slug: "amharic-site", angle: 150 },
		{ label: "SPARQL", slug: "sparql", angle: 210 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 1
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			Mentorship syncs, deep dives into LangGraph orchestration, finishing up the site refactor, and embarking on a specialized KG course.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#gsoc-2026</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#week-1</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#langgraph</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#knowledge-graphs</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#sparql</span>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2 class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground">
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					1
				</span>
				May 23, 2026 - May 29, 2026
			</h2>
			<div class="mt-5 space-y-4">
				{#each bodyParagraphs as paragraph (paragraph)}
					<p>
						{#each renderParagraph(paragraph) as segment, index (index)}
							{#if segment.link}
								<a
									href={segment.link.startsWith("http") ? segment.link : `${base}/blog/gsoc/2026/week-1#${segment.text.toLowerCase().replaceAll(" ", "-")}`}
									target={segment.link.startsWith("http") ? "_blank" : undefined}
									rel={segment.link.startsWith("http") ? "noreferrer" : undefined}
									class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
								>
									{segment.text}
								</a>
							{:else}
								<span>{segment.text}</span>
							{/if}
						{/each}
					</p>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				First SPARQL query
			</h2>
			<p class="mt-5">
				Ran this against the Amharic DBpedia SPARQL endpoint just to see how many entities even
				had an Amharic label:
			</p>
			<pre
				class="mt-4 overflow-x-auto rounded-xl border border-cyan/15 bg-zinc-950 p-4 text-xs leading-6 text-cyan/90"><code>{firstSparqlQuery}</code></pre>
			<p class="mt-4">
				The count came back lower than I expected, which is what got me looking more closely at
				Amharic coverage gaps later in the summer -- that became the full SPARQL audit in Week
				10.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-1#${link.slug}`}
				title={link.label}
				reason="Concept reference from this note."
				variant="backlink"
			/>
		{/each}
	</div>

	<div class="mt-6 border-t border-foreground/10 pt-6">
		<p class="blog-label">Mind map</p>
		<div class="relative mt-4 h-64 rounded-3xl bg-zinc-950 text-white overflow-hidden shadow-[inset_0_0_20px_rgba(0,0,0,0.5)]">
			<svg class="absolute inset-0 h-full w-full pointer-events-none" viewBox="0 0 260 256" preserveAspectRatio="xMidYMid meet">
				<circle cx="130" cy="128" r="40" fill="none" stroke="rgba(34,211,238,0.2)" stroke-width="1" class="animate-ping" style="animation-duration: 3s;" />
				{#each mindMapNodes as node (node.label)}
					{@const rad = (node.angle * Math.PI) / 180}
					{@const cx = 130 + 85 * Math.cos(rad)}
					{@const cy = 128 + 85 * Math.sin(rad)}
					<line x1="130" y1="128" x2={cx} y2={cy} stroke="rgba(34,211,238,0.25)" stroke-width="1.5" stroke-dasharray="4 6" class="transition-all duration-700"/>
				{/each}
			</svg>

			<div class="absolute inset-0 flex items-center justify-center pointer-events-none">
				<div class="flex h-16 w-16 items-center justify-center rounded-full border-2 border-cyan/40 bg-zinc-900/80 backdrop-blur-md text-center text-[10px] font-black text-cyan shadow-[0_0_15px_rgba(34,211,238,0.25)]">
					WEEK 1
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-1#${node.slug}`}
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
