<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "Website CI PR",
			slug: "website-ci-pr",
			href: "https://github.com/Amharic-DBpedia/website/pull/1"
		},
		{
			label: "Extraction Framework",
			slug: "extraction-framework",
			href: "https://github.com/dbpedia/extraction-framework"
		},
		{
			label: "Java 8",
			slug: "java-8",
			href: "https://www.java.com/releases/"
		},
		{
			label: "Husky",
			slug: "husky",
			href: "https://typicode.github.io/husky/"
		},
		{
			label: "ISO language codes",
			slug: "iso-language-codes",
			href: "https://en.wikipedia.org/wiki/List_of_ISO_639_language_codes"
		}
	];

	const milestones = [
		{
			title: "Extraction and statistics API",
			body: "I continued the backend work by implementing the extraction and statistics API that calculates statistics for a selected Amharic dump. This endpoint is important because it turns the extraction framework from a local developer tool into something the website can call and explain."
		},
		{
			title: "Frontend, backend, and E2E CI",
			body: "I integrated CI for the Amharic DBpedia website so frontend checks, backend checks, and end-to-end tests can run together. This work was submitted in the website CI pull request, which helps future contributors know when a change breaks the app before it reaches users."
		},
		{
			title: "Java 8 export fix",
			body: "I fixed an extraction API error by exporting the runtime to Java 8. The extraction framework has compatibility assumptions, and making that runtime explicit made the API path more predictable."
		},
		{
			title: "Contribution workflow",
			body: "I added Husky hooks and PR contribution guidance so contributors get feedback before pushing and reviewers get a clearer structure for proposed changes."
		}
	];

	const debuggingFindings = [
		{
			title: "New regional language codes are not fully supported",
			body: "Some newer or regional language identifiers are not handled flexibly by the extraction framework path. One example is be-tarask. The behavior appears hardcoded around an older or narrower language-code set, which is a problem for multilingual DBpedia work because language support should not require changing core logic every time a new variant is needed."
		},
		{
			title: "Invalid template nodes appear in parsed content",
			body: "While running Amharic extraction, I saw invalid nodes such as {{Lable|am|የሙዚቃ መሣሪያ ተጫዋች}}. These malformed or unexpected template calls can be ignored by the parser, but each ignored node is also a clue that source templates and parser expectations are not perfectly aligned."
		},
		{
			title: "Ontology property domains can be missing",
			body: "Another issue was a missing domain error: domain 'dbo:Disease' of property 'dbo:virus' not found. This kind of error points to a mismatch between mapping definitions, ontology assumptions, and what the extraction framework expects to find when resolving a property."
		},
		{
			title: "Incorrect templates can break extraction",
			body: "Some templates are written in ways that make parsing fragile. Even if the template is useful on a wiki page, it can still cause extraction errors if its structure does not match what the DBpedia mapping and parser expect."
		}
	];

	const ciSteps = ["Frontend checks", "Backend checks", "E2E tests", "Pages-ready build"];

	const mindMapNodes = [
		{ label: "Statistics API", slug: "extraction-framework", angle: 270 },
		{ label: "CI", slug: "website-ci-pr", angle: 342 },
		{ label: "Java 8", slug: "java-8", angle: 54 },
		{ label: "Husky", slug: "husky", angle: 126 },
		{ label: "Language codes", slug: "iso-language-codes", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 3
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			Week 3 was about making the implementation safer to run: extracting dump statistics, adding
			CI across the website stack, fixing Java compatibility, and documenting the rough edges I
			found while exercising the DBpedia extraction framework for Amharic.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-3</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#ci</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#extraction-framework</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					3
				</span>
				June 6, 2026 - June 12, 2026
			</h2>
			<p class="mt-5">
				After Week 2 established the first backend and UI pieces, Week 3 focused on making those
				pieces more dependable. I worked on the extraction/statistics API, connected the project
				to a real CI workflow, fixed a Java compatibility issue, and spent time debugging the
				extraction framework with Amharic-specific inputs. The debugging work was especially
				useful because it showed the difference between a feature working in a clean demo and a
				feature surviving real multilingual data.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Implementation milestones
			</h2>
			<div class="mt-5 space-y-5">
				{#each milestones as milestone (milestone.title)}
					<div class="rounded-2xl border border-foreground/10 bg-background/70 p-5">
						<h3 class="font-mono text-xl font-black">{milestone.title}</h3>
						<p class="mt-3">{milestone.body}</p>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				CI for the website stack
			</h2>
			<p class="mt-5">
				I integrated a CI workflow that runs the frontend, backend, and end-to-end tests for the
				DBpedia website. The pull request is available here:
				<a
					href="https://github.com/Amharic-DBpedia/website/pull/1"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>
					Amharic-DBpedia/website#1</a
				>. This matters because the site now has multiple moving parts. A change can break the UI,
				the API, or the browser flow, so the CI needs to exercise the full path.
			</p>
			<div class="mt-5 grid gap-3 md:grid-cols-4">
				{#each ciSteps as step, index (step)}
					<div class="rounded-2xl border border-cyan/15 bg-cyan/5 p-4">
						<p class="blog-label">0{index + 1}</p>
						<p class="mt-2 font-mono text-sm font-black text-foreground">{step}</p>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				What the extraction-framework debugging revealed
			</h2>
			<p class="mt-5">
				The hardest part of the week was not only wiring the API. It was understanding why
				extraction can fail on real Amharic content. These failures are valuable because they
				point to the places where our mapping work, templates, language configuration, and
				ontology assumptions need to become more precise.
			</p>

			<div class="mt-5 space-y-5">
				{#each debuggingFindings as finding (finding.title)}
					<div class="rounded-2xl border border-foreground/10 bg-muted/25 p-5">
						<h3 class="font-mono text-xl font-black">{finding.title}</h3>
						<p class="mt-3">{finding.body}</p>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				The practical lesson
			</h2>
			<p class="mt-5">
				Week 3 made the project feel more real. The website now has CI, the backend has a clearer
				extraction/statistics path, and the extraction framework issues are becoming specific
				enough to debug instead of vague enough to ignore. The next step is to keep turning those
				findings into fixes: cleaner templates, better language-code handling, more robust
				mappings, and API behavior that explains failures instead of hiding them.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-3#${link.slug}`}
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
					WEEK 3
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-3#${node.slug}`}
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
