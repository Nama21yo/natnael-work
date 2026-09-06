<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "Website PR #2",
			slug: "website-pr-2",
			href: "https://github.com/AmharicDBpedia/amharicdbpedia.github.io/pull/2"
		},
		{
			label: "Server proposal",
			slug: "server-proposal",
			href: "https://docs.google.com/document/d/1BWr-BoHnoUpBE2ag_HBffmUfL-z-f2jxAF85AqmjNx0/edit?usp=sharing"
		},
		{
			label: "DBpedia mappings",
			slug: "dbpedia-mappings",
			href: "https://mappings.dbpedia.org/"
		},
		{
			label: "GSoC evaluations",
			slug: "gsoc-evaluations",
			href: "https://summerofcode.withgoogle.com/"
		}
	];

	const mindMapNodes = [
		{ label: "Website PR", slug: "website-pr-2", angle: 270 },
		{ label: "Proposal", slug: "server-proposal", angle: 342 },
		{ label: "Mid Eval", slug: "gsoc-evaluations", angle: 54 },
		{ label: "VPN Issue", slug: "dbpedia-mappings", angle: 126 },
		{ label: "DBpedia", slug: "dbpedia-mappings", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 8: Website PR, Mid-Term Evaluation, and Server Proposal
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			The mid-point of GSoC arrived this week. The website received another PR, a formal server
			access proposal was submitted, mid-term evaluation was completed, and a DBpedia
			infrastructure issue was discovered.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-8</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#website</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#mid-evaluation</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#proposal</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#server</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">
					8
				</span>
				Jul 10, 2026 – Jul 17, 2026
			</h2>
			<p class="mt-5">
				Week 8 was the official midpoint of GSoC 2026, and it felt like one. The focus shifted
				slightly from implementation toward documentation and process — submitting a website PR
				based on feedback, preparing a formal server access proposal, completing the mid-term
				evaluation, and investigating an infrastructure problem with the live DBpedia mapping
				website. Each of these tasks required stepping back from code and thinking about the
				project's shape and trajectory.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Website PR #2
			</h2>
			<p class="mt-5">
				Based on mentor feedback from the PR #1 review cycle, the Amharic DBpedia website was
				refactored again and the changes were submitted as
				<a
					href="https://github.com/AmharicDBpedia/amharicdbpedia.github.io/pull/2"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>PR #2</a
				>. The changes in this PR addressed specific UI and UX suggestions from the review: the
				layout was adjusted for better readability on smaller screens, the data presentation
				components were refined to better communicate what each statistic means to a visitor who
				is not familiar with knowledge graphs, and several minor accessibility improvements were
				made based on mentor comments.
			</p>
			<p class="mt-4">
				Working through a second review cycle was valuable beyond the specific changes it produced.
				The feedback pattern — submit, review, revise, re-submit — is the real-world open source
				contribution workflow, and practising it on a project with active mentors gives much better
				training for future contributions than simply merging your own PRs would.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Server access proposal
			</h2>
			<p class="mt-5">
				The cluster administrators at Leuphana required a formal written proposal before granting
				GPU access. This week, a detailed
				<a
					href="https://docs.google.com/document/d/1BWr-BoHnoUpBE2ag_HBffmUfL-z-f2jxAF85AqmjNx0/edit?usp=sharing"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>server access proposal</a
				> was written and submitted describing the experiment design in full. The proposal covered:
				the research motivation (benchmarking multilingual LLMs for Amharic property mapping), the
				specific models to be run (five models from the candidate list), the compute requirements
				(minimum 48 GB VRAM for the 32B parameter model), the expected run time per experiment
				configuration, and the output format (per-model CSV files in a structured results
				directory).
			</p>
			<p class="mt-4">
				Writing the proposal was also a useful exercise in its own right. Articulating the
				experiment design precisely enough that an administrator unfamiliar with NLP could
				evaluate it forced a level of clarity about what exactly was being tested and why. The
				proposal was the document that eventually unlocked H100 access in Week 11.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Mid-term evaluation
			</h2>
			<p class="mt-5">
				The GSoC mid-term evaluation was completed this week. In the evaluation discussion with
				mentors, the first eight weeks of work were reviewed: the website build and two PR cycles,
				the mapping pipeline experiments on Kaggle, the model selection and benchmarking rationale,
				and the extraction framework work. The evaluation gave mentors a formal opportunity to
				provide structured feedback, and it gave the project a clear milestone marker — a written
				record of what had been accomplished and what remained for the second half.
			</p>
			<p class="mt-4">
				The mid-term also clarified priorities for Weeks 9–14. The three areas that needed the
				most attention were: (1) the extraction framework bugs that had been accumulating in
				notes but not yet filed as formal issues, (2) the multi-model benchmark that would require
				H100 compute, and (3) the integration plan for the best-performing configuration.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				DBpedia mapping website — VPN issue
			</h2>
			<p class="mt-5">
				A significant infrastructure problem was discovered this week: the live DBpedia mapping
				website at mappings.dbpedia.org was inaccessible without a VPN connection routed through
				specific European university networks. For contributors working from Ethiopia or elsewhere
				in Africa, this is a serious barrier — the VPN access required is not publicly available,
				meaning the very people who might contribute Amharic mappings cannot reach the platform
				where mappings are submitted.
			</p>
			<p class="mt-4">
				The issue was discussed with Thomas Tsoru, who confirmed that it was a known hosting
				configuration restriction rather than a temporary outage. The conversation identified that
				this would need to be raised as an infrastructure issue with the broader DBpedia
				organisation, since it affects not just Amharic contributors but any volunteer working
				from a network outside the allowed ranges. This became one of the background findings that
				motivated building kgproxy in Week 14.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-8#${link.slug}`}
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
					WEEK 8
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-8#${node.slug}`}
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
