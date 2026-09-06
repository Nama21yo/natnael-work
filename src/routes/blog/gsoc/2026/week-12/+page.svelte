<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "LLMIntegration PR (Issue #3)",
			slug: "issue-3-pr",
			href: "https://github.com/AmharicDBpedia/LLMIntegration"
		},
		{
			label: "DSPy ChainOfThought",
			slug: "dspy-cot",
			href: "https://dspy.ai/"
		},
		{
			label: "Ethiopian Calendar",
			slug: "ethiopian-calendar",
			href: "https://en.wikipedia.org/wiki/Ethiopian_calendar"
		},
		{
			label: "DBpedia Extraction Framework",
			slug: "extraction-framework",
			href: "https://github.com/dbpedia/extraction-framework"
		},
		{
			label: "Amharic property mapping dataset",
			slug: "dataset",
			href: "https://huggingface.co/datasets/dice-research/amharic-property-mapping"
		}
	];

	const promptStrategies = [
		{
			name: "Zero-shot (baseline)",
			description:
				"No examples provided. The LLM must rely entirely on its pre-trained knowledge of English DBpedia properties and its ability to interpret Amharic text.",
			result: "Accuracy ~58–62%; strong for common properties, weak for rare ontology classes."
		},
		{
			name: "Few-shot with LabeledFewShot",
			description:
				"DSPy's LabeledFewShot optimizer selects k demonstrations from the training set that most closely resemble the test example, sampling with a fixed random seed for reproducibility.",
			result: "Best at k=5: +4.2 pp over zero-shot on average across models."
		},
		{
			name: "ChainOfThought (CoT)",
			description:
				"The DSPy ChainOfThought module prepends 'Let's think step by step' reasoning to the prediction. The model describes why each candidate does or doesn't match before committing to a choice.",
			result: "Modest improvement over standard few-shot; reasoning traces helpful for debugging failures."
		},
		{
			name: "Self-consistency ensemble",
			description:
				"Three independent CoT samples are taken for each input, and the majority vote is used as the final answer. Tied votes are broken by retriever rank order.",
			result: "Small but consistent gain over single-sample CoT; adds latency per example."
		}
	];

	const ethiopianBugs = [
		{
			bug: "Year-off-by-one for months 1, 2, 5, 6",
			detail:
				"The Ethiopian calendar conversion code uses a fixed Julian Day Number offset, but months at the turn of the Ethiopian year (Meskerem and Tikimt) and the middle of the year (Ginbot and Sene) are off by ±1 Gregorian year depending on the leap year cycle."
		},
		{
			bug: "Missing ቀን regex (94.5% of dates dropped)",
			detail:
				"The word ቀን (meaning 'day') appears in most Ethiopian date strings but was absent from the extraction regex. Without it, 94.5% of date literals in the Amharic dump were silently discarded rather than extracted."
		},
		{
			bug: "Month 4 (Hamle) nearly unrecognised",
			detail:
				"Month 4 — ሐምሌ — had a spelling variant that appeared in 99.8% of Amharic Wikipedia articles but was not in the month-name lookup table. The result: virtually no July dates extracted from Amharic Wikipedia."
		}
	];

	const mindMapNodes = [
		{ label: "Issue #3 PR", slug: "issue-3-pr", angle: 270 },
		{ label: "Issue #4", slug: "dspy-cot", angle: 342 },
		{ label: "CoT strategy", slug: "dataset", angle: 54 },
		{ label: "Ethiopian bugs", slug: "ethiopian-calendar", angle: 126 },
		{ label: "EF audit", slug: "extraction-framework", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 12: Issue #3 Merged, Prompt Engineering, and Calendar Bug Confirmation
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			The baseline benchmark PR was merged with full documented results. Issue #4 — a systematic
			prompt engineering experiment — got underway. And the Ethiopian calendar extraction bugs
			discovered in Week 9 were confirmed empirically with extraction output evidence.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#gsoc-2026</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#week-12</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#prompt-engineering</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#extraction-bugs</span>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2 class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground">
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">12</span>
				Aug 7, 2026 – Aug 14, 2026
			</h2>
			<p class="mt-5">
				Week 12 was a week of consolidation and forward motion. The Issue #3 PR was reviewed by
				mentors, merged into the main branch, and its results documented in the project's progress
				report. The next experiment — Issue #4, which explores how different prompt engineering
				strategies affect accuracy — began immediately. And a parallel thread of work confirmed,
				with extraction evidence rather than code inspection alone, that the Ethiopian calendar
				bugs documented in Week 9 were producing real data loss in the Amharic DBpedia graph.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Issue #3 PR: merging the baseline
			</h2>
			<p class="mt-5">
				The pull request implementing Issue #3 was the first formal code contribution to the
				LLMIntegration repository that went through a full review cycle. The PR included the
				benchmark harness (configurable via YAML), the Afro-XLM-R retrieval index builder, the
				DSPy-based reranker, the answer snapping logic, and the results CSV for all evaluated
				models and shot settings. Mentor feedback during review focused on two areas: the
				reproducibility of seed sampling (confirmed fixed via <code>random.Random(SEED + seed)</code>)
				and the clarity of the accuracy metric definition (documented in the README with the full
				fuzzy-match formula).
			</p>
			<p class="mt-4">
				The merged results showed that the best baseline configuration — Afro-XLM-R retriever
				feeding 5-shot Qwen 2.5 (7B) — achieved approximately 62.4% accuracy on the 279-example
				test set. This was a meaningful improvement over the retrieval-only floor of ~52%, but
				it also made clear that there was substantial room for improvement through better prompt
				design, cross-lingual reasoning, and ensemble methods. Those improvements are what Issues
				#4, #5, and #6 set out to achieve.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Issue #4: prompt engineering experiment
			</h2>
			<p class="mt-5">
				The guiding question for Issue #4 was: does the way we instruct the LLM to reason about
				the shortlist of candidates affect accuracy, and if so, which strategy works best for
				Amharic property mapping? The experiment kept the retrieval stage fixed (Afro-XLM-R, top
				10) and varied only the prompt structure and reasoning pattern.
			</p>
			<div class="mt-5 space-y-4">
				{#each promptStrategies as strategy (strategy.name)}
					<div class="rounded-2xl border border-foreground/10 bg-background/70 p-5">
						<h3 class="font-mono text-base font-black">{strategy.name}</h3>
						<p class="mt-2 text-sm leading-7">{strategy.description}</p>
						<p class="mt-3 rounded-xl bg-cyan/5 px-3 py-2 text-xs font-bold text-cyan">
							Result: {strategy.result}
						</p>
					</div>
				{/each}
			</div>
			<p class="mt-5">
				The prompt engineering work used DSPy's module system throughout, which meant that each
				strategy was implemented as a composable DSPy module rather than a hand-crafted prompt
				string. This made it straightforward to swap strategies in and out without rewriting the
				evaluation loop. The results confirmed that few-shot examples and chain-of-thought
				reasoning both help, but neither alone closes the gap to the theoretical ceiling — a
				result that motivated the translation and ensemble experiments in Weeks 13–14.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Ethiopian calendar bugs: extraction evidence
			</h2>
			<p class="mt-5">
				The extraction bugs identified in Week 9 were mostly found through code inspection: reading
				the extraction framework source and spotting the missing regex, the wrong offset, the
				absent month names. Week 12 produced something more concrete — actual extraction output
				showing that these bugs were causing real data loss at scale.
			</p>
			<div class="mt-5 space-y-4">
				{#each ethiopianBugs as bug (bug.bug)}
					<div class="rounded-2xl border border-rose-500/20 bg-rose-500/5 p-5">
						<h3 class="font-mono text-sm font-black text-rose-400">{bug.bug}</h3>
						<p class="mt-2 text-sm leading-7">{bug.detail}</p>
					</div>
				{/each}
			</div>
			<p class="mt-5">
				The most striking finding was the missing ቀን regex: because a word meaning "day" was not
				in the pattern, the extraction framework was silently discarding 94.5% of date literals
				from the Amharic dump. That is not a minor edge case — it means that almost every
				Ethiopian-calendar date in every Amharic Wikipedia article had been silently dropped
				rather than extracted. This confirmed that the extraction framework needed real upstream
				fixes, not workarounds in the mapping layer. Formal bug reports were being prepared for
				the following week.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-12#${link.slug}`}
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
				{#each mindMapNodes as node (node.slug)}
					{@const rad = (node.angle * Math.PI) / 180}
					{@const cx = 130 + 85 * Math.cos(rad)}
					{@const cy = 128 + 85 * Math.sin(rad)}
					<line x1="130" y1="128" x2={cx} y2={cy} stroke="rgba(34,211,238,0.25)" stroke-width="1.5" stroke-dasharray="4 6" />
				{/each}
			</svg>
			<div class="pointer-events-none absolute inset-0 flex items-center justify-center">
				<div class="flex h-16 w-16 items-center justify-center rounded-full border-2 border-cyan/40 bg-zinc-900/80 text-center text-[10px] font-black text-cyan shadow-[0_0_15px_rgba(34,211,238,0.25)] backdrop-blur-md">WEEK 12</div>
			</div>
			{#each mindMapNodes as node (node.slug)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a href={`${base}/blog/gsoc/2026/week-12#${node.slug}`} class="absolute flex h-[54px] w-[54px] -translate-x-1/2 -translate-y-1/2 items-center justify-center rounded-full border border-white/10 bg-zinc-900/90 p-1.5 text-center text-[8px] leading-tight font-bold text-white/80 shadow-lg backdrop-blur-sm transition-all duration-300 hover:z-20 hover:scale-125 hover:border-cyan hover:bg-cyan/10 hover:text-cyan hover:shadow-[0_0_20px_rgba(34,211,238,0.4)]" style={`left: ${(cx / 260) * 100}%; top: ${(cy / 256) * 100}%;`} aria-label={node.label} title={node.label}>
					<span class="line-clamp-3">{node.label}</span>
				</a>
			{/each}
		</div>
	</div>
</aside>
