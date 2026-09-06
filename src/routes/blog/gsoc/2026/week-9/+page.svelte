<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "Extraction Framework",
			slug: "extraction-framework",
			href: "https://github.com/dbpedia/extraction-framework"
		},
		{
			label: "Mappings spreadsheet",
			slug: "mappings-spreadsheet",
			href: "https://docs.google.com/spreadsheets/d/1cCO_8K4m8DOv7N5kospO6mzXJOT9-xV-swAp-uoDrTo/edit?usp=sharing"
		},
		{
			label: "am.dbpedia.org",
			slug: "am-dbpedia",
			href: "https://am.dbpedia.org"
		},
		{
			label: "amseg",
			slug: "amseg",
			href: "https://pypi.org/project/amseg/"
		}
	];

	const bugs = [
		{
			title: "Ethiopian date parser — ቀን word not recognised",
			body: "The date parser silently drops 94.5% of Ethiopian-format dates because none of its five regular expressions admit the standard Amharic word ቀን (meaning 'day') that appears between the month and year in the most common date format (e.g. month day ቀን year). The result is that the vast majority of Ethiopian-format dates in Amharic Wikipedia infoboxes are simply ignored rather than converted."
		},
		{
			title: "Ethiopian date parser — year conversion ±1 error",
			body: "The Gregorian year conversion formula in EthiopianDateParser branches on the Ethiopian month number where the algorithm actually requires the Gregorian month number. For months 1, 2, 5, and 6 this causes an off-by-one year error, so dates like Meskerem 1 (September) are converted to the wrong Gregorian year. Libya's independence date appearing as 0029-01-26 in the live endpoint is a direct consequence."
		},
		{
			title: "Month spelling gaps",
			body: "EthiopianDateParserConfig knows only one spelling per month, and for Tahisas (month 4) it registers a spelling used by only 0.2% of actual Amharic Wikipedia date occurrences. The two dominant spellings — ታኅሣሥ and ታህሳስ — are entirely unrecognised, so nearly all occurrences of month 4 fail to parse."
		},
		{
			title: "Regex capture group arity mismatch",
			body: "Two of the five date regular expressions have a mismatch between the number of capture groups in the pattern and the number of group references in the extraction code. The mismatch causes a silent failure: the regex matches text but the subsequent extraction code reads from the wrong group index, so no date is produced. These two regexes effectively never fire."
		},
		{
			title: "ignoreProperties union bug",
			body: "InfoboxExtractor uses .getOrElse when merging the language-specific ignore set with the English baseline. This means that any language that defines its own ignore set loses the English baseline rather than extending it. The consequence for Amharic: image, map, and flag properties that should be suppressed are instead emitted as facts, polluting the knowledge graph with URL literals."
		},
		{
			title: "Empty literal triples",
			body: "Blank infobox parameters produce empty string literals that are written as triples. A triple with an empty string object (e.g. dbr:Ethiopia dbo:capital '') is actively harmful because SPARQL queries can match it, making queries return misleading results. An absent triple is far preferable to a triple carrying no information."
		},
		{
			title: "Stale Namespaces.scala",
			body: "Namespaces.scala was generated from old Amharic Wikipedia siteinfo. The Amharic Wikipedia community has since renamed namespace 828 from the English placeholder 'Module' to the Amharic ሞጁል, but the extraction framework still uses the old name. This causes 39 Lua module pages to be misclassified as main-namespace articles and extracted as if they were encyclopaedia entries."
		}
	];

	const mindMapNodes = [
		{ label: "Extraction", slug: "extraction-framework", angle: 270 },
		{ label: "Amharic Dump", slug: "am-dbpedia", angle: 342 },
		{ label: "Date Parser", slug: "extraction-framework", angle: 54 },
		{ label: "Namespace Bug", slug: "extraction-framework", angle: 126 },
		{ label: "Mappings", slug: "mappings-spreadsheet", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 9: Extraction Framework Audit and Amharic Bug Discovery
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			Turned attention to the DBpedia extraction framework and ran it against multiple Amharic
			Wikipedia dumps. The result was a systematic catalogue of bugs — some affecting only
			Amharic, others affecting all languages — that would become the foundation for the GitHub
			issues documented later.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-9</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#extraction-framework</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#bug-audit</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#mappings</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					9
				</span>
				Jul 17, 2026 – Jul 24, 2026
			</h2>
			<p class="mt-5">
				Week 9 was the most intensive debugging week of the project. Attention shifted away from
				the LLM pipeline and toward the DBpedia extraction framework — the Scala codebase that
				reads Amharic Wikipedia dumps and converts infobox data into RDF triples. Running the
				framework against multiple dump snapshots and comparing the outputs made recurring bugs
				visible in a way that a single run could not. The resulting catalogue of seven bug
				classes became the foundation for the formal GitHub issues filed in Week 13.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Running the extraction framework on Amharic dumps
			</h2>
			<p class="mt-5">
				The
				<a
					href="https://github.com/dbpedia/extraction-framework"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>DBpedia extraction framework</a
				> was run against four Amharic Wikipedia dump files spanning different dates:
				amwiki-20260101, amwiki-20260401, amwiki-20260601, and amwiki-20260801. Running across
				multiple dump dates was deliberate — bugs that appear in every dump are structural problems
				in the extraction code, while bugs that appear in only some dumps may indicate changes in
				the Wikipedia content or template structure. Comparing outputs across these four dates
				made it possible to classify bugs by their stability and origin.
			</p>
			<p class="mt-4">
				The extraction output for each dump was examined systematically: RDF triple counts per
				extractor, error logs, property distributions, and a sample of individual triples for
				spot-checking. This cross-dump comparison approach is more reliable than reading the code
				alone because bugs that are invisible in a code review often become obvious when their
				effects compound across thousands of articles.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Bug catalogue — what was found
			</h2>
			<p class="mt-5">
				Seven distinct bug classes were identified across the extraction framework. Each had a
				measurable impact on the quality of the Amharic DBpedia knowledge graph:
			</p>
			<div class="mt-5 space-y-5">
				{#each bugs as bug (bug.title)}
					<div class="rounded-2xl border border-foreground/10 bg-muted/25 p-5">
						<h3 class="font-mono text-base font-black">{bug.title}</h3>
						<p class="mt-3 text-sm leading-7">{bug.body}</p>
					</div>
				{/each}
			</div>
			<p class="mt-5">
				What made this audit valuable was the specificity of each finding. Vague reports of
				"extraction problems" are easy to dismiss; a report that says "94.5% of Ethiopian-format
				dates are silently dropped because of a missing regex pattern, with evidence from 847
				failed articles across three dump dates" demands attention and has a clear path to a fix.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Additional Amharic mappings
			</h2>
			<p class="mt-5">
				Alongside the extraction framework audit, further infobox mappings were added to the
				shared tracking
				<a
					href="https://docs.google.com/spreadsheets/d/1cCO_8K4m8DOv7N5kospO6mzXJOT9-xV-swAp-uoDrTo/edit?usp=sharing"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>spreadsheet</a
				>. The extraction framework audit made it clear that even perfectly-authored mappings
				would produce degraded output until the underlying bugs were fixed — but the mapping work
				continued in parallel, because both the mapping coverage and the extraction quality need
				to improve together. A correct mapping through a broken extractor still produces no
				triples; a perfect extractor with incomplete mappings produces no triples either.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-9#${link.slug}`}
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
					WEEK 9
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-9#${node.slug}`}
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
