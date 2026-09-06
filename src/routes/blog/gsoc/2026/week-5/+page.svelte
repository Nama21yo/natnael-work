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

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 5
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			A research-heavy week. I got the website's first major PR merged, evaluated six candidate
			embedding models, and worked through the prompt engineering literature systematically —
			landing on DSPy as a practical framework.
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
				Week 5 was the most research-heavy week so far. There wasn't one dominant task — I had to
				move three things forward at once: get the website's first major PR through, evaluate the
				model shortlist rigorously enough to narrow it to serious candidates, and read the prompt
				engineering literature closely enough to have a real opinion on which techniques would
				matter most for property mapping. By the end of the week DSPy had become the clear practical
				direction.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">Website PR #1</h2>
			<p class="mt-5">
				I refactored the Amharic DBpedia website and submitted the changes as
				<a
					href="https://github.com/AmharicDBpedia/amharicdbpedia.github.io/pull/1"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>PR #1</a
				>. The refactor broke the monolithic page down into smaller, reusable Svelte components and
				fixed the data presentation so the statistics and entity counts from Week 4 displayed
				correctly across screen sizes. Submitting a real PR instead of pushing straight to main also
				set the review workflow every later website contribution would follow.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Six models evaluated for Amharic property mapping
			</h2>
			<p class="mt-5">
				I expanded the Week 4 shortlist and evaluated it more carefully, checking each model against
				three criteria: documented Amharic support, cross-lingual retrieval capability, and whether
				it's practical to run without API access. Here are the six candidates and the reasoning
				behind each:
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
				I worked through the prompt engineering literature systematically and came out with a
				working taxonomy. Starting from the anatomy of a well-formed prompt — role, context,
				instruction, examples, output format — I covered zero-shot (instruction only), one-shot, and
				few-shot prompting, and why adding examples generally helps LLMs give more consistent
				answers on narrow classification tasks.
			</p>
			<p class="mt-4">
				The more advanced techniques mattered just as much. Chain-of-thought (CoT) asks the model to
				reason step-by-step before answering, which helps on tasks needing multi-step inference —
				like explaining why an Amharic phrase maps to a specific DBpedia property. Tree-of-thoughts
				(ToT) extends that by exploring several reasoning branches at once. Chain-of-drafts is a
				newer, cheaper variant that produces short intermediate drafts instead of full reasoning
				chains. And ReAct — Think, Act, Observe — combines reasoning with tool calls, which maps
				directly onto an agent workflow where the LLM can call a retrieval tool before deciding.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">GEPA paper</h2>
			<p class="mt-5">
				I read the abstract and introduction of the GEPA paper (<a
					href="https://arxiv.org/pdf/2507.19457"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>Generative Evolutionary Prompt Adaptation</a
				>). The core idea: instead of fine-tuning model weights — expensive, needs labelled data and
				GPU time — you can build a system that automatically rewrites and optimizes the prompt
				itself. It treats the prompt like a candidate solution in an optimization loop: generate
				variants, score them against a held-out metric, keep the best, repeat. Directly relevant
				here, since hand-tuning prompts is brittle and slow.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">DSPy</h2>
			<p class="mt-5">
				The most important discovery of the week was
				<a
					href="https://dspy.ai"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>DSPy</a
				>, Stanford's library for programming with language models. The model is elegant: instead
				of hand-crafting prompt text, you write a Python program describing the logical structure of
				what you want the LLM to do ("given an Amharic mention and a list of candidate DBpedia
				properties, choose the best one"), give it a scoring metric (exact-match accuracy on a
				labelled dataset), and DSPy iterates, mutates, and optimizes the exact prompt wording for
				you — programmatic prompt engineering at scale. For property mapping, that meant I could
				find good prompts without guessing.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				MCP for dbpedia-mapper
			</h2>
			<p class="mt-5">
				I implemented an MCP (Model Context Protocol) server for the DBpedia mapper, exposing two
				initial tool functions that make it callable from LLM agents. MCP is an open standard for
				connecting LLM agents to external tools and data sources, so wiring it up here means the
				property matching logic can be invoked by any MCP-compatible agent framework — including the
				LangGraph workflow from Week 4. The two tools covered property retrieval and mapping
				suggestion, keeping the retrieval concern separate from the reasoning concern.
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
					WEEK 5
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
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
