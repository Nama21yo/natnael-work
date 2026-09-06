<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "SLURM workload manager",
			slug: "slurm",
			href: "https://slurm.schedmd.com/"
		},
		{
			label: "Afro-XLM-R",
			slug: "afro-xlmr",
			href: "https://huggingface.co/Davlan/afro-xlmr-base"
		},
		{
			label: "Ollama",
			slug: "ollama",
			href: "https://ollama.com/"
		},
		{
			label: "DSPy",
			slug: "dspy",
			href: "https://dspy.ai/"
		},
		{
			label: "LLMIntegration (GitHub)",
			slug: "llm-integration",
			href: "https://github.com/AmharicDBpedia/LLMIntegration"
		}
	];

	const benchmarkModels = [
		{
			name: "Qwen 2.5 (7B)",
			role: "Primary LLM for few-shot classification",
			notes: "Strong instruction following; evaluated at 0-, 1-, 3-, 5-, and 8-shot settings."
		},
		{
			name: "Llama 3.1 (8B)",
			role: "Secondary LLM baseline",
			notes: "Good zero-shot performance as a comparison anchor for the Qwen results."
		},
		{
			name: "Gemma 2 (9B)",
			role: "Multilingual capability probe",
			notes: "Evaluated for its ability to handle Ge'ez script directly without translation."
		},
		{
			name: "Afro-XLM-R",
			role: "Dense retriever (encoder)",
			notes:
				"Encodes all 595 DBpedia property labels and the query mention into 768-d vectors; cosine similarity selects the top-10 candidates."
		}
	];

	const issue3Highlights = [
		{
			title: "Configurable model registry",
			body: "Issue #3 introduced a YAML-based model registry so experiments can specify any combination of retriever and LLM without modifying Python source. A single config file controls which models are loaded, what shot counts to evaluate, how many random seeds to run, and where to write results."
		},
		{
			title: "Afro-XLM-R as the retrieval backbone",
			body: "The retriever encodes all 595 canonical DBpedia property labels into a fixed index at startup. For each test example, it encodes the Amharic property mention and computes cosine similarities, returning the top-10 most similar labels as candidates for the LLM stage."
		},
		{
			title: "Answer snapping with RapidFuzz",
			body: "LLM outputs do not always exactly match a valid property label. Answer snapping normalises the LLM response using RapidFuzz token_sort_ratio ≥ 85, falling back to the top-1 retriever result if no candidate exceeds the threshold. This made accuracy metrics stable and comparable across models."
		},
		{
			title: "Fuzzy accuracy metric",
			body: "The evaluation metric lowercases, collapses whitespace, applies Amharic homophone normalisation via amseg, then checks for exact match or fuzz score ≥ 85 between the predicted label and the ground truth. All 279 test examples are evaluated on each seed and the results are averaged."
		}
	];

	const mindMapNodes = [
		{ label: "H100 server", slug: "slurm", angle: 270 },
		{ label: "Issue #3", slug: "llm-integration", angle: 342 },
		{ label: "Afro-XLM-R", slug: "afro-xlmr", angle: 54 },
		{ label: "First results", slug: "dspy", angle: 126 },
		{ label: "5+ mappings", slug: "ollama", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 11: H100 Access, Issue #3 Benchmark, and First Results
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			The project's most compute-intensive phase began this week when H100 GPU server access was
			granted via SLURM. Issue #3 — a configurable multi-model benchmark using Afro-XLM-R
			retrieval paired with local LLMs — was fully implemented, five new Amharic template
			mappings were added, and the first benchmark results CSV came out of the pipeline.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-11</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#h100</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#benchmark</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#afro-xlmr</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span
					class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan"
				>
					11
				</span>
				Jul 31, 2026 – Aug 7, 2026
			</h2>
			<p class="mt-5">
				The earlier benchmarking work — run on Kaggle's free GPU tier — had established which
				models were worth investigating, but Kaggle's session time limits and notebook format made
				it difficult to run systematic multi-seed evaluations. Week 11 changed that: server access
				to a SLURM cluster with NVIDIA H100 GPUs was approved, giving the project the ability to
				run long multi-model, multi-seed evaluations and serve large local LLMs via Ollama without
				session interruptions. The benchmark infrastructure built this week became the foundation
				for Issues #3, #4, and #5.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Getting on the H100 cluster
			</h2>
			<p class="mt-5">
				The server runs SLURM — the standard workload manager for HPC clusters — and GPU
				allocation is requested with an interactive job submission. The command used to acquire a
				GPU for development and benchmarking sessions was:
			</p>
			<pre
				class="mt-4 overflow-x-auto rounded-xl border border-cyan/15 bg-zinc-950 p-4 text-xs leading-6 text-cyan/90"><code>salloc --partition=capella --gpus=h100:1 --time=04:00:00</code></pre>
			<p class="mt-4">
				Once on a node, Ollama was started as a background process and models were pulled from the
				Ollama registry. The key insight was that Ollama's local API — identical in interface to a
				remote API call — meant that the DSPy-based benchmark code written for Kaggle required
				almost no modification to run on the cluster. The only change was pointing the LM
				configuration at the local Ollama endpoint rather than a public API.
			</p>
			<p class="mt-4">
				Having an H100 also meant that larger models became accessible. Qwen 2.5 at 7B parameters
				had been the workhorse on Kaggle; the H100 allowed evaluation of the 72B parameter
				variant, which produced noticeably better zero-shot accuracy on Amharic property mention
				classification.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Issue #3: the baseline benchmark
			</h2>
			<p class="mt-5">
				Issue #3 in the LLMIntegration repository defined the first formal experiment: a
				configurable multi-model benchmark that measures how accurately a retrieve-then-rerank
				pipeline can map Amharic property mentions to canonical DBpedia property labels. The
				dataset is <code>dice-research/amharic-property-mapping</code> — 2,261 training examples,
				251 validation examples, and 279 test examples. The task, for each example, is: given an
				Amharic string like <span class="font-mono text-cyan">«ሙዚቃ ቡድን»</span> in the context of a musician entity,
				select the correct DBpedia property from a set of 595 candidates.
			</p>
			<div class="mt-5 space-y-4">
				{#each issue3Highlights as highlight (highlight.title)}
					<div class="rounded-2xl border border-foreground/10 bg-background/70 p-5">
						<h3 class="font-mono text-base font-black">{highlight.title}</h3>
						<p class="mt-3 text-sm leading-7">{highlight.body}</p>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Models evaluated in Issue #3
			</h2>
			<p class="mt-5">
				The benchmark was designed to be model-agnostic. Any LLM accessible via Ollama and any
				sentence-transformer model accessible via HuggingFace could be plugged in by changing the
				config file. For the baseline experiment, four models were evaluated:
			</p>
			<div class="mt-5 grid gap-4 md:grid-cols-2">
				{#each benchmarkModels as model (model.name)}
					<div class="rounded-2xl border border-cyan/15 bg-cyan/5 p-4">
						<h3 class="font-mono text-sm font-black text-cyan">{model.name}</h3>
						<p class="mt-1 text-xs font-bold text-muted-foreground">{model.role}</p>
						<p class="mt-2 text-xs leading-5">{model.notes}</p>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				First results CSV
			</h2>
			<p class="mt-5">
				By the end of the week, the pipeline had run successfully across all shot settings (0, 1,
				3, 5, 8) for both primary LLMs, with three seeds per setting to measure variance. The
				output was a CSV file per model containing accuracy, standard deviation, and per-example
				predictions. Key findings from the first run:
			</p>
			<ul class="mt-4 space-y-2 text-sm leading-7">
				<li class="flex items-start gap-2">
					<span class="mt-1.5 h-1.5 w-1.5 shrink-0 rounded-full bg-cyan"></span>
					<span>The pure retriever (Afro-XLM-R top-1) achieved approximately 52% accuracy on the 279-example test set, establishing the retrieval-only floor.</span>
				</li>
				<li class="flex items-start gap-2">
					<span class="mt-1.5 h-1.5 w-1.5 shrink-0 rounded-full bg-cyan"></span>
					<span>Adding the LLM reranker on top of the top-10 retrieval shortlist pushed accuracy to the 58–62% range at zero-shot, confirming that the two-stage design beats retrieval alone.</span>
				</li>
				<li class="flex items-start gap-2">
					<span class="mt-1.5 h-1.5 w-1.5 shrink-0 rounded-full bg-cyan"></span>
					<span>Few-shot examples (3–5 demonstrations) improved accuracy by roughly 3–5 percentage points over zero-shot, with diminishing returns beyond 5 shots.</span>
				</li>
				<li class="flex items-start gap-2">
					<span class="mt-1.5 h-1.5 w-1.5 shrink-0 rounded-full bg-cyan"></span>
					<span>Variance across seeds was low (&lt;1 percentage point standard deviation), indicating that the shot sampling strategy was stable enough for reliable comparisons.</span>
				</li>
			</ul>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Five new template mappings
			</h2>
			<p class="mt-5">
				Alongside the benchmarking infrastructure work, five new Amharic template mappings were
				added to the DBpedia mappings repository. The templates were chosen based on the SPARQL
				coverage audit from Week 10, which identified entity types that appeared frequently in
				Amharic Wikipedia but had incomplete or missing mapping coverage. Each new mapping was
				tested against a real Amharic Wikipedia article using the extraction framework to confirm
				that the properties were correctly serialised into RDF triples before the mapping was
				submitted.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-11#${link.slug}`}
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
					WEEK 11
				</div>
			</div>

			{#each mindMapNodes as node (node.slug)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-11#${node.slug}`}
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
