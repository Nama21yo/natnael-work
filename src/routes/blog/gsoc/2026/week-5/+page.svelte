<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "Website PR #1",
			slug: "website-pr-1",
			href: "https://github.com/AmharicDBpedia/amharicdbpedia.github.io/pull/1"
		},
		{
			label: "LaBSE",
			slug: "labse",
			href: "https://huggingface.co/sentence-transformers/LaBSE"
		},
		{
			label: "DSPy",
			slug: "dspy",
			href: "https://dspy.ai"
		},
		{
			label: "GEPA paper",
			slug: "gepa-paper",
			href: "https://arxiv.org/pdf/2507.19457"
		},
		{
			label: "MCP",
			slug: "mcp",
			href: "https://modelcontextprotocol.io"
		}
	];

	const models = [
		{
			name: "LaBSE",
			href: "https://huggingface.co/sentence-transformers/LaBSE",
			body: "Google's Language-Agnostic BERT Sentence Embedding model supports 109 languages including Amharic. Unlike standard masked LM pretraining, LaBSE is trained on bilingual sentence pairs, teaching the model to produce similar vector representations for sentences with the same meaning in different languages. This cross-lingual transfer is precisely what is needed for matching Amharic infobox fields to English DBpedia property labels."
		},
		{
			name: "multilingual-e5-large",
			href: "https://huggingface.co/intfloat/multilingual-e5-large",
			body: "Built on XLM-RoBERTa-large, multilingual-e5-large uses a shared cross-lingual vector space that aligns representations from different languages into the same mathematical space. The result is that semantically equivalent phrases in Amharic and English land near each other in the embedding space, giving the model genuine multilingual semantic understanding rather than simple token-level overlap."
		},
		{
			name: "bge-m3",
			href: "https://huggingface.co/BAAI/bge-m3",
			body: "The M3 in bge-m3 stands for Multi-Lingual, Multi-Functionality, Multi-Granularity. Developed by BAAI, it supports over 100 languages including Amharic and is trained on large cross-lingual corpora covering many domains. The multi-granularity design means it can produce embeddings at the token, sentence, or passage level, giving flexibility in how retrieval is structured."
		},
		{
			name: "am-roberta",
			href: "https://huggingface.co/uhhlt/am-roberta",
			body: "A monolingual Amharic RoBERTa model trained specifically on Amharic text, making it excellent at understanding the morphological richness and token structure of the Ge'ez script. The limitation is significant for this project: being monolingual, it cannot directly bridge Amharic inputs to English DBpedia property labels without an additional cross-lingual step. It remains valuable as a reference model for Amharic-only understanding tasks."
		},
		{
			name: "jina-embeddings-v3",
			href: "https://huggingface.co/jinaai/jina-embeddings-v3",
			body: "Jina AI's embedding model supports 89 languages including Amharic and uses LoRA adapters under the hood for GPU-efficient fine-tuning and inference. The LoRA design means the base model can be adapted to specific retrieval tasks with a fraction of the usual compute, which is an attractive property for a project that may eventually want task-specific fine-tuning."
		},
		{
			name: "gte-multilingual-base",
			href: "https://huggingface.co/Alibaba-NLP/gte-multilingual-base",
			body: "At the time of evaluation, gte-multilingual-base sat at the top of the HuggingFace MTEB multilingual retrieval leaderboard. Developed by Alibaba NLP, it is trained using contrastive learning — a technique that directly optimises the model to pull semantically similar sentence pairs together and push dissimilar pairs apart in the vector space, which is ideal for retrieval-based property mapping."
		}
	];

	const mindMapNodes = [
		{ label: "Website PR", slug: "website-pr-1", angle: 270 },
		{ label: "LaBSE", slug: "labse", angle: 342 },
		{ label: "DSPy", slug: "dspy", angle: 54 },
		{ label: "GEPA", slug: "gepa-paper", angle: 126 },
		{ label: "MCP", slug: "mcp", angle: 198 }
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
				class="block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground"
			>
				Week 4
			</a>
			<a
				href={`${base}/blog/gsoc/2026/week-5`}
				class="block rounded-xl bg-cyan/15 px-3 py-2 text-sm font-bold text-cyan"
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
				GSoC 2026 Week 5: Website PR, Model Selection, and Prompt Engineering
			</h1>
			<p class="mt-5 text-base leading-8 text-muted-foreground">
				A research-heavy week. The website received its first major PR, six candidate embedding
				models were evaluated, and prompt engineering literature was studied systematically —
				ending with DSPy as a practical framework.
			</p>
			<div class="mt-5 flex flex-wrap gap-2">
				<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
					>#gsoc-2026</span
				>
				<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
					>#week-5</span
				>
				<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
					>#website</span
				>
				<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
					>#models</span
				>
				<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
					>#prompt-engineering</span
				>
				<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
					>#dspy</span
				>
			</div>
		</div>

		<div class="prose-obsidian mt-8 space-y-10">
			<section>
				<h2
					class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
				>
					<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
						5
					</span>
					Jun 19, 2026 – Jun 26, 2026
				</h2>
				<p class="mt-5">
					Week 5 was the most research-intensive week of the project so far. Rather than a single
					dominant task, it spread across three areas that all needed to advance together: the
					website got its first major PR, the model shortlist was evaluated with enough rigour to
					narrow down to serious candidates, and the prompt engineering literature was read
					systematically enough to produce a working theory of which techniques would matter most for
					property mapping. The week ended with DSPy as a clear practical direction.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">Website PR #1</h2>
				<p class="mt-5">
					The Amharic DBpedia website was refactored and the changes were submitted as
					<a
						href="https://github.com/AmharicDBpedia/amharicdbpedia.github.io/pull/1"
						target="_blank"
						rel="noreferrer"
						class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
					>PR #1</a
					>. The refactor focused on cleaner component structure — breaking down the monolithic page
					into smaller, reusable Svelte components — and improved data presentation so that the
					statistics and entity counts introduced in Week 4 displayed correctly across screen sizes.
					Submitting a real PR rather than pushing directly to the main branch also established the
					review workflow that all future website contributions would follow.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
					Six models evaluated for Amharic property mapping
				</h2>
				<p class="mt-5">
					The shortlist from Week 4 was expanded and evaluated more carefully this week. Each model
					was assessed on three criteria: documented Amharic support, cross-lingual retrieval
					capability, and practical deployability without API access. The six candidates and the
					reasoning behind each:
				</p>
				<div class="mt-5 space-y-5">
					{#each models as model (model.name)}
						<div class="rounded-2xl border border-foreground/10 bg-background/70 p-5">
							<h3 class="font-mono text-xl font-black">
								<a
									href={model.href}
									target="_blank"
									rel="noreferrer"
									class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
								>{model.name}</a>
							</h3>
							<p class="mt-3">{model.body}</p>
						</div>
					{/each}
				</div>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
					Prompt engineering deep-dive
				</h2>
				<p class="mt-5">
					A systematic study of prompt engineering techniques produced a working taxonomy. Starting
					with the anatomy of a well-formed prompt — role, context, instruction, examples, output
					format — the reading then covered zero-shot (instruction only, no examples), one-shot (one
					example), and few-shot (multiple examples) prompting, and why adding examples generally
					helps LLMs produce more consistent outputs on narrow classification tasks.
				</p>
				<p class="mt-4">
					The more advanced techniques were equally important. Chain-of-thought (CoT) prompting asks
					the model to reason step-by-step before answering, which improves accuracy on tasks that
					require multi-step inference — like explaining why an Amharic phrase maps to a specific
					DBpedia property. Tree-of-thoughts (ToT) extends this by exploring multiple reasoning
					branches simultaneously. Chain-of-drafts is a more recent and more efficient variant that
					produces short intermediate drafts rather than full reasoning chains. Finally, ReAct —
					Think, Act, Observe — combines reasoning with tool calls, which is directly applicable to
					an agent workflow where the LLM can call a retrieval tool to look up candidate properties
					before making a decision.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">GEPA paper</h2>
				<p class="mt-5">
					The abstract and introduction of the GEPA paper (<a
						href="https://arxiv.org/pdf/2507.19457"
						target="_blank"
						rel="noreferrer"
						class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
					>Generative Evolutionary Prompt Adaptation</a
					>) were read this week. The core insight is that instead of updating model weights through
					fine-tuning — an expensive process that requires labelled data and GPU time — it is
					possible to build a system that automatically rewrites and optimises the system prompt
					itself. The evolutionary framing means the prompt is treated like a candidate solution in
					an optimisation loop: generate variants, score them against a held-out metric, keep the
					best, repeat. This is directly relevant to the property mapping problem because hand-tuning
					prompts is brittle and time-consuming.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">DSPy</h2>
				<p class="mt-5">
					The week's most important discovery was
					<a
						href="https://dspy.ai"
						target="_blank"
						rel="noreferrer"
						class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
					>DSPy</a
					>, the Stanford library for programming with language models. The DSPy model is elegant:
					instead of hand-crafting prompt text, you write a Python program that describes the
					logical structure of what you want the LLM to do (e.g. "given an Amharic mention and a
					list of candidate DBpedia properties, choose the best one"), provide a scoring metric (e.g.
					exact-match accuracy on a labelled dataset), and DSPy automatically iterates, mutates, and
					optimises the exact wording of the prompt for you. The optimiser is essentially doing
					programmatic prompt engineering at scale. For the property mapping task, this meant it was
					now possible to find good prompts without guessing.
				</p>
			</section>

			<section>
				<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
					MCP for dbpedia-mapper
				</h2>
				<p class="mt-5">
					An MCP (Model Context Protocol) server was implemented for the DBpedia mapper, exposing
					two initial tool functions that make the mapper callable from LLM agents. The Model Context
					Protocol is an open standard for connecting LLM agents to external tools and data sources,
					and implementing it for the mapper means the property matching logic can be invoked by any
					MCP-compatible agent framework — including the LangGraph workflow from Week 4. The two
					initial tools covered property retrieval and mapping suggestion, giving the agent a clean
					interface that separates the retrieval concern from the reasoning concern.
				</p>
			</section>
		</div>
	</article>

	<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
		<p class="blog-label">Backlinks</p>
		<div class="mt-4 space-y-3">
			{#each wikiLinks as link (link.slug)}
				<ZettelLink
					href={link.href ?? `${base}/blog/gsoc/2026/week-5#${link.slug}`}
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
						WEEK 5
					</div>
				</div>

				{#each mindMapNodes as node (node.slug)}
					{@const rad = (node.angle * Math.PI) / 180}
					{@const cx = 130 + 85 * Math.cos(rad)}
					{@const cy = 128 + 85 * Math.sin(rad)}
					<a
						href={`${base}/blog/gsoc/2026/week-5#${node.slug}`}
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
