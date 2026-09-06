<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "Kaggle notebook",
			slug: "kaggle-notebook",
			href: "https://www.kaggle.com/code/natnaelyohanes/multi-llm-benchmarking-v3"
		},
		{
			label: "amseg",
			slug: "amseg",
			href: "https://pypi.org/project/amseg/"
		},
		{
			label: "MTEB leaderboard",
			slug: "mteb-leaderboard",
			href: "https://huggingface.co/spaces/mteb/leaderboard"
		},
		{
			label: "Afro-XLM-R",
			slug: "afro-xlmr",
			href: "https://huggingface.co/Davlan/afro-xlmr-base"
		}
	];

	const mindMapNodes = [
		{ label: "Kaggle", slug: "kaggle-notebook", angle: 270 },
		{ label: "amseg", slug: "amseg", angle: 342 },
		{ label: "Prompt Papers", slug: "mteb-leaderboard", angle: 54 },
		{ label: "Server Request", slug: "afro-xlmr", angle: 126 },
		{ label: "Fuzzy Search", slug: "amseg", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 6: First GPU Benchmarks on Kaggle and Fuzzy Amharic Search
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			The first real benchmarking results arrived this week. Models were run on Kaggle GPU,
			prompt strategies were tested side-by-side, and a specialised Amharic-aware fuzzy search
			was introduced to handle the language's phonetic variation.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-6</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#benchmarking</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#kaggle</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#prompt-engineering</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#fuzzy-search</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					6
				</span>
				Jun 26, 2026 – Jul 3, 2026
			</h2>
			<p class="mt-5">
				Week 6 produced the first concrete numbers. Rather than reading about models and
				techniques, this week was about running them and measuring. Kaggle's free GPU tier made it
				possible to execute the first benchmarking suite without dedicated compute, multiple prompt
				strategies were compared head-to-head on a single model, and a critical problem with exact
				string matching in Amharic was solved by introducing a language-aware fuzzy search
				approach.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Reading the prompt engineering papers
			</h2>
			<p class="mt-5">
				The prompt engineering survey from Week 5 was grounded this week by going back to the
				primary papers. Reading the original chain-of-thought paper, the tree-of-thoughts paper,
				and the few-shot learning work made the trade-offs far clearer than any blog summary could.
				The key insight that emerged was the relationship between task complexity and the benefit of
				reasoning steps: simple retrieval tasks get little from CoT, but tasks requiring
				disambiguation between similar properties (e.g. dbo:country vs. dbo:nationality vs.
				dbo:birthPlace) benefit significantly from having the model articulate why it is choosing
				one property over another.
			</p>
			<p class="mt-4">
				Chain-of-drafts also got serious attention this week. The claim in the original paper —
				that producing short intermediate drafts rather than full reasoning chains gives most of
				the accuracy benefit at a fraction of the token cost — is directly relevant to a
				production pipeline where inference latency and token budget both matter.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Deep dive into the four multilingual models
			</h2>
			<p class="mt-5">
				The four most promising models from the Week 5 shortlist — LaBSE, multilingual-e5-large,
				bge-m3, and gte-multilingual-base — were studied in technical depth this week. The focus
				was on exactly how each model handles cross-lingual alignment. LaBSE uses bilingual
				sentence pairs as direct training signal, which means the alignment is explicitly
				supervised. The XLM-RoBERTa-based models (multilingual-e5-large, bge-m3) learn a shared
				representation space through masked language modelling across many languages simultaneously,
				which gives broader coverage but potentially less precise cross-lingual transfer. The
				gte-multilingual-base architecture uses contrastive learning at the sentence level, which
				more directly optimises for retrieval quality than masked LM does.
			</p>
			<p class="mt-4">
				For Amharic specifically, the Ge'ez script presents a challenge for models trained
				primarily on Latin-script languages: tokenisation, character coverage, and morphological
				richness all behave differently. Understanding each model's tokeniser and its Amharic
				vocabulary size became part of the evaluation criteria this week.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Kaggle GPU benchmarking
			</h2>
			<p class="mt-5">
				The full multilingual LLM benchmarking suite ran for the first time on a Kaggle GPU,
				captured in the
				<a
					href="https://www.kaggle.com/code/natnaelyohanes/multi-llm-benchmarking-v3"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>multi-llm-benchmarking-v3 notebook</a
				>. The pipeline was implemented as a structured Kaggle notebook that loads the labelled
				Amharic property mapping dataset, encodes the DBpedia property labels with the chosen
				retriever, and for each test example retrieves the top-10 candidates and passes them to an
				LLM with varying prompts. Multiple prompt strategies — zero-shot, one-shot, few-shot — were
				tested on a single model in one run, and the accuracy was recorded for each combination.
			</p>
			<p class="mt-4">
				Kaggle's environment has constraints — session time limits, memory caps, no persistent
				background processes — that made some experiment configurations impractical. But for the
				first round of benchmarking, it was exactly sufficient. The results were the first
				concrete evidence of which prompt strategies work and which do not, moving the project
				from informed speculation to measured fact.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Server access request
			</h2>
			<p class="mt-5">
				To run the full benchmark suite — multiple models across multiple prompt strategies, with
				multiple random seeds for statistical reliability — Kaggle's free tier was not sufficient.
				An access request for a dedicated GPU server was submitted through Mentor Tilahun,
				specifying a minimum of 48 GB VRAM. The justification was straightforward: the largest
				model on the candidate list (Qwen 2.5 32B) requires approximately 20 GB in float16, and
				running five models at six shot settings with three seeds each is a combinatorial
				workload that needs sustained GPU compute rather than 12-hour session windows.
			</p>
			<p class="mt-4">
				The server request would eventually unlock H100 access in Week 11, but this week was about
				preparing the formal justification that the cluster administrators would require before
				granting access.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Fuzzy search with Amharic normalisation
			</h2>
			<p class="mt-5">
				Exact string matching proved inadequate for Amharic evaluation this week, which led to the
				introduction of fuzzy search with a custom Amharic normalisation pre-processing step. The
				problem is fundamental to the Ge'ez writing system: many sounds that are phonetically
				identical are written with different characters across the seven vowel forms (ሀ, ሃ, ሄ, ህ, ሆ,
				and others all represent related sounds), and different writers or Wikipedia editors may use
				any of these interchangeably for the same word.
			</p>
			<p class="mt-4">
				The solution was
				<a
					href="https://pypi.org/project/amseg/"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>amseg</a
				>, a Python library for Amharic text normalisation that handles Ge'ez homophone
				normalisation as a pre-processing step. Before any string comparison, both the predicted
				label and the ground-truth label pass through amseg's normaliser, which collapses
				phonetically equivalent character variants to a canonical form. This single change
				significantly improved evaluation accuracy figures by ensuring that a model giving a
				correct-sounding answer was not penalised for a different but phonetically identical
				spelling. Without normalisation, exact string matching missed a large fraction of genuinely
				correct answers.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-6#${link.slug}`}
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
					WEEK 6
				</div>
			</div>

			{#each mindMapNodes as node (node.slug)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-6#${node.slug}`}
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
