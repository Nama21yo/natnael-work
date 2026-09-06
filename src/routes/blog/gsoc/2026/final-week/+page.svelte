<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "extraction-framework issue #845",
			slug: "issue-845",
			href: "https://github.com/dbpedia/extraction-framework/issues/845"
		},
		{
			label: "extraction-framework PR #846",
			slug: "pr-846",
			href: "https://github.com/dbpedia/extraction-framework/pull/846"
		},
		{
			label: "agentic-amdbpedia (GitHub)",
			slug: "agentic-amdbpedia",
			href: "https://github.com/Nama21yo/agentic-amdbpedia"
		},
		{
			label: "LLMIntegration (GitHub)",
			slug: "llm-integration",
			href: "https://github.com/AmharicDBpedia/LLMIntegration"
		},
		{
			label: "Tentris",
			slug: "tentris",
			href: "https://github.com/dice-group/tentris"
		}
	];

	const frameworkFixes = [
		{
			bug: "A — Ethiopian→Gregorian year off by one",
			impact: "9 spot-checked dates, all wrong by exactly ±1 year",
			fix: "Branch on the Gregorian month the JDN algorithm actually needs, not the Ethiopian one"
		},
		{
			bug: "B — Standard ቀን date format never parsed",
			impact: "94.5% of Ethiopian dates in the dump silently dropped",
			fix: 'Added a regex admitting ቀን ("day") between the day and year'
		},
		{
			bug: "C — Two date regexes could never match",
			impact: "dateRegex2/3 declared 4 capture groups, destructured into 3",
			fix: "Fixed the arity mismatch so all five regexes actually run"
		},
		{
			bug: "D — ~9% of month spellings unrecognised",
			impact: "Month 4: 1,193 of 1,195 uses (99.8%) used an unlisted spelling",
			fix: "Extended monthsMap with the spellings actually seen in the dump"
		},
		{
			bug: "E — ignoreProperties overrides, doesn't union",
			impact: "2,888 image/map/flag triples (7% of the dataset) leaked through",
			fix: "Language-specific ignore list now unions with the English baseline"
		},
		{
			bug: "F — Empty literals emitted as facts",
			impact: "902 blank-string triples, 701 of them in mappingbased-literals",
			fix: 'Empty parses are dropped instead of written out as ""@am'
		},
		{
			bug: "G — Stale Module namespace mapping",
			impact: "39 ሞጁል: pages misclassified as articles, plus NPEs",
			fix: "Recognises the localised ሞጁል namespace while keeping legacy aliases"
		}
	];

	const shipHighlights = [
		{
			title: "Approve or reject, right in the chat",
			body: "The review decision used to live on a separate page. Now a mapping the pipeline proposes shows Approve/Reject buttons inline in the same chat turn, and the outcome persists as a status badge -- no context switch to close the human-in-the-loop."
		},
		{
			title: "Paste a Wikipedia link, get a mapping",
			body: "Drop a wikipedia.org article URL into the chat and the backend fetches its wikitext (SSRF-safe: the host is checked against an anchored wikipedia.org pattern before any request goes out), extracts the infobox, and runs it through the same pipeline as a pasted infobox."
		},
		{
			title: "A left sidebar worth using for a demo",
			body: "Rebuilt as a collapsible icon rail with live search, sessions grouped by Today/Yesterday/Previous 7 days/Older, a confirm-before-delete dialog, and a live badge showing how many mappings are waiting in the review queue."
		},
		{
			title: "Demo-ready chat history, seeded and verified",
			body: "A browser-console script populates nine realistic sessions -- resolved approvals, a rejection, honest no-match refusals -- spread across every recency bucket, so a demo doesn't open on an empty sidebar. Its own test runs the script's real source against a real DOM before trusting it."
		}
	];

	const mindMapNodes = [
		{ label: "PR #846", slug: "pr-846", angle: 270 },
		{ label: "Review queue", slug: "agentic-amdbpedia", angle: 342 },
		{ label: "Wikipedia link", slug: "llm-integration", angle: 54 },
		{ label: "Sidebar redesign", slug: "issue-845", angle: 126 },
		{ label: "Tentris QA", slug: "tentris", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Final Week
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			Coding wrapped in Week 14, but two things were still open: the extraction-framework bugs
			filed as issue #845 needed an actual fix, and <code>agentic-amdbpedia</code> needed to go
			from a working pipeline to something a mentor could sit down and use end to end. This week
			closed both out.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#gsoc-2026</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#final-week</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#extraction-framework</span>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground">#agentic-amdbpedia</span>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2 class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground">
				<span class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan">F</span>
				Aug 28, 2026 – Sep 4, 2026
			</h2>
			<p class="mt-5">
				Two things carried over from Week 14 into this one: the extraction-framework bugs filed as
				issue #845 still needed an actual fix, and <code>agentic-amdbpedia</code> still needed to go
				from a working pipeline to something a mentor could sit down and use end to end. This week
				finished both.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Fixing what issue #845 found
			</h2>
			<p class="mt-5">
				Issue #845, filed against <code>dbpedia/extraction-framework</code> on August 27,
				documented seven distinct bugs found while auditing the <code>amwiki-20260801</code> dump:
				four in
				<code>EthiopianDateParser</code> and its config, three in shared infrastructure that
				affects every language, not just Amharic. Each one shipped with dump-level evidence --
				exact article examples, occurrence counts, live triples on <code>am.dbpedia.org</code> -- rather
				than a description of the symptom.
			</p>
			<p class="mt-4">
				<a
					href="https://github.com/dbpedia/extraction-framework/pull/846"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>
					PR #846
				</a>
				fixes all seven: 231 additions and 16 deletions across 11 files, four commits, closing #845
				on merge.
			</p>
			<div class="mt-5 overflow-x-auto rounded-2xl border border-foreground/10">
				<table class="w-full text-sm">
					<thead>
						<tr class="border-b border-foreground/10 bg-muted/40">
							<th class="px-4 py-3 text-left font-mono text-xs font-black text-muted-foreground">Bug</th>
							<th class="px-4 py-3 text-left font-mono text-xs font-black text-muted-foreground">Measured impact</th>
							<th class="px-4 py-3 text-left font-mono text-xs font-black text-cyan">Fix</th>
						</tr>
					</thead>
					<tbody>
						{#each frameworkFixes as row (row.bug)}
							<tr class="border-b border-foreground/10 last:border-0">
								<td class="px-4 py-3 font-mono text-xs font-black">{row.bug}</td>
								<td class="px-4 py-3 text-xs text-muted-foreground">{row.impact}</td>
								<td class="px-4 py-3 text-xs">{row.fix}</td>
							</tr>
						{/each}
					</tbody>
				</table>
			</div>
			<p class="mt-5">
				Bug B affected the most dates: 94.5% of Ethiopian dates in the dump used the standard
				<code>&lt;month&gt; &lt;day&gt; ቀን &lt;year&gt;</code> form, and none of the five date
				regexes admitted it, so almost every date was silently dropped rather than mis-parsed. Bug
				E affected the most triples: because the Amharic ignore-list replaced the English baseline
				instead of adding to it, 7% of the entire infobox-properties dataset was image filenames and
				flag references emitted as facts.
			</p>
			<p class="mt-4">
				The PR was validated the same way the bug report was written -- against real data, not
				just unit tests in isolation: Java 8 core test compilation (<code>BUILD SUCCESS</code>), a
				focused JUnit regression suite (15 tests, all passing), and a full Java 8 reactor build
				through the <code>server</code> module. It's open and awaiting maintainer review as of this
				writing.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				agentic-amdbpedia: the pipeline, end to end
			</h2>
			<p class="mt-5">
				The project formerly known as <code>cross-lingual-knowledge-assistant</code> was renamed
				to
				<code>agentic-amdbpedia</code> this stretch -- a better name for what it actually is now: a
				LangGraph-orchestrated pipeline that takes an Amharic Wikipedia infobox, retrieves candidate
				DBpedia ontology properties with a hybrid Afro-XLM-R dense + BM25 sparse retriever, reranks
				them with a Gemma 2 9B predictor, and puts the result in front of a human before anything touches
				the live wiki.
			</p>
			<p class="mt-4">
				Every accepted or rejected mapping is captured into an append-only training-example log,
				written in the same format
				<a
					href="https://github.com/AmharicDBpedia/LLMIntegration"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>
					LLMIntegration
				</a>
				uses for its own benchmark datasets -- so every real decision a reviewer makes becomes more
				labeled data for the next round of experiments, not a one-off click. Publishing back to the
				wiki is consent-gated through MediaWiki bot credentials, and before any of it was trusted, the
				extracted output was loaded into
				<a
					href="https://github.com/dice-group/tentris"
					target="_blank"
					rel="noreferrer"
					class="rounded bg-brand-subtle/50 px-1 font-semibold text-brand-muted transition-colors hover:bg-brand hover:text-background"
				>
					Tentris
				</a>
				and checked with real SPARQL queries, not just spot-read by eye.
			</p>
			<p class="mt-4">
				Getting there also meant paying down infrastructure debt that had been masked by a working
				local <code>.env</code>: CI was quietly broken because constructing the app at import time
				forced a <code>GROQ_API_KEY</code> the CI environment never had, the integration test that
				expects a real Postgres instance never actually had one in CI, and an unpinned
				<code>mcp</code> dependency had been silently failing the scheduled end-to-end job for weeks
				on an unrelated breaking release. All three are fixed and green now, alongside a disk-cached
				retrieval index so the pipeline no longer rebuilds its embeddings on every cold start.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Frontend: review flow, Wikipedia-link extraction, and the sidebar redesign
			</h2>
			<p class="mt-5">
				A working pipeline is not the same as something a mentor can sit down and use, so this
				stretch also included a UI/UX pass focused on the frontend someone unfamiliar with the
				project would actually touch:
			</p>
			<div class="mt-5 space-y-4">
				{#each shipHighlights as item (item.title)}
					<div class="rounded-2xl border border-cyan/15 bg-cyan/5 p-5">
						<h3 class="font-mono text-sm font-black">{item.title}</h3>
						<p class="mt-2 text-sm leading-7">{item.body}</p>
					</div>
				{/each}
			</div>
			<p class="mt-5">
				The README was rewritten alongside it to actually explain the system rather than just list
				commands: how confidence scores are calculated, how the Templates/Mapped/Coverage
				statistics are derived, and the exact <code>Special:BotPasswords</code> steps needed to get
				a MediaWiki bot username and password for publishing.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Where things stand
			</h2>
			<p class="mt-5">
				PR #846 is the upstream outcome of the summer's extraction-framework audit: seven bugs,
				evidenced with real dump data, now fixed and validated, waiting on maintainer review.
				<code>agentic-amdbpedia</code> is the working-tool outcome: a pipeline that goes from a
				pasted infobox or a bare Wikipedia link to a reviewed, published DBpedia mapping, with every
				decision logged for the next round of benchmarking back in LLMIntegration.
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/final-week#${link.slug}`}
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
				<div class="flex h-16 w-16 items-center justify-center rounded-full border-2 border-cyan/40 bg-zinc-900/80 text-center text-[10px] font-black text-cyan shadow-[0_0_15px_rgba(34,211,238,0.25)] backdrop-blur-md">FINAL</div>
			</div>
			{#each mindMapNodes as node (node.slug)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a href={`${base}/blog/gsoc/2026/final-week#${node.slug}`} class="absolute flex h-[54px] w-[54px] -translate-x-1/2 -translate-y-1/2 items-center justify-center rounded-full border border-white/10 bg-zinc-900/90 p-1.5 text-center text-[8px] leading-tight font-bold text-white/80 shadow-lg backdrop-blur-sm transition-all duration-300 hover:z-20 hover:scale-125 hover:border-cyan hover:bg-cyan/10 hover:text-cyan hover:shadow-[0_0_20px_rgba(34,211,238,0.4)]" style={`left: ${(cx / 260) * 100}%; top: ${(cy / 256) * 100}%;`} aria-label={node.label} title={node.label}>
					<span class="line-clamp-3">{node.label}</span>
				</a>
			{/each}
		</div>
	</div>
</aside>
