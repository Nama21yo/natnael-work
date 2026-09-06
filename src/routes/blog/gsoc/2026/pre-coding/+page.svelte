<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{ label: "GSoC 2026", slug: "gsoc-2026", href: "https://summerofcode.withgoogle.com/programs/2026/organizations/dbpedia" },
		{ label: "DBpedia Paper 1", slug: "dbpedia-paper-1", href: "https://journals.sagepub.com/doi/epdf/10.3233/SW-140134" },
		{ label: "DBpedia Paper 2", slug: "dbpedia-paper-2", href: "https://link.springer.com/chapter/10.1007/978-3-540-76298-0_52" },
		{ label: "XLM-R, Afro-XLM-R, and BERT", slug: "transformers", href: "https://arxiv.org/pdf/2204.06487" },
		{ label: "repo", slug: "amharic-dbpedia-repo", href: "https://github.com/Amharic-DBpedia/AmharicDBpediaChapter" },
		{ label: "Link", slug: "amharic-dbpedia-paper", href: "https://drive.google.com/file/d/1_ZjC5mQ7WsquwVaXXjQqeI5zBXxHa4lq/view?usp=sharing" },
		{ label: "Kaggle", slug: "kaggle-benchmark", href: "https://www.kaggle.com/code/natnaelyohanes/am-dbpedia-ontology-alignment" },
		{ label: "Notes", slug: "lora", href: "https://arxiv.org/pdf/2106.09685" },
		{ label: "Docs", slug: "langgraph", href: "https://langchain-ai.github.io/langgraph/" }
	];

	const bodyParagraphsPeriod1 = [
		"Read two papers shared by Hizkeal on DBpedia ([[DBpedia Paper 1]], [[DBpedia Paper 2]]).",
		"Read the three papers on [[XLM-R, Afro-XLM-R, and BERT]].",
		"Started refactoring the Amharic Dbpedia Website locally with vanilla Typescript ([[repo]]).",
		"Participated in the DBpedia GSoC 2026 meeting on May 22, 2026. Although the session was shorter than anticipated, the mentors were highly collaborative and provided clear, direct insights.",
		"Gained a comprehensive understanding of the other contributors' project scopes.",
		"Documented the progress achieved throughout the week in a formal report."
	];

	const bodyParagraphsPeriod0 = [
		"I had my first onboarding meeting on Thursday with my GSOC Mentors (Dr. Richard, Hizkeal, Tilahun, and Andargachew). We introduced ourselves to each other and decided to have our meeting be on Fridays at 2:00 PM.",
		"They also gave me to checkout the current Models that being used for Ontology Alignment and property retrieval on the Amharic DBpedia.",
		"Joined and integrated into all primary communication channels: Slack, WhatsApp, and Microsoft Teams.",
		"Conducted a revision of the Amharic DBpedia Chapter research paper ([[Link]]).",
		"I have read and performed model benchmarking on Kaggle using GPU access to explore mBERT, XLM-R, and Afro-XLM-R that was developed by the DICE-Research team ([[Kaggle]]).",
		"Revised core knowledge of Transformers, BERT, and efficient fine-tuning methods like LoRA ([[Notes]]).",
		"Selected LangGraph as the primary framework for building the project's agentic orchestration layer and read about Langgraph ([[Docs]]).",
		"Why LangGraph? It is used by production-grade companies like LinkedIn. It has large community support. It is customized for both python and javascript compared to Mastra.",
		"Developed and launched a personal portfolio website and started writing a blog about GSOC progress each week.",
		"Prepared a technical reference document for mentors to outline the proposed development roadmap."
	];

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
		{ label: "DBpedia", slug: "dbpedia", angle: 270 },
		{ label: "Transformers", slug: "transformers", angle: 330 },
		{ label: "LoRA", slug: "lora", angle: 30 },
		{ label: "LangGraph", slug: "langgraph", angle: 90 },
		{ label: "Kaggle", slug: "kaggle-benchmark", angle: 150 },
		{ label: "GSoC 2026", slug: "gsoc-2026", angle: 210 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Pre-Coding
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			Merging insights from the initial onboarding weeks, from reading papers to exploring LangGraph and finalizing the roadmap.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#gsoc-2026</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#pre-coding</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#nlp</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#dbpedia</span>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<!-- Timeline 1 -->
		<section>
			<h2 class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground">
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-brand/20 text-brand">
					1
				</span>
				May 16, 2026 - May 22, 2026
			</h2>
			<div class="mt-5 space-y-4">
				{#each bodyParagraphsPeriod1 as paragraph (paragraph)}
					<p>
						{#each renderParagraph(paragraph) as segment, index (index)}
							{#if segment.link}
								<a
									href={segment.link.startsWith("http") ? segment.link : `${base}/blog/gsoc/2026/pre-coding#${segment.text.toLowerCase().replaceAll(" ", "-")}`}
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

		<!-- Timeline 2 -->
		<section>
			<h2 class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground">
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					0
				</span>
				May 7, 2026 - May 15, 2026
			</h2>
			<div class="mt-5 space-y-4">
				{#each bodyParagraphsPeriod0 as paragraph (paragraph)}
					<p>
						{#each renderParagraph(paragraph) as segment, index (index)}
							{#if segment.link}
								<a
									href={segment.link.startsWith("http") ? segment.link : `${base}/blog/gsoc/2026/pre-coding#${segment.text.toLowerCase().replaceAll(" ", "-")}`}
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
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/pre-coding#${link.slug}`}
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
					PRE-CODING
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/pre-coding#${node.slug}`}
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
