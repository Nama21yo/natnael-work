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
			GSoC 2026 Week 7
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			The pipeline matured a lot this week. I added a top-10 dense retrieval layer before the LLM,
			turning it into a proper retrieve-then-rerank system, and studied LLM consistency metrics to
			understand how much to trust the results.
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
				Week 7 turned the pipeline from a prototype into a system with a principled architecture.
				Adding dense retrieval before the LLM reasoning step is the defining architectural decision
				so far: it constrains the LLM's search space to a manageable shortlist, which improves both
				accuracy and consistency by a lot. I also built a reliability evaluation framework this
				week, because knowing a model's average accuracy isn't enough — knowing how much that
				accuracy varies across runs is what actually makes it trustworthy.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				LLM reliability evaluation metrics
			</h2>
			<p class="mt-5">
				Before I could trust the Week 6 results, I had to answer a basic question: do LLMs give the
				same answer when asked the same question twice? No — LLM outputs are stochastic, so accuracy
				figures from a single run are noisy estimates. This week I built a systematic reliability
				evaluation framework around three core metrics.
			</p>
			<p class="mt-4">
				Self-consistency is the simplest one: run the same prompt N times on the same input and
				measure what fraction of runs agree. A model that returns the same answer 9 out of 10 times
				is far more reliable than one that agrees with itself 6 out of 10, even at similar mean
				accuracy. Variance across random seeds captures a related but different concern — whether
				accuracy changes meaningfully when the seed changes. And standard deviation of accuracy
				across runs gives a single number for how much to trust a reported mean: a model at 60%
				accuracy with ±1 pp standard deviation is practically more valuable than one at 62% with ±8
				pp, since the latter's real performance could plausibly be anywhere between 54% and 70%.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Retrieve-then-rerank pipeline
			</h2>
			<p class="mt-5">
				The core architectural change this week was adding a top-10 retrieval step using Afro-XLM-R
				dense embeddings before every LLM call. The pipeline now works in two stages. First, I
				pre-encode all 595 DBpedia property labels into a matrix of dense vectors using Afro-XLM-R
				as a bi-encoder. Then, for each Amharic infobox field at inference time, I encode the field
				with the same model and use cosine similarity to select the 10 most semantically similar
				DBpedia property labels from the full vocabulary.
			</p>
			<p class="mt-4">
				The LLM then only sees these 10 candidates instead of all 595. This retrieve-then-rerank
				architecture helps in two ways. Accuracy improves because the LLM isn't reasoning over an
				enormous vocabulary anymore — it just has to pick the best match from a pre-filtered
				shortlist where the correct answer is usually present. And consistency improves because the
				LLM's decision space is constrained, which cuts down on the model hallucinating a property
				label that isn't in the vocabulary at all.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Few-shot prompting with retrieved candidates
			</h2>
			<p class="mt-5">
				The retrieved top-10 candidates did double duty this week: they're both the shortlist for
				the LLM to choose from and the demonstration pool for few-shot examples. Instead of drawing
				few-shot examples randomly from the training set, I select examples whose ground-truth
				labels are similar to the current query's top-10 retrieved candidates, so the demonstrations
				are relevant to the specific disambiguation problem at hand.
			</p>
			<p class="mt-4">
				The Kaggle results showed meaningful accuracy gains with 1 to 3 demonstrations over
				zero-shot. Moving from zero-shot to one example gave the largest single gain, with
				diminishing returns after that. This confirmed retrieve-then-rerank as the right foundation
				and settled the few-shot demonstration strategy I'd carry into the formal benchmark in Week
				11.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Results documented
			</h2>
			<p class="mt-5">
				I ran the full pipeline — Afro-XLM-R retriever, top-10 shortlist, few-shot LLM — on the
				Kaggle server and documented all results systematically: accuracy per model and per shot
				count (0 through 5), each configuration repeated with multiple random seeds to compute the
				standard deviation. This documentation practice became the template for every experiment
				after it — model, prompt strategy, shot count, mean accuracy, standard deviation, every
				time. Without that structure, comparing results across weeks would've been impossible.
			</p>
			<p class="mt-4">
				This week's results became the baseline for the multi-model benchmark in Issue #3, which
				would run on the H100 server in Week 11. Every experiment after this was designed to either
				beat this baseline or explain why it couldn't.
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
