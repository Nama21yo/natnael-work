<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "Afro-XLM-R",
			slug: "afro-xlmr",
			href: "https://huggingface.co/Davlan/afro-xlmr-base"
		},
		{
			label: "Kaggle notebook",
			slug: "kaggle-notebook",
			href: "https://www.kaggle.com/code/natnaelyohanes/multi-llm-benchmarking-v3"
		},
		{
			label: "DSPy",
			slug: "dspy",
			href: "https://dspy.ai"
		},
		{
			label: "amharic-property-mapping dataset",
			slug: "amharic-property-mapping",
			href: "https://huggingface.co/datasets/dice-research/amharic-property-mapping"
		}
	];

	const mindMapNodes = [
		{ label: "RAG", slug: "amharic-property-mapping", angle: 270 },
		{ label: "Afro-XLMR", slug: "afro-xlmr", angle: 342 },
		{ label: "Reliability", slug: "dspy", angle: 54 },
		{ label: "Few-shot", slug: "kaggle-notebook", angle: 126 },
		{ label: "Top-10 Retrieval", slug: "amharic-property-mapping", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 7: RAG Integration and LLM Reliability Evaluation
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			The pipeline matured significantly this week. A top-10 dense retrieval layer was added
			before the LLM, turning it into a proper retrieve-then-rerank system. LLM consistency
			metrics were also studied to understand how to trust the results.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-7</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#rag</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#retrieval</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#reliability</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#few-shot</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					7
				</span>
				Jul 3, 2026 – Jul 10, 2026
			</h2>
			<p class="mt-5">
				Week 7 transformed the pipeline from a prototype into a system with principled
				architecture. The addition of dense retrieval before the LLM reasoning step is the
				defining architectural decision of the project: it constrains the LLM's search space to a
				manageable shortlist, dramatically improving both accuracy and consistency. This week also
				introduced the reliability evaluation framework — because knowing a model's average
				accuracy is not enough; knowing how much that accuracy varies across runs is what makes it
				trustworthy.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				LLM reliability evaluation metrics
			</h2>
			<p class="mt-5">
				Before the Week 6 results could be interpreted with confidence, a fundamental question had
				to be addressed: do LLMs give the same answer when asked the same question twice? The
				answer is no — LLM outputs are stochastic, and accuracy figures measured from a single run
				are noisy estimates. This week introduced a systematic reliability evaluation framework
				with three core metrics.
			</p>
			<p class="mt-4">
				Self-consistency is the simplest measure: run the same prompt N times with the same input
				and measure what fraction of runs agree on the output. A model that returns the same
				answer 9 out of 10 times is far more reliable than one that agrees with itself 6 out of
				10 times, even if their mean accuracy is similar. Variance across random seeds captures
				a related but distinct concern — whether the accuracy changes meaningfully when the random
				number generator seed is changed. Finally, standard deviation of accuracy across runs
				gives a single number that quantifies how much to trust a reported mean. A model at 60%
				accuracy with ±1 pp standard deviation is practically more valuable than one at 62% with
				±8 pp, because the latter's real performance could plausibly be anywhere between 54% and
				70%.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Retrieve-then-rerank pipeline
			</h2>
			<p class="mt-5">
				The core architectural improvement of this week was introducing a top-10 retrieval step
				using Afro-XLM-R dense embeddings before every LLM call. The pipeline now works in two
				stages. First, all 595 DBpedia property labels are pre-encoded into a matrix of dense
				vectors using Afro-XLM-R as a bi-encoder. Then, for each Amharic infobox field at
				inference time, the field is encoded with the same model and cosine similarity selects
				the 10 most semantically similar DBpedia property labels from the full vocabulary.
			</p>
			<p class="mt-4">
				The LLM then receives only these 10 candidates rather than all 595. This is the
				retrieve-then-rerank architecture, and the benefit is significant in two directions. First,
				accuracy improves because the LLM no longer has to reason over an enormous vocabulary —
				it only has to pick the best match from a pre-filtered shortlist where the correct answer
				is likely present. Second, consistency improves because the LLM's decision space is
				constrained, reducing the chance of the model hallucinating a property label that is not
				in the vocabulary at all.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Few-shot prompting with retrieved candidates
			</h2>
			<p class="mt-5">
				The retrieved top-10 candidates served a dual purpose this week: they were both the
				shortlist for the LLM to choose from and the demonstration pool for few-shot examples.
				Rather than drawing few-shot examples randomly from the training set, the pipeline selects
				examples whose ground-truth labels are similar to the current query's top-10 retrieved
				candidates. This ensures that the few-shot demonstrations are relevant to the specific
				disambiguation problem at hand.
			</p>
			<p class="mt-4">
				The Kaggle results from this approach showed meaningful accuracy gains with 1 to 3
				demonstrations compared to zero-shot. Moving from zero-shot to one example typically gave
				the largest single gain, with diminishing returns at higher shot counts. This validated
				the retrieve-then-rerank architecture as the right foundation and established the few-shot
				demonstration strategy that would carry forward into the formal benchmark in Week 11.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Results documented
			</h2>
			<p class="mt-5">
				The full pipeline — Afro-XLM-R retriever, top-10 shortlist, few-shot LLM — was run on
				the Kaggle server and all results were documented systematically. Accuracy figures were
				recorded per model and per shot count (0 through 5), and for each configuration the run
				was repeated with multiple random seeds to compute the standard deviation. This careful
				documentation practice became the template for all subsequent experiments: every result
				recorded includes the model, the prompt strategy, the shot count, the mean accuracy, and
				the standard deviation. Without this structure, comparing results across weeks would be
				impossible.
			</p>
			<p class="mt-4">
				The documented results from this week became the baseline for the multi-model benchmark
				in Issue #3, which would run on the H100 server in Week 11. Every subsequent experiment
				was designed to either beat this baseline or explain why it could not be beaten.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-7#${link.slug}`}
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
					WEEK 7
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-7#${node.slug}`}
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
