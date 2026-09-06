<script lang="ts">
	import { base } from "$app/paths";
	import Icon from "@iconify/svelte";
	import ScrollReveal from "$lib/components/ScrollReveal.svelte";

	const gsocWeeks = [
		{
			id: "final-week",
			title: "Final Week",
			date: "2026-08-28",
			status: "completed",
			excerpt:
				"extraction-framework PR #846 fixes all seven bugs from issue #845; agentic-amdbpedia renamed, hardened, and shipped in-chat review, Wikipedia-link extraction, and a redesigned sidebar."
		},
		{
			id: "week-14",
			title: "Week 14",
			date: "2026-08-21",
			status: "completed",
			excerpt:
				"Progress report and benchmarking presentation published; kgproxy deployed on AWS to solve the DBpedia SPARQL VPN access issue."
		},
		{
			id: "week-13",
			title: "Week 13",
			date: "2026-08-14",
			status: "completed",
			excerpt:
				"Issue #4 (prompt engineering) and Issue #5 (translation experiment) PRs merged; formal Extraction Framework bug reports filed; integration approach document written."
		},
		{
			id: "week-12",
			title: "Week 12",
			date: "2026-08-07",
			status: "completed",
			excerpt:
				"Issue #3 PR merged with full benchmark results; Issue #4 prompt engineering experiment underway; Ethiopian calendar extraction bugs formally confirmed."
		},
		{
			id: "week-11",
			title: "Week 11",
			date: "2026-07-31",
			status: "completed",
			excerpt:
				"H100 GPU server access granted; Issue #3 multi-model benchmark implemented with Afro-XLM-R; first results CSV produced; five additional mappings added."
		},
		{
			id: "week-10",
			title: "Week 10",
			date: "2026-07-24",
			status: "completed",
			excerpt:
				"SPARQL queries used to investigate Amharic DBpedia coverage; Libya article refactored as test case; eight Amharic Wikipedia pages updated."
		},
		{
			id: "week-9",
			title: "Week 9",
			date: "2026-07-17",
			status: "completed",
			excerpt:
				"Deep audit of the DBpedia Extraction Framework against Amharic dumps revealed seven distinct bug classes in date, regex, and ontology handling."
		},
		{
			id: "week-8",
			title: "Week 8",
			date: "2026-07-10",
			status: "completed",
			excerpt:
				"Website PR #2 submitted; server proposal written; Mid-Term Evaluation passed; DBpedia mapping VPN issue discovered and investigated."
		},
		{
			id: "week-7",
			title: "Week 7",
			date: "2026-07-03",
			status: "completed",
			excerpt:
				"LLM reliability metrics studied; RAG pipeline with top-10 retrieval implemented; Afro-XLM-R integrated as the retrieval backbone; Kaggle results documented."
		},
		{
			id: "week-6",
			title: "Week 6",
			date: "2026-06-26",
			status: "completed",
			excerpt:
				"Prompt engineering paper deep dives; GPU benchmarking on Kaggle; 48 GB VRAM server request submitted; fuzzy search and amseg normalization added."
		},
		{
			id: "week-5",
			title: "Week 5",
			date: "2026-06-19",
			status: "completed",
			excerpt:
				"Six multilingual models evaluated; prompt engineering strategies (CoT, ToT, ReAct, DSPy) benchmarked; Website PR #1 submitted; GEPA paper studied."
		},
		{
			id: "week-4",
			title: "Week 4",
			date: "2026-06-12",
			status: "completed",
			excerpt:
				"Deployment planning with Thomas Tsoru; three new template mappings; Wikidata-style stats UI refactor; multilingual model shortlist; LangGraph agent scaffold."
		},
		{
			id: "week-3",
			title: "Week 3",
			date: "2026-06-05",
			status: "completed",
			excerpt:
				"Extraction and statistics API, frontend/backend/E2E CI, Java 8 compatibility, Husky hooks, and extraction-framework debugging notes."
		},
		{
			id: "week-2",
			title: "Week 2",
			date: "2026-05-29",
			status: "completed",
			excerpt:
				"Backend infrastructure, sanitizer and dump preview endpoints, Playwright coverage, UI updates, and first Amharic template mappings."
		},
		{
			id: "week-1",
			title: "Week 1",
			date: "2026-05-22",
			status: "completed",
			excerpt:
				"Mentorship syncs, deep dives into LangGraph orchestration, finishing up the site refactor, and embarking on a specialized KG course."
		},
		{
			id: "pre-coding",
			title: "Pre-Coding",
			date: "2026-05-15",
			status: "completed",
			excerpt:
				"Merging insights from the initial onboarding weeks, from reading papers to exploring LangGraph and finalizing the roadmap."
		}
	];

	const mentors = [
		{
			name: "Prof. Dr. Ricardo Usbeck",
			role: "Project Mentor & Professor of Information Systems (AI & Explainability), Leuphana University",
			href: "https://www.leuphana.de/en/institutes/iis/persons/ricardo-usbeck.html",
			bio: "Leads national and international research on knowledge graphs, LLMs, chatbots, and AI for social purposes. Bridges research and application."
		},
		{
			name: "Andargachew Tibebe Merso",
			role: "Co-Mentor & Lecturer, Addis Ababa University",
			href: "https://contact-andy.github.io/",
			bio: "Holds an MSc in Computer Science from University of Gondar. Interested in knowledge graphs and NLP."
		},
		{
			name: "Tilahun Abedissa Taffa",
			role: "Co-Mentor & PhD Researcher, Leuphana University",
			href: "https://www.leuphana.de/en/institutes/iis/persons/tilahun-taffa.html",
			bio: "Specializes in hybrid QA systems for low-resource languages like Amharic. Research associate at AI & Explainability group."
		},
		{
			name: "Hizkiel Mitiku Alemayehu",
			role: "Co-Mentor & PhD Candidate, Paderborn University",
			href: "https://scholar.google.com/citations?user=mOMRLWEAAAAJ&hl=en",
			bio: "Research focuses on NLP and Digital Humanities. Research assistant at Paderborn University, Germany."
		}
	];
</script>

<section class="mx-auto max-w-7xl px-5 py-16 lg:px-8 lg:py-24">
	<ScrollReveal direction="down">
		<div class="max-w-3xl">
			<p class="section-kicker">GSoC 2026 @ DBpedia</p>
			<h1
				class="font-display text-5xl leading-tight font-black tracking-tight transition-colors duration-500 hover:text-cyan md:text-7xl"
			>
				Amharic DBpedia Ontology Mapping
			</h1>
			<p class="mt-6 text-xl leading-9 text-muted-foreground">
				Building an agentic pipeline to map Amharic text entities to DBpedia ontology classes using
				Afro-XLM-R and LangGraph. Weekly progress from Pre-Coding through the Final Week.
			</p>
		</div>
	</ScrollReveal>
</section>

<section class="mx-auto max-w-7xl px-5 pb-20 lg:px-8">
	<div class="grid gap-8 lg:grid-cols-[1fr_1.5fr]">
		<div class="space-y-6">
			<ScrollReveal direction="right">
				<div class="rounded-[2rem] border border-foreground/10 bg-background/75 p-6">
					<p class="section-kicker">Weekly Updates</p>
					<div class="mt-4 space-y-2">
						{#each gsocWeeks as week (week.id)}
							<a
								href={`${base}/blog/gsoc/2026/${week.id}`}
								class="flex items-center justify-between rounded-xl px-4 py-3 text-sm font-bold transition hover:bg-muted hover:text-foreground"
							>
								<div class="flex items-center gap-2">
									<div
										class="h-2 w-2 rounded-full {week.status === 'completed'
											? 'bg-emerald'
											: 'bg-amber'}"
									></div>
									<span>{week.title}</span>
								</div>
								<span class="text-xs font-normal text-muted-foreground">{week.date}</span>
							</a>
						{/each}
					</div>
				</div>
			</ScrollReveal>

			<ScrollReveal delay={100} direction="right">
				<div class="rounded-[2rem] border border-foreground/10 bg-background/75 p-6">
					<p class="section-kicker">Project Details</p>
					<div class="mt-4 space-y-3 text-sm">
						<div class="flex items-start gap-2">
							<Icon icon="iconoir:network" class="mt-0.5 shrink-0 text-cyan" width="16" />
							<span class="text-muted-foreground">Organization:</span>
							<a
								href="https://summerofcode.withgoogle.com/programs/2026/organizations/dbpedia"
								target="_blank"
								rel="noreferrer"
								class="font-bold hover:text-cyan"
							>
								DBpedia
							</a>
						</div>
						<div class="flex items-start gap-2">
							<Icon icon="iconoir:calendar" class="mt-0.5 shrink-0 text-cyan" width="16" />
							<span class="text-muted-foreground">Timeline:</span>
							<span class="font-bold">May 2026 – August 2026</span>
						</div>
						<div class="flex items-start gap-2">
							<Icon icon="iconoir:git-branch" class="mt-0.5 shrink-0 text-cyan" width="16" />
							<span class="text-muted-foreground">Duration:</span>
							<span class="font-bold">12 Weeks</span>
						</div>
					</div>
				</div>
			</ScrollReveal>

			<ScrollReveal delay={200} direction="right">
				<div class="rounded-[2rem] border border-foreground/10 bg-background/75 p-6">
					<p class="section-kicker">Tech Stack</p>
					<div class="mt-4 flex flex-wrap gap-2">
						{#each ["Afro-XLM-R", "LoRA", "LangGraph", "Python", "Transformers", "BERT"] as tech (tech)}
							<span class="rounded-full bg-muted px-3 py-1 text-sm font-bold">{tech}</span>
						{/each}
					</div>
				</div>
			</ScrollReveal>
		</div>

		<ScrollReveal delay={150} direction="left">
			<div class="rounded-[2rem] border border-foreground/10 bg-background/75 p-6">
				<p class="section-kicker">Mentors</p>
				<div class="mt-5 space-y-4">
					{#each mentors as mentor (mentor.name)}
						<a
							href={mentor.href}
							target="_blank"
							rel="noreferrer"
							class="group block rounded-2xl border border-foreground/10 bg-muted/35 p-4 transition hover:-translate-y-1 hover:border-cyan/30 hover:shadow-lg"
						>
							<div class="flex items-start justify-between">
								<div>
									<h3 class="font-black group-hover:text-cyan">{mentor.name}</h3>
									<p class="mt-0.5 text-xs font-bold text-cyan">{mentor.role}</p>
								</div>
								<Icon
									icon="iconoir:arrow-up-right"
									class="shrink-0 text-muted-foreground transition group-hover:text-cyan"
									width="16"
								/>
							</div>
							<p class="mt-2 text-sm leading-6 text-muted-foreground">{mentor.bio}</p>
						</a>
					{/each}
				</div>
			</div>
		</ScrollReveal>
	</div>
</section>
