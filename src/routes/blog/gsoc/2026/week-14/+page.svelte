<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "kgproxy (GitHub)",
			slug: "kgproxy",
			href: "https://github.com/Nama21yo/kgproxy"
		},
		{
			label: "Multilingual LLM Benchmarking (presentation)",
			slug: "presentation",
			href: `${base}/presentations/multilingual-llm-benchmarking`
		},
		{
			label: "LLMIntegration (GitHub)",
			slug: "llm-integration",
			href: "https://github.com/AmharicDBpedia/LLMIntegration"
		},
		{
			label: "AWS EC2",
			slug: "aws-ec2",
			href: "https://aws.amazon.com/ec2/"
		},
		{
			label: "DBpedia SPARQL endpoint",
			slug: "dbpedia-sparql",
			href: "https://dbpedia.org/sparql"
		}
	];

	const benchmarkSummary = [
		{
			experiment: "Issue #3 — Baseline",
			bestAccuracy: "62.4%",
			method: "Afro-XLM-R + 5-shot Qwen 2.5 (7B)",
			insight: "Two-stage retrieve-then-rerank beats retrieval alone by ~10 pp."
		},
		{
			experiment: "Issue #4 — Prompt Engineering",
			bestAccuracy: "64.5%",
			method: "ChainOfThought + 5-shot",
			insight: "CoT reasoning helps on ambiguous shortlists; self-consistency adds ~1.3 pp more."
		},
		{
			experiment: "Issue #5 — Translation",
			bestAccuracy: "65.2%",
			method: "Augmented retrieval (Amharic + English)",
			insight: "Discarding Amharic context hurts; both languages together beats either alone."
		},
		{
			experiment: "Issue #6 — Ensemble",
			bestAccuracy: "67.6%",
			method: "Majority vote over 5 members",
			insight: "Ensemble ceiling ~67.6%; gains taper off beyond 5 members."
		}
	];

	const kgproxyFeatures = [
		{
			title: "Elastic IP on AWS EC2",
			body: "kgproxy runs on a t3.micro EC2 instance with an Elastic IP address, giving it a stable public endpoint that doesn't change across restarts. Any caller with the URL can submit a SPARQL query without institutional VPN credentials."
		},
		{
			title: "SPARQL forwarding proxy",
			body: "Incoming SPARQL queries are validated, forwarded to the internal Amharic DBpedia endpoint with the appropriate authentication headers, and the response is returned to the caller. The proxy adds CORS headers so that browser-based clients (like the Amharic DBpedia website) can call it directly."
		},
		{
			title: "Rate limiting",
			body: "Basic rate limiting prevents the proxy from being used as an open relay for arbitrary SPARQL traffic. Requests over the per-minute threshold receive a 429 response, protecting the internal endpoint from accidental or malicious overload."
		},
		{
			title: "Open-source and self-hostable",
			body: "The proxy code is published at github.com/Nama21yo/kgproxy under an MIT licence. Any DBpedia language chapter facing the same VPN-access problem can deploy their own instance against their own endpoint in under ten minutes."
		}
	];

	const mindMapNodes = [
		{ label: "kgproxy", slug: "kgproxy", angle: 270 },
		{ label: "Presentation", slug: "presentation", angle: 342 },
		{ label: "Progress report", slug: "llm-integration", angle: 54 },
		{ label: "AWS deploy", slug: "aws-ec2", angle: 126 },
		{ label: "SPARQL proxy", slug: "dbpedia-sparql", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 14: Progress Report, Presentation, and kgproxy on AWS
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			The final week of Google Summer of Code 2026. All four benchmark experiments were
			summarised in a progress report and an interactive presentation. kgproxy — a lightweight
			SPARQL proxy — was deployed on AWS with an Elastic IP, solving the VPN access problem
			that had blocked the website's live data features since Week 8.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#gsoc-2026</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#week-14</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#kgproxy</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#gsoc-finale</span>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2 class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground">
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">14</span>
				Aug 21, 2026 – Aug 28, 2026
			</h2>
			<p class="mt-5">
				Week 14 was the closing chapter of GSoC 2026. The four benchmark experiments — Issues #3
				through #6 — had produced a full empirical picture of how different LLM strategies
				perform on the Amharic property mapping task. The job for this final week was to
				communicate those results clearly: a structured progress report, an interactive
				12-slide presentation for mentors and the broader DBpedia community, and a deployed
				infrastructure piece that removes the last major technical blocker for the Amharic DBpedia
				website's interactive features.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				The progress report
			</h2>
			<p class="mt-5">
				<code>PROGRESS_REPORT.md</code> in the LLMIntegration repository is a nine-section
				document covering everything from the problem statement to the final cross-experiment
				comparison. It was written to be readable by someone who is familiar with NLP but not
				necessarily with DBpedia or Amharic: it introduces the task with concrete Ge'ez script
				examples, explains the mathematical foundations of the cosine similarity retriever and the
				fuzzy accuracy metric, and presents all four experiment results in tables alongside
				plain-language interpretations.
			</p>
			<p class="mt-4">
				The report also includes a cost-versus-accuracy trade-off analysis: larger models and
				more complex strategies produce higher accuracy, but the gains shrink with each step.
				The ensemble of five members (Issue #6) achieves the highest measured accuracy at 67.62%,
				but adds roughly 15× the inference cost compared to a single zero-shot call. For a
				production pipeline that needs to process every Wikipedia dump, that trade-off matters.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Benchmark results across all four experiments
			</h2>
			<div class="mt-5 overflow-x-auto rounded-2xl border border-foreground/10">
				<table class="w-full text-sm">
					<thead>
						<tr class="border-b border-foreground/10 bg-muted/40">
							<th class="px-4 py-3 text-left font-mono text-xs font-black text-muted-foreground">Experiment</th>
							<th class="px-4 py-3 text-left font-mono text-xs font-black text-muted-foreground">Best method</th>
							<th class="px-4 py-3 text-right font-mono text-xs font-black text-cyan">Accuracy</th>
						</tr>
					</thead>
					<tbody>
						{#each benchmarkSummary as row (row.experiment)}
							<tr class="border-b border-foreground/10 last:border-0">
								<td class="px-4 py-3 font-mono text-xs font-black">{row.experiment}</td>
								<td class="px-4 py-3 text-xs text-muted-foreground">{row.method}</td>
								<td class="px-4 py-3 text-right font-mono text-xs font-black text-cyan">{row.bestAccuracy}</td>
							</tr>
						{/each}
					</tbody>
				</table>
			</div>
			<div class="mt-4 space-y-3">
				{#each benchmarkSummary as row (row.experiment)}
					<div class="flex items-start gap-2 rounded-xl border border-foreground/10 bg-muted/15 px-4 py-3">
						<span class="mt-0.5 font-mono text-xs font-black text-cyan">{row.bestAccuracy}</span>
						<span class="text-xs leading-5 text-muted-foreground">{row.insight}</span>
					</div>
				{/each}
			</div>
			<p class="mt-5">
				The 67.62% ceiling on Issue #6 is best understood not as a failure but as a measurement:
				it tells us exactly where the current approach tops out and therefore what the next phase
				of work needs to target. The most likely path to higher accuracy is better-quality
				training data for few-shot examples (curated, diverse demonstrations rather than random
				samples), a retriever fine-tuned on the Amharic DBpedia vocabulary rather than a
				general-purpose multilingual encoder, and fixing the extraction framework bugs so the
				ground truth labels themselves are more reliable.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				The presentation
			</h2>
			<p class="mt-5">
				Alongside the written report, a 12-slide interactive presentation was published on this
				portfolio website under the name <em>Multilingual LLM Benchmarking</em>. The slides walk
				through the problem statement, the pipeline architecture, the mathematical foundations,
				and all four experiment results, with animated bar charts and data tables that make the
				cross-experiment comparison easy to read at a glance.
			</p>
			<p class="mt-4">
				The presentation was designed to be self-contained: someone watching without any DBpedia
				or Amharic background should be able to understand the problem and the results by the
				time the final slide appears. Each slide has a specific purpose — no slide is purely
				decorative — and the information density is matched to what can be absorbed in about
				thirty seconds per slide at a normal presentation pace.
			</p>
			<p class="mt-4">
				<a
					href={`${base}/presentations/multilingual-llm-benchmarking`}
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>
					View the presentation →
				</a>
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				kgproxy: solving the VPN problem
			</h2>
			<p class="mt-5">
				Since Week 8, a recurring blocker for the Amharic DBpedia website was the VPN
				requirement for the SPARQL endpoint. The internal endpoint is accessible only from
				within the institutional network, which means the public website — hosted on GitHub Pages
				as static files — could not make live SPARQL queries to it. Every interactive feature
				that depended on real data was either faked with static snapshots or simply deferred.
			</p>
			<p class="mt-4">
				kgproxy solves this by acting as an authenticated intermediary. The proxy runs inside the
				VPN (on an EC2 instance that is peered into the institutional network) and exposes a
				public HTTP endpoint for SPARQL queries. Any client — including a JavaScript function
				running in a visitor's browser on the Amharic DBpedia website — can send a SPARQL query
				to kgproxy's public URL and receive a response, without needing VPN credentials.
			</p>
			<div class="mt-5 space-y-4">
				{#each kgproxyFeatures as feature (feature.title)}
					<div class="rounded-2xl border border-cyan/15 bg-cyan/5 p-5">
						<h3 class="font-mono text-sm font-black">{feature.title}</h3>
						<p class="mt-2 text-sm leading-7">{feature.body}</p>
					</div>
				{/each}
			</div>
			<p class="mt-5">
				The kgproxy repository is at
				<a
					href="https://github.com/Nama21yo/kgproxy"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>
					github.com/Nama21yo/kgproxy
				</a>.
				Deploying it this week unblocked the interactive data features on the Amharic DBpedia
				website and demonstrated that the VPN problem, while real, was solvable with a small
				piece of infrastructure rather than a change to the DBpedia endpoint itself.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Looking back and forward
			</h2>
			<p class="mt-5">
				Fourteen weeks is a short time to make measurable progress on a genuinely hard problem:
				building a pipeline that maps low-resource-language text to a large, structured ontology
				with limited training data, limited GPU access for most of the summer, and a base
				infrastructure (the extraction framework) that needed debugging before it could be trusted.
				The benchmarks showed that the two-stage retrieve-then-rerank approach works — the best
				ensemble achieves 67.62% accuracy on a 279-example test set — and the experiments
				identified the specific levers that move accuracy most reliably: better few-shot examples,
				chain-of-thought reasoning, and cross-lingual signal from Amharic-to-English translation.
			</p>
			<p class="mt-4">
				The extraction framework work produced seven formally filed bug reports that represent
				concrete, actionable improvements to how DBpedia processes Amharic Wikipedia content.
				The kgproxy deployment solved an infrastructure problem that had been blocking the public
				website for six weeks. And the template mappings added throughout the summer incrementally
				expanded the fraction of Amharic Wikipedia content that DBpedia can represent as
				structured Linked Data.
			</p>
			<p class="mt-4">
				The work is not finished — the extraction framework bugs are upstream fixes waiting to
				be merged, the LLM pipeline has a ceiling that better training data and a fine-tuned
				retriever should be able to raise, and the Amharic DBpedia graph has a long way to go
				before it approaches the coverage of its English counterpart. But the summer produced a
				foundation: documented, reproducible, and open-source, ready for whoever picks it up next.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-14#${link.slug}`}
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
				<div class="flex h-16 w-16 items-center justify-center rounded-full border-2 border-cyan/40 bg-zinc-900/80 text-center text-[10px] font-black text-cyan shadow-[0_0_15px_rgba(34,211,238,0.25)] backdrop-blur-md">WEEK 14</div>
			</div>
			{#each mindMapNodes as node (node.slug)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a href={`${base}/blog/gsoc/2026/week-14#${node.slug}`} class="absolute flex h-[54px] w-[54px] -translate-x-1/2 -translate-y-1/2 items-center justify-center rounded-full border border-white/10 bg-zinc-900/90 p-1.5 text-center text-[8px] leading-tight font-bold text-white/80 shadow-lg backdrop-blur-sm transition-all duration-300 hover:z-20 hover:scale-125 hover:border-cyan hover:bg-cyan/10 hover:text-cyan hover:shadow-[0_0_20px_rgba(34,211,238,0.4)]" style={`left: ${(cx / 260) * 100}%; top: ${(cy / 256) * 100}%;`} aria-label={node.label} title={node.label}>
					<span class="line-clamp-3">{node.label}</span>
				</a>
			{/each}
		</div>
	</div>
</aside>
