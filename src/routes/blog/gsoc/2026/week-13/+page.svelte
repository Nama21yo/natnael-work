<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "LLMIntegration PR #9",
			slug: "pr-9",
			href: "https://github.com/AmharicDBpedia/LLMIntegration"
		},
		{
			label: "LLMIntegration PR #10",
			slug: "pr-10",
			href: "https://github.com/AmharicDBpedia/LLMIntegration"
		},
		{
			label: "DBpedia Extraction Framework",
			slug: "extraction-framework",
			href: "https://github.com/dbpedia/extraction-framework"
		},
		{
			label: "Helsinki-NLP translation",
			slug: "helsinki-nlp",
			href: "https://huggingface.co/Helsinki-NLP"
		},
		{
			label: "amseg tokeniser",
			slug: "amseg",
			href: "https://github.com/uhh-lt/amharic-segmentation"
		}
	];

	const translationApproaches = [
		{
			title: "Pivot translation (Amharic → English)",
			description:
				"The Amharic property mention is translated to English before being passed to the retriever and LLM. This allows the LLM to operate entirely in English, where it has much stronger priors about DBpedia property names.",
			pros: "Leverages LLM's English-language knowledge; works with any English-only retriever.",
			cons: "Translation errors compound with prediction errors; loses Ge'ez script context the LLM might use."
		},
		{
			title: "Augmented retrieval (Amharic + English)",
			description:
				"The retriever embeds both the original Amharic mention and its English translation, then takes the union of the top-5 candidates from each query. The LLM reranks the combined shortlist of up to 10 unique candidates.",
			pros: "More robust to translation errors; keeps both language signals alive for the LLM.",
			cons: "Larger shortlist can dilute the most relevant candidate; slightly higher latency."
		}
	];

	const efIssues = [
		{
			title: "Ethiopian calendar date parser (±1 year off)",
			severity: "High",
			detail:
				"Months 1, 2, 5, and 6 of the Ethiopian calendar year produce incorrect Gregorian year conversions due to a fixed Julian Day Number offset that does not account for leap year boundary crossings."
		},
		{
			title: "Missing ቀን regex — 94.5% date loss",
			severity: "Critical",
			detail:
				"The word ቀን ('day') is present in virtually every Ethiopian date string but was absent from the extraction regex, causing 94.5% of date literals to be silently discarded."
		},
		{
			title: "Capture-group arity mismatch",
			severity: "Medium",
			detail:
				"A regex used for parsing date components declares more capture groups than the post-processing code expects, leading to an ArrayIndexOutOfBoundsException on certain inputs."
		},
		{
			title: "Month 4 (Hamle) 99.8% unrecognised",
			severity: "High",
			detail:
				"The spelling variant of ሐምሌ (Hamle, the fourth month) used in almost all Amharic Wikipedia articles was missing from the month-name lookup table."
		},
		{
			title: "ignoreProperties union bug",
			severity: "Medium",
			detail:
				"When two mapping files both list the same property in their ignoreProperties set, the union operation silently overwrites one entry with the other instead of merging them."
		},
		{
			title: "Empty literal emission",
			severity: "Low",
			detail:
				"Certain template fields with whitespace-only values are extracted as empty string literals rather than being skipped, producing invalid RDF triples."
		},
		{
			title: "Stale Namespaces.scala",
			severity: "Low",
			detail:
				"The namespace constants file references a DBpedia namespace URI that was retired, causing some triples to be serialised with an outdated prefix."
		}
	];

	const mindMapNodes = [
		{ label: "PR #9", slug: "pr-9", angle: 270 },
		{ label: "PR #10", slug: "pr-10", angle: 342 },
		{ label: "Translation", slug: "helsinki-nlp", angle: 54 },
		{ label: "EF bugs", slug: "extraction-framework", angle: 126 },
		{ label: "Integration doc", slug: "amseg", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 13
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			I submitted Issue #4 (prompt engineering) as PR #9 and Issue #5 (translation experiment)
			as PR #10, formally reported all seven extraction framework bugs, and wrote an
			integration approach document laying out how the LLM pipeline and the extraction
			framework would eventually work together.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#gsoc-2026</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#week-13</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#translation</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#extraction-bugs</span>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2 class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground">
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">13</span>
				Aug 14, 2026 – Aug 21, 2026
			</h2>
			<p class="mt-5">
				Week 13 was one of the most productive of the summer. I submitted two pull requests in a
				single week — PR #9 closing Issue #4 (prompt engineering) and PR #10 closing Issue #5
				(cross-lingual translation experiment). Alongside that, I wrote up the seven extraction
				framework bugs documented over Weeks 9 and 12 as formal GitHub issues on the upstream
				repository, and drafted an integration approach document describing how the LLM mapping
				pipeline would connect to the broader DBpedia extraction workflow once the individual
				experiments were done.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				PR #9: Issue #4 prompt engineering results
			</h2>
			<p class="mt-5">
				The prompt engineering experiment gave me clear guidance on which strategies are actually
				worth using for Amharic property mapping. The pull request included the full experiment
				code, all result CSVs, and a markdown summary of findings. Chain-of-thought reasoning
				produced about a 2.1 percentage point improvement over standard few-shot across all models,
				with the biggest gains on examples where the correct property isn't the most
				surface-similar candidate in the shortlist — cases where reasoning helps the model look past
				misleading synonyms.
			</p>
			<p class="mt-4">
				The self-consistency ensemble (three CoT samples, majority vote) added another 1.3
				percentage points on top of single-sample CoT, at roughly 3× the latency per example —
				acceptable for an offline mapping pipeline, but it would need addressing for any real-time
				use case. These findings fed directly into Issue #6 (ensemble methods), which explored more
				principled ensemble strategies.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				PR #10: Issue #5 translation experiment
			</h2>
			<p class="mt-5">
				The hypothesis behind Issue #5 was that translating the Amharic property mention to
				English before handing it to the LLM might improve accuracy, since the LLM has much richer
				English-language training signal for DBpedia property names than it does for Amharic. I
				evaluated two translation strategies:
			</p>
			<div class="mt-5 space-y-4">
				{#each translationApproaches as approach (approach.title)}
					<div class="rounded-2xl border border-foreground/10 bg-background/70 p-5">
						<h3 class="font-mono text-base font-black">{approach.title}</h3>
						<p class="mt-2 text-sm leading-7">{approach.description}</p>
						<div class="mt-3 grid gap-2 md:grid-cols-2">
							<div class="rounded-xl bg-emerald/5 border border-emerald/20 px-3 py-2">
								<p class="text-xs font-bold text-emerald">Pros</p>
								<p class="mt-1 text-xs leading-5">{approach.pros}</p>
							</div>
							<div class="rounded-xl bg-rose-500/5 border border-rose-500/20 px-3 py-2">
								<p class="text-xs font-bold text-rose-400">Cons</p>
								<p class="mt-1 text-xs leading-5">{approach.cons}</p>
							</div>
						</div>
					</div>
				{/each}
			</div>
			<p class="mt-5">
				I used translation models from the Helsinki-NLP Opus-MT family, which provide open-source
				Amharic-to-English translation. The augmented retrieval approach (using both Amharic and
				English queries) beat pivot translation alone, giving a net improvement of about 2.8
				percentage points over the best baseline from Issue #3 — confirming that Amharic context
				carries signal that shouldn't be discarded even when English translation is available.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Extraction Framework: all seven bugs formally reported
			</h2>
			<p class="mt-5">
				Over Weeks 9 and 12 I'd accumulated evidence for seven distinct bug classes in the DBpedia
				extraction framework's Amharic language support. This week I wrote those findings up as
				formal GitHub issues on the upstream extraction-framework repository, with reproduction
				steps, expected vs. actual output, and impact estimates.
			</p>
			<div class="mt-5 space-y-3">
				{#each efIssues as issue (issue.title)}
					<div class="flex items-start gap-3 rounded-xl border border-foreground/10 bg-muted/20 px-4 py-3">
						<span class="mt-0.5 shrink-0 rounded-full px-2 py-0.5 text-[9px] font-black {
							issue.severity === 'Critical' ? 'bg-rose-500/20 text-rose-400' :
							issue.severity === 'High' ? 'bg-amber/20 text-amber' :
							'bg-muted text-muted-foreground'
						}">{issue.severity}</span>
						<div>
							<p class="text-sm font-bold">{issue.title}</p>
							<p class="mt-1 text-xs leading-5 text-muted-foreground">{issue.detail}</p>
						</div>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Integration approach document
			</h2>
			<p class="mt-5">
				The LLM benchmarking work and the extraction framework work had been running as parallel
				tracks all summer. This week I wrote a document connecting the two: an integration approach
				describing how the property mapping pipeline would work within the larger DBpedia
				extraction workflow once it's production-ready.
			</p>
			<p class="mt-4">
				The integration I proposed works like this: the extraction framework runs its existing
				heuristic extraction pass over the Amharic Wikipedia dump, producing candidate property
				assignments for each entity. For assignments where the heuristic confidence is below a
				threshold, the LLM mapping pipeline runs as a second pass, using the Amharic infobox field
				as the query and the Afro-XLM-R retriever to surface candidates. The LLM picks from the
				shortlist and its prediction replaces the low-confidence heuristic assignment.
				High-confidence heuristic assignments pass through unchanged, so the LLM pass only costs
				compute proportional to the fraction of ambiguous cases.
			</p>
			<p class="mt-4">
				I shared this document with mentors, and it became the basis for the final week's progress
				report and presentation.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-13#${link.slug}`}
				title={link.label}
				reason="Concept reference from this note."
				variant="backlink"
			/>
		{/each}
	</div>

	<div class="mt-6 border-t border-foreground/10 pt-6">
		<p class="blog-label">Mind map</p>
		<div class="relative mt-4 h-64 overflow-hidden rounded-3xl bg-zinc-950 text-white shadow-[inset_0_0_20px_rgba(0,0,0,0.5)]">
			<svg class="pointer-events-none absolute inset-0 h-full w-full" viewBox="0 0 260 256" preserveAspectRatio="xMidYMid meet">
				<circle cx="130" cy="128" r="40" fill="none" stroke="rgba(34,211,238,0.2)" stroke-width="1" class="animate-ping" style="animation-duration: 3s;" />
				{#each mindMapNodes as node (node.label)}
					{@const rad = (node.angle * Math.PI) / 180}
					{@const cx = 130 + 85 * Math.cos(rad)}
					{@const cy = 128 + 85 * Math.sin(rad)}
					<line x1="130" y1="128" x2={cx} y2={cy} stroke="rgba(34,211,238,0.25)" stroke-width="1.5" stroke-dasharray="4 6" />
				{/each}
			</svg>
			<div class="pointer-events-none absolute inset-0 flex items-center justify-center">
				<div class="flex h-16 w-16 items-center justify-center rounded-full border-2 border-cyan/40 bg-zinc-900/80 text-center text-[10px] font-black text-cyan shadow-[0_0_15px_rgba(34,211,238,0.25)] backdrop-blur-md">WEEK 13</div>
			</div>
			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a href={`${base}/blog/gsoc/2026/week-13#${node.slug}`} class="absolute flex h-[54px] w-[54px] -translate-x-1/2 -translate-y-1/2 items-center justify-center rounded-full border border-white/10 bg-zinc-900/90 p-1.5 text-center text-[8px] leading-tight font-bold text-white/80 shadow-lg backdrop-blur-sm transition-all duration-300 hover:z-20 hover:scale-125 hover:border-cyan hover:bg-cyan/10 hover:text-cyan hover:shadow-[0_0_20px_rgba(34,211,238,0.4)]" style={`left: ${(cx / 260) * 100}%; top: ${(cy / 256) * 100}%;`} aria-label={node.label} title={node.label}>
					<span class="line-clamp-3">{node.label}</span>
				</a>
			{/each}
		</div>
	</div>
</aside>
