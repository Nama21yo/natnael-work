<script lang="ts">
	import { onMount } from "svelte";
	import { base } from "$app/paths";
	import Icon from "@iconify/svelte";
	import { profile } from "$lib/data/portfolio";

	const slideCount = 11;
	const slideNumbers = Array.from({ length: slideCount }, (_, i) => i);
	let activeSlide = 0;
	let deckElement: HTMLElement;

	function goToSlide(index: number) {
		activeSlide = Math.max(0, Math.min(slideCount - 1, index));
	}

	function toggleFullscreen() {
		if (!document.fullscreenElement) {
			deckElement?.requestFullscreen();
			return;
		}
		document.exitFullscreen();
	}

	onMount(() => {
		const handleKeydown = (event: KeyboardEvent) => {
			if (event.key === "ArrowRight" || event.key === " " || event.key === "PageDown") {
				event.preventDefault();
				goToSlide(activeSlide + 1);
			}
			if (event.key === "ArrowLeft" || event.key === "PageUp") {
				event.preventDefault();
				goToSlide(activeSlide - 1);
			}
			if (event.key === "Home") { event.preventDefault(); goToSlide(0); }
			if (event.key === "End")  { event.preventDefault(); goToSlide(slideCount - 1); }
		};
		window.addEventListener("keydown", handleKeydown);
		return () => window.removeEventListener("keydown", handleKeydown);
	});

	const models = [
		{ alias: "Qwen 2.5 32B",   color: "cyan",    zero: 57.35, best: 60.21, bestShot: "2-shot",
		  shots: [{ k:1, m:59.02, s:1.8 },{ k:2, m:60.21, s:2.1 },{ k:3, m:59.85, s:1.5 },{ k:5, m:58.18, s:1.9 }] },
		{ alias: "Gemma 2 9B",     color: "violet",  zero: 57.71, best: 65.83, bestShot: "5-shot",
		  shots: [{ k:1, m:60.22, s:2.1 },{ k:2, m:62.84, s:1.8 },{ k:3, m:61.65, s:2.4 },{ k:5, m:65.83, s:2.84 }] },
		{ alias: "Llama 3.1 8B",   color: "amber",   zero: 42.65, best: 61.41, bestShot: "3-shot",
		  shots: [{ k:1, m:50.90, s:3.2 },{ k:2, m:58.66, s:2.7 },{ k:3, m:61.41, s:2.1 },{ k:5, m:56.87, s:2.9 }] },
		{ alias: "Llama 3.2 3B",   color: "emerald", zero: 40.50, best: 52.69, bestShot: "1-shot",
		  shots: [{ k:1, m:52.69, s:4.1 },{ k:2, m:52.33, s:3.8 },{ k:3, m:47.19, s:4.2 },{ k:5, m:52.09, s:4.3 }] },
		{ alias: "Aya Expanse 8B", color: "rose",    zero: 37.28, best: 48.99, bestShot: "1-shot",
		  shots: [{ k:1, m:48.99, s:5.2 },{ k:2, m:43.25, s:6.1 },{ k:3, m:48.99, s:5.8 },{ k:5, m:36.80, s:8.1 }] },
	];

	const strategies = [
		{ num:"01", name:"Direct", desc:"Plain multiple-choice. Pick the best property from the shortlist. No reasoning. Serves as the baseline carried from Experiment 1." },
		{ num:"02", name:"Chain-of-Thought", desc:"Reason in 1–2 sentences before answering. Helps the model explain its selection. Requires at least 2 demonstrations to be effective.", highlight: true },
		{ num:"03", name:"Persona", desc:"System prompt sets the role: \"You are a Semantic Web ontology engineer.\" Shifts outputs toward formal DBpedia vocabulary." },
		{ num:"04", name:"Translation-CoT", desc:"Mentally translate the Amharic mention to English inside the chain of thought, then reason to a candidate. No external translation call." },
	];
</script>

<svelte:head>
	<title>Multilingual LLM Benchmarking | {profile.name}</title>
	<meta name="description" content="Benchmarking LLMs on Amharic DBpedia property mapping with a retrieve-then-rerank pipeline." />
</svelte:head>

<section class="presentation-shell mx-auto max-w-[96rem] px-3 py-8 md:px-6 md:py-12">
	<div class="mb-4 flex flex-wrap items-center justify-between gap-3 px-2">
		<a href={`${base}/presentations`} class="inline-flex items-center gap-2 text-xs font-black tracking-[0.16em] text-muted-foreground uppercase transition hover:text-foreground">
			<Icon icon="iconoir:arrow-left" width="16" />
			All presentations
		</a>
		<div class="flex items-center gap-2">
			<span class="hidden text-xs font-bold text-muted-foreground sm:inline">Use ← → or space</span>
			<button type="button" class="deck-icon-button" onclick={toggleFullscreen} aria-label="Toggle fullscreen">
				<Icon icon="iconoir:expand" width="17" />
			</button>
		</div>
	</div>

	<div class="deck" bind:this={deckElement}>
		<div class="deck-progress" aria-hidden="true">
			<span style={`width:${((activeSlide + 1) / slideCount) * 100}%`}></span>
		</div>

		<div class="deck-track" style={`transform:translateX(-${activeSlide * 100}%)`}>

			<!-- SLIDE 01 — COVER -->
			<section class="slide slide-cover" aria-hidden={activeSlide !== 0}>
				<div class="cover-grid" aria-hidden="true"></div>
				<div class="cover-scatter" aria-hidden="true">
					{#each ["Qwen 2.5", "Gemma 2", "Llama 3.1", "Llama 3.2", "Aya Expanse"] as m, i (i)}
						<span class="model-chip" style={`--chip-index:${i}`}>{m}</span>
					{/each}
					<div class="scatter-orbit scatter-orbit-one"></div>
					<div class="scatter-orbit scatter-orbit-two"></div>
				</div>

				<div class="cover-copy">
					<div class="deck-kicker"><span></span>AI Benchmarking · Amharic DBpedia</div>
					<h1>
						Multilingual LLM
						<strong>Benchmarking</strong>
					</h1>
					<p>
						Mapping Amharic Wikipedia infobox properties to the English DBpedia ontology using a
						retrieve-then-rerank pipeline — five models, four experiments, Amharic script.
					</p>
				</div>

				<div class="cover-footer">
					<div>
						<span>Presented by</span>
						<strong>{profile.name}</strong>
						<small>{profile.role} · Addis Ababa, Ethiopia</small>
					</div>
					<div class="cover-stat-row">
						<div class="cover-stat"><strong>595</strong><small>DBpedia properties</small></div>
						<div class="cover-stat"><strong>279</strong><small>test examples</small></div>
						<div class="cover-stat"><strong>4</strong><small>experiments</small></div>
					</div>
				</div>
			</section>

			<!-- SLIDE 02 — PROBLEM STATEMENT -->
			<section class="slide slide-problem" aria-hidden={activeSlide !== 1}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span>Slide 02 · The Problem</p>
						<h2>Mapping <em>Amharic script</em> to a knowledge graph.</h2>
					</div>
					<p>
						DBpedia is a structured knowledge base extracted from Wikipedia. For Amharic Wikipedia to
						be part of it, every infobox property written in Amharic script must be linked to a
						canonical English DBpedia property. There are <strong>595</strong> possible targets.
					</p>
				</div>

				<div class="problem-layout">
					<div class="example-table" role="table" aria-label="Example property mapping inputs and outputs">
						<div class="example-head" role="row">
							<span>Amharic Input</span>
							<Icon icon="iconoir:arrow-right" width="16" />
							<span>Correct DBpedia Property</span>
						</div>
						<div class="example-row" role="row">
							<code class="amharic">የሰዎች ሀገር's የስነ ህዝብ</code>
							<Icon icon="iconoir:arrow-right" width="14" class="row-arrow" />
							<code class="property">dbo:populationTotal</code>
						</div>
						<div class="example-row" role="row">
							<code class="amharic">ሀገር's ርዕሰ ከተማ</code>
							<Icon icon="iconoir:arrow-right" width="14" class="row-arrow" />
							<code class="property">dbo:capital</code>
						</div>
						<div class="example-row" role="row">
							<code class="amharic">ሙዚቀኛ's የተወለደበት ቀን</code>
							<Icon icon="iconoir:arrow-right" width="14" class="row-arrow" />
							<code class="property">dbo:birthDate</code>
						</div>
						<div class="example-row" role="row">
							<code class="amharic">ሀገር's ስፋት</code>
							<Icon icon="iconoir:arrow-right" width="14" class="row-arrow" />
							<code class="property">dbo:areaTotal</code>
						</div>
					</div>

					<div class="dataset-panel">
						<p class="deck-kicker"><span></span>Dataset</p>
						<a
							href="https://huggingface.co/datasets/dice-research/amharic-property-mapping"
							target="_blank"
							rel="noreferrer"
							class="dataset-name dataset-link"
						>
							dice-research / amharic-property-mapping
							<Icon icon="iconoir:arrow-up-right" width="13" class="dataset-arrow" />
						</a>
						<div class="split-stats">
							<article>
								<strong>2,261</strong>
								<span>train</span>
							</article>
							<article>
								<strong>251</strong>
								<span>validation</span>
							</article>
							<article class="stat-highlight">
								<strong>279</strong>
								<span>test</span>
								<small>all results here</small>
							</article>
						</div>
						<div class="format-note">
							<Icon icon="iconoir:info-circle" width="15" />
							<span>Input: <code>&lt;entity type&gt;'s &lt;property mention&gt;</code></span>
						</div>
					</div>
				</div>
			</section>

			<!-- SLIDE 03 — PIPELINE ARCHITECTURE -->
			<section class="slide slide-pipeline" aria-hidden={activeSlide !== 2}>
				<div class="slide-heading slide-heading-narrow">
					<div>
						<p class="deck-kicker"><span></span>Slide 03 · System Design</p>
						<h2>Retrieve, then <em>rerank.</em></h2>
					</div>
					<p>
						Searching all 595 labels per LLM call would exceed context limits. A dense retriever
						narrows the field to 10 candidates in milliseconds; the LLM then picks one.
					</p>
				</div>

				<div class="arch-wrapper">
					<!-- Main pipeline flow -->
					<div class="arch-flow">
						<div class="arch-node arch-input">
							<div class="arch-tag">INPUT</div>
							<div class="arch-input-text">ሀገር's ርዕሰ ከተማ</div>
							<div class="arch-input-sub">Amharic property mention</div>
						</div>

						<div class="arch-arrow">
							<div class="arch-line"></div>
							<span class="arch-label">encode</span>
						</div>

						<div class="arch-stage arch-stage-1">
							<div class="arch-stage-badge">01</div>
							<div class="arch-stage-title">Retriever</div>
							<div class="arch-stage-model">Afro-XLM-R</div>
							<div class="arch-dots" aria-hidden="true">
								{#each Array(12) as _, i (i)}
									<div class="arch-dot" style={`opacity:${i < 3 ? 1 : 0.2}`}></div>
								{/each}
							</div>
							<div class="arch-stage-out">cosine similarity × 595 labels</div>
						</div>

						<div class="arch-arrow">
							<div class="arch-line"></div>
							<span class="arch-label">top 10</span>
						</div>

						<div class="arch-stage arch-stage-2">
							<div class="arch-stage-badge arch-badge-cyan">02</div>
							<div class="arch-stage-title">Reranker</div>
							<div class="arch-stage-model">LLM via DSPy</div>
							<div class="arch-candidates">
								{#each ["candidate 1", "candidate 2", "···", "candidate 10"] as c (c)}
									<div class="arch-cand">{c}</div>
								{/each}
							</div>
							<div class="arch-stage-out">selects 1 answer</div>
						</div>

						<div class="arch-arrow">
							<div class="arch-line"></div>
							<span class="arch-label">answer</span>
						</div>

						<div class="arch-node arch-output">
							<div class="arch-tag arch-tag-out">OUTPUT</div>
							<div class="arch-output-text">dbo:capital</div>
						</div>
					</div>

					<!-- Bottom row: label bank + answer snapping -->
					<div class="arch-bottom">
						<div class="arch-bank">
							<Icon icon="iconoir:db" width="16" />
							<span><strong>595</strong> DBpedia property labels — pre-encoded at startup into a fixed vector index</span>
						</div>
						<div class="arch-snap">
							<Icon icon="iconoir:magic-wand" width="16" />
							<span><strong>Answer snapping</strong> — fuzzy-matches the LLM's raw output to the nearest candidate (<code>token_sort_ratio ≥ 85</code>). Falls back to top-1 retriever result if nothing matches.</span>
						</div>
					</div>
				</div>
			</section>

			<!-- SLIDE 04 — THE MATH -->
			<section class="slide slide-math" aria-hidden={activeSlide !== 3}>
				<div class="slide-heading slide-heading-narrow">
					<div>
						<p class="deck-kicker"><span></span>Slide 04 · The Mathematics</p>
						<h2>How retrieval and <em>evaluation</em> work.</h2>
					</div>
				</div>

				<div class="math-grid">
					<article class="math-card math-card-retrieval">
						<span class="math-label">Retrieval</span>
						<h3>Cosine similarity over 595 labels</h3>
						<div class="formula">
							<div class="formula-line">
								<em>L</em> ∈ ℝ<sup>595×d</sup> &nbsp;·&nbsp; <em>p</em> ∈ ℝ<sup>d</sup>
							</div>
							<div class="formula-line main-formula">
								s<sub>i</sub> = <span class="frac"><span>p · L<sub>i</sub></span><span>‖p‖ · ‖L<sub>i</sub>‖</span></span>
							</div>
						</div>
						<code class="code-snippet">scores = premise @ labels.T<br />top10 = torch.topk(scores, k=10)</code>
					</article>

					<article class="math-card math-card-metric">
						<span class="math-label">Evaluation</span>
						<h3>Fuzzy-match accuracy</h3>
						<div class="formula">
							<div class="formula-line main-formula">
								Acc = <span class="frac"><span>1</span><span>N</span></span>
								∑ <strong>𝟙</strong>[FuzzyMatch(ŷ, y) ≥ 85] × 100
							</div>
						</div>
						<p class="math-note">
							Normalise → lowercase + whitespace collapse + Amharic homophone normalisation (amseg),
							then compare with RapidFuzz <code>token_sort_ratio</code>. N = 279.
						</p>
					</article>

					<article class="math-card math-card-seeds">
						<span class="math-label">Few-shot variance</span>
						<h3>Mean ± std over seeds</h3>
						<div class="formula">
							<div class="formula-line">
								μ<sub>k</sub> = <span class="frac"><span>1</span><span>N<sub>seeds</sub></span></span>
								∑ Acc(k, s)
							</div>
							<div class="formula-line">
								σ<sub>k</sub> = √<span class="sqrt-arg">
									<span class="frac"><span>1</span><span>N<sub>seeds</sub></span></span> ∑(Acc − μ)<sup>2</sup>
								</span>
							</div>
						</div>
						<p class="math-note">High σ means the result is demo-sensitive, not robustly learned.</p>
					</article>

					<article class="math-card math-card-vote">
						<span class="math-label">Ensemble</span>
						<h3>Majority vote</h3>
						<div class="formula">
							<div class="formula-line main-formula">
								ŷ<sub>vote</sub> = argmax<sub>c</sub> ∑<sub>m</sub> <strong>𝟙</strong>[ŷ<sub>m</sub> = c]
							</div>
						</div>
						<p class="math-note">
							Tie-break: the answer ranked highest by the retriever wins — a principled fallback
							because retriever rank reflects embedding similarity.
						</p>
					</article>
				</div>
			</section>

			<!-- SLIDE 05 — BASELINE RESULTS -->
			<section class="slide slide-baseline" aria-hidden={activeSlide !== 4}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span>Slide 05 · Experiment 1 · Multi-Model Baseline</p>
						<h2>Five models, one <em>retriever.</em></h2>
					</div>
					<p>
						Each model evaluated at 0-shot through 5-shot with 3 random seeds per setting.
						Few-shot columns show <strong>mean ± std</strong> across seeds. N = 279 test examples.
					</p>
				</div>

				<div class="baseline-layout">
					<div class="bar-chart" aria-label="Best accuracy per model">
						{#each models as m (m.alias)}
							<div class="bar-row">
								<span class="bar-label">{m.alias}</span>
								<div class="bar-track">
									<div class="bar-fill bar-fill-{m.color}" style={`width:${m.best}%`} aria-label="{m.alias}: {m.best}%"></div>
									<span class="bar-value">{m.best}%</span>
								</div>
								<span class="bar-meta">{m.bestShot}</span>
							</div>
						{/each}
					</div>

					<div class="baseline-table-wrap">
						<table class="result-table">
							<thead>
								<tr>
									<th>Model</th>
									<th>0-shot</th>
									<th>1-shot</th>
									<th>3-shot</th>
									<th>5-shot</th>
								</tr>
							</thead>
							<tbody>
								{#each models as m (m.alias)}
									{@const s1 = m.shots[0]}
									{@const s3 = m.shots[2]}
									{@const s5 = m.shots[3]}
									<tr class={m.alias === "Gemma 2 9B" ? "row-highlight" : ""}>
										<td>{m.alias}</td>
										<td>{m.zero}</td>
										<td class:cell-best={m.bestShot === "1-shot"}>{s1.m} <span class="std-tag">±{s1.s}</span></td>
										<td class:cell-best={m.bestShot === "3-shot"}>{s3.m} <span class="std-tag">±{s3.s}</span></td>
										<td class:cell-best={m.bestShot === "5-shot"}>{s5.m} <span class="std-tag">±{s5.s}</span></td>
									</tr>
								{/each}
							</tbody>
						</table>
						<p class="table-note">Cyan = best per model. Aya Expanse 5-shot (36.80 ± 8.1) is worse than 0-shot.</p>
					</div>
				</div>
			</section>

			<!-- SLIDE 06 — PROMPT ENGINEERING STRATEGIES -->
			<section class="slide slide-strategies" aria-hidden={activeSlide !== 5}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span>Slide 06 · Experiment 2 · Prompt Engineering</p>
						<h2>Four ways to ask the LLM to <em>choose.</em></h2>
					</div>
					<p>
						Same retriever shortlist across all strategies. Only the prompt structure changes.
						Best result: Gemma 2 + chain-of-thought at 5-shot → <strong>67.62%</strong>.
					</p>
				</div>

				<div class="strategy-list">
					{#each strategies as s (s.num)}
						<div class="strat-row {s.highlight ? 'strat-row-highlight' : ''}">
							<span class="strat-num">{s.num}</span>
							<div class="strat-body">
								<span class="strat-name">{s.name}</span>
								<span class="strat-desc">{s.desc}</span>
							</div>
							{#if s.highlight}
								<span class="strat-badge">best</span>
							{/if}
						</div>
					{/each}
				</div>

				<div class="cot-note">
					<Icon icon="iconoir:warning-triangle" width="16" />
					<span>Chain-of-Thought needs demonstrations — at 0-shot it hurts every model. Use ≥ 2 shots before enabling it.</span>
				</div>
			</section>

			<!-- SLIDE 07 — PROMPT ENGINEERING RESULTS -->
			<section class="slide slide-pe-results" aria-hidden={activeSlide !== 6}>
				<div class="slide-heading slide-heading-narrow">
					<div>
						<p class="deck-kicker"><span></span>Slide 07 · Prompt Engineering Results</p>
						<h2>CoT at 5-shot reaches <em>67.62%.</em></h2>
					</div>
					<p>Gemma 2 is the only model that consistently improves with every strategy.</p>
				</div>

				<div class="pe-results-layout">
					<div class="pe-best-table-wrap">
						<table class="result-table">
							<thead>
								<tr><th>Model</th><th>Strategy</th><th>Shots</th><th>Accuracy</th><th>Std</th></tr>
							</thead>
							<tbody>
								<tr class="row-highlight">
									<td>Gemma 2</td><td>cot</td><td>5</td><td class="cell-best">67.62%</td><td>±0.89</td>
								</tr>
								<tr>
									<td>Gemma 2</td><td>persona</td><td>5</td><td>66.19%</td><td>±3.47</td>
								</tr>
								<tr>
									<td>Gemma 2</td><td>translation-cot</td><td>3</td><td>64.40%</td><td>±2.65</td>
								</tr>
								<tr>
									<td>Llama 3.1</td><td>persona</td><td>3</td><td>63.80%</td><td>±0.78</td>
								</tr>
								<tr>
									<td>Qwen 2.5 32B</td><td>direct</td><td>2</td><td>60.45%</td><td>±5.51</td>
								</tr>
								<tr>
									<td>Llama 3.2</td><td>cot</td><td>4</td><td>56.87%</td><td>±2.16</td>
								</tr>
								<tr>
									<td>Aya Expanse</td><td>direct</td><td>1</td><td>48.27%</td><td>±7.82</td>
								</tr>
							</tbody>
						</table>
					</div>

					<div class="pe-detail">
						<p class="deck-kicker" style="color:var(--deck-violet)"><span></span>Gemma 2 · CoT grid</p>
						<div class="gemma-cot-grid">
							{#each [
								{ shots: "0", acc: 46.24, note: "hurts" },
								{ shots: "1", acc: 59.98, note: "" },
								{ shots: "2", acc: 65.71, note: "" },
								{ shots: "3", acc: 63.92, note: "" },
								{ shots: "4", acc: 63.08, note: "" },
								{ shots: "5", acc: 67.62, note: "best" }
							] as row (row.shots)}
								<div class="cot-cell {row.note === 'best' ? 'cot-best' : row.note === 'hurts' ? 'cot-hurts' : ''}">
									<span class="cot-shots">{row.shots}-shot</span>
									<strong>{row.acc}%</strong>
								</div>
							{/each}
						</div>

						<div class="pe-callout">
							<strong>67.62%</strong>
							<span>Overall project best — single model, 5 demonstrations</span>
						</div>
					</div>
				</div>
			</section>

			<!-- SLIDE 08 — TRANSLATION TECHNIQUES -->
			<section class="slide slide-translation" aria-hidden={activeSlide !== 7}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span>Slide 08 · Experiment 3 · Cross-Lingual Translation</p>
						<h2>Should the LLM see <em>Amharic or English?</em></h2>
					</div>
					<p>
						The same model translates its own input, then reranks. Translation runs
						<strong>once up front</strong> and is reused across all technique/shot/seed combinations.
					</p>
				</div>

				<div class="translation-layout">
					<article class="technique-card technique-pivot">
						<div class="technique-number">01</div>
						<h3>Pivot</h3>
						<p class="technique-desc">
							The Amharic premise is <strong>fully replaced</strong> by its English translation. The
							LLM never sees Amharic script.
						</p>
						<div class="technique-example">
							<div class="ex-row">
								<span class="ex-label">LLM sees:</span>
								<code>"country's population"</code>
							</div>
							<div class="ex-row">
								<span class="ex-label">Candidates:</span>
								<code>[dbo:populationTotal, …]</code>
							</div>
						</div>
						<p class="technique-verdict verdict-neutral">
							Tests a pure English pipeline. Translation errors propagate directly — no Amharic
							signal to fall back on.
						</p>
					</article>

					<article class="technique-card technique-augmented">
						<div class="technique-number">02</div>
						<h3>Augmented</h3>
						<p class="technique-desc">
							The Amharic premise is <strong>kept</strong> and the English translation is added as a
							separate reference field alongside it.
						</p>
						<div class="technique-example">
							<div class="ex-row">
								<span class="ex-label">premise:</span>
								<code class="amharic-sm">ሀገር's ህዝብ</code>
							</div>
							<div class="ex-row">
								<span class="ex-label">translation:</span>
								<code>"country's population"</code>
							</div>
						</div>
						<p class="technique-verdict verdict-good">
							Richer signal — the model can use whichever representation it trusts more. Augmented
							beats Pivot on every model at every shot count.
						</p>
					</article>
				</div>

				<div class="translation-flow">
					<div>Amharic input</div>
					<Icon icon="iconoir:arrow-right" width="17" />
					<div>same-model translator</div>
					<Icon icon="iconoir:arrow-right" width="17" />
					<div>English translation (cached)</div>
					<Icon icon="iconoir:arrow-right" width="17" />
					<div>Pivot or Augmented</div>
					<Icon icon="iconoir:arrow-right" width="17" />
					<div>LLM reranker</div>
				</div>
			</section>

			<!-- SLIDE 09 — TRANSLATION RESULTS -->
			<section class="slide slide-tr-results" aria-hidden={activeSlide !== 8}>
				<div class="slide-heading slide-heading-narrow">
					<div>
						<p class="deck-kicker"><span></span>Slide 09 · Translation Results</p>
						<h2>Augmented wins — context beats <em>replacement.</em></h2>
					</div>
				</div>

				<div class="tr-results-layout">
					<div class="tr-tables">
						<div>
							<p class="tr-table-label">Pivot (English only)</p>
							<table class="result-table result-table-sm">
								<thead><tr><th>Model</th><th>0-shot</th><th>Best</th></tr></thead>
								<tbody>
									<tr><td>Qwen 2.5 32B</td><td>53.76</td><td>52.93 (5s)</td></tr>
									<tr><td>Gemma 2</td><td>55.91</td><td>61.53 (4s)</td></tr>
									<tr><td>Llama 3.1</td><td>36.56</td><td>55.20 (3s)</td></tr>
									<tr><td>Llama 3.2</td><td>41.94</td><td>53.17 (5s)</td></tr>
									<tr><td>Aya Expanse</td><td>39.07</td><td>42.17 (3s)</td></tr>
								</tbody>
							</table>
						</div>
						<div>
							<p class="tr-table-label tr-table-label-good">Augmented (Amharic + English)</p>
							<table class="result-table result-table-sm">
								<thead><tr><th>Model</th><th>0-shot</th><th>Best</th></tr></thead>
								<tbody>
									<tr><td>Qwen 2.5 32B</td><td>57.35</td><td>54.12 (3s)</td></tr>
									<tr class="row-highlight"><td>Gemma 2</td><td>50.54</td><td class="cell-best">66.90 (4s)</td></tr>
									<tr><td>Llama 3.1</td><td class="cell-delta">53.41 ↑</td><td>59.02 (4s)</td></tr>
									<tr><td>Llama 3.2</td><td class="cell-delta">47.67 ↑</td><td>56.99 (4s)</td></tr>
									<tr><td>Aya Expanse</td><td class="cell-delta">42.29 ↑</td><td>53.65 (1s)</td></tr>
								</tbody>
							</table>
						</div>
					</div>

					<div class="tr-insights">
						<p class="deck-kicker" style="color:var(--deck-emerald)"><span></span>Key findings</p>
						<ul class="insight-bullets">
							<li>
								<span class="ib-dot ib-good"></span>
								<span><strong>Helps weaker models.</strong> Llama 3.1 0-shot: 42.65% → 53.41% (+10.76 pp). Models that struggle with Amharic script gain a meaningful boost from the English hint.</span>
							</li>
							<li>
								<span class="ib-dot ib-warn"></span>
								<span><strong>Hurts strong models at few-shot.</strong> Qwen 2.5 32B drops from 60.21% → 54.12% at 3-shot. The extra field adds noise for models already capable of cross-lingual reasoning.</span>
							</li>
							<li>
								<span class="ib-dot ib-good"></span>
								<span><strong>Gemma 2 augmented peak: 66.90% at 4-shot.</strong> Second-highest single-model result in the project, trailing only chain-of-thought prompting.</span>
							</li>
							<li>
								<span class="ib-dot ib-neutral"></span>
								<span><strong>Augmented always beats Pivot.</strong> Keeping the Amharic context alongside the translation is better than discarding it in every case tested.</span>
							</li>
						</ul>
					</div>
				</div>
			</section>

			<!-- SLIDE 10 — ENSEMBLE -->
			<section class="slide slide-ensemble" aria-hidden={activeSlide !== 9}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span>Slide 10 · Experiment 4 · Ensemble Methods</p>
						<h2>Five models vote better than <em>one chooses.</em></h2>
					</div>
					<p>
						Member predictions collected once per (shots, seed) and reused for both strategies.
						No recomputation on strategy switch.
					</p>
				</div>

				<div class="ensemble-layout">
					<div class="vote-diagram" aria-label="Majority vote example">
						<p class="vote-label">Example — all 5 models predict for one test item:</p>
						<div class="vote-members">
							{#each [
								{ model: "Qwen 2.5", pred: "dbo:capital", correct: true },
								{ model: "Gemma 2", pred: "dbo:capital", correct: true },
								{ model: "Llama 3.1", pred: "dbo:location", correct: false },
								{ model: "Llama 3.2", pred: "dbo:capital", correct: true },
								{ model: "Aya", pred: "dbo:country", correct: false }
							] as m (m.model)}
								<div class="vote-member {m.correct ? 'vote-correct' : 'vote-wrong'}">
									<span class="vote-model">{m.model}</span>
									<span class="vote-pred">{m.pred}</span>
									{#if m.correct}
										<Icon icon="iconoir:check-circle" width="14" />
									{:else}
										<Icon icon="iconoir:xmark-circle" width="14" />
									{/if}
								</div>
							{/each}
						</div>
						<div class="vote-result">
							<strong>Majority vote → dbo:capital ✓</strong>
							<span>(3/5 votes)</span>
						</div>
					</div>

					<div class="ensemble-results-panel">
						<table class="result-table">
							<thead>
								<tr><th>Strategy</th><th>0-shot</th><th>2-shot</th><th>3-shot</th><th>5-shot</th></tr>
							</thead>
							<tbody>
								<tr class="row-highlight">
									<td><strong>vote</strong></td>
									<td class="cell-delta">63.08</td>
									<td>66.19</td>
									<td class="cell-best">66.67</td>
									<td>66.19</td>
								</tr>
								<tr>
									<td>rerank-vote</td>
									<td>57.71</td>
									<td>58.78</td>
									<td>58.78</td>
									<td>58.78</td>
								</tr>
							</tbody>
						</table>
						<p class="table-note">Best solo model at 0-shot: Gemma 2 at 57.71%</p>

						<div class="ensemble-callouts">
							<div class="e-callout e-callout-good">
								<strong>63.08%</strong>
								<span>Vote at 0-shot beats all individual models' best few-shot scores (except Gemma 2)</span>
							</div>
							<div class="e-callout e-callout-warn">
								<strong>Rerank-vote underperforms</strong>
								<span>Reranker sees candidates but no few-shot demos — adds noise, not signal</span>
							</div>
						</div>
					</div>
				</div>
			</section>

			<!-- SLIDE 11 — CROSS-EXPERIMENT COMPARISON -->
			<section class="slide slide-comparison" aria-hidden={activeSlide !== 10}>
				<div class="slide-heading slide-heading-narrow">
					<div>
						<p class="deck-kicker"><span></span>Slide 11 · Results Summary</p>
						<h2>Every experiment <em>raises accuracy.</em></h2>
					</div>
				</div>

				<div class="comparison-layout">
					<div class="best-table-wrap">
						<table class="result-table">
							<thead>
								<tr><th>Experiment</th><th>Best Config</th><th>Accuracy</th><th>Std</th></tr>
							</thead>
							<tbody>
								<tr>
									<td>1 · Baseline</td>
									<td>Gemma 2 · 5-shot</td>
									<td>65.83%</td>
									<td>±2.84</td>
								</tr>
								<tr>
									<td>3 · Translation</td>
									<td>Gemma 2 + Augmented · 4-shot</td>
									<td>66.90%</td>
									<td>±1.50</td>
								</tr>
								<tr>
									<td>4 · Ensemble</td>
									<td>Vote · 3-shot</td>
									<td>66.67%</td>
									<td>±2.03</td>
								</tr>
								<tr class="row-highlight">
									<td>2 · Prompt Engineering</td>
									<td>Gemma 2 + CoT · 5-shot</td>
									<td class="cell-best">67.62%</td>
									<td>±0.89</td>
								</tr>
								<tr class="row-bonus">
									<td>4 · Ensemble (0-shot)</td>
									<td>Vote · no demos</td>
									<td>63.08%</td>
									<td>—</td>
								</tr>
							</tbody>
						</table>
					</div>

					<div class="summary-panel">
						<p class="deck-kicker" style="color:var(--deck-amber)"><span></span>What we learned</p>
						<ul class="summary-bullets">
							<li>
								<span class="ib-dot ib-good"></span>
								<span><strong>Chain-of-thought + 5 shots</strong> is the most accurate single-model strategy at 67.62% ± 0.89.</span>
							</li>
							<li>
								<span class="ib-dot ib-good"></span>
								<span><strong>Ensemble vote at 0-shot (63.08%)</strong> beats most individual few-shot results — useful when you have no labeled examples.</span>
							</li>
							<li>
								<span class="ib-dot ib-warn"></span>
								<span><strong>Translation helps weaker models, hurts stronger ones.</strong> Keep Amharic context alongside the English translation.</span>
							</li>
							<li>
								<span class="ib-dot ib-neutral"></span>
								<span><strong>CoT at 0-shot hurts.</strong> Add at least 2 demonstrations before enabling chain-of-thought.</span>
							</li>
							<li>
								<span class="ib-dot ib-neutral"></span>
								<span><strong>Next step:</strong> fine-tune Afro-XLM-R on the training split — the retriever sets the ceiling for everything above it.</span>
							</li>
						</ul>

						<div class="ceiling-box">
							<div class="ceiling-num">67.62%</div>
							<span>project ceiling · Gemma 2 · CoT · 5-shot</span>
						</div>
					</div>
				</div>
			</section>

		</div><!-- end deck-track -->

		<div class="deck-controls">
			<button type="button" onclick={() => goToSlide(activeSlide - 1)} disabled={activeSlide === 0} aria-label="Previous slide">
				<Icon icon="iconoir:arrow-left" width="19" />
			</button>

			<div class="deck-dots" aria-label="Choose slide">
				{#each slideNumbers as index (index)}
					<button
						type="button"
						class:active={activeSlide === index}
						onclick={() => goToSlide(index)}
						aria-label={`Go to slide ${index + 1}`}
						aria-current={activeSlide === index ? "step" : undefined}
					></button>
				{/each}
			</div>

			<span class="deck-counter">{String(activeSlide + 1).padStart(2, "0")} / {String(slideCount).padStart(2, "0")}</span>

			<button type="button" onclick={() => goToSlide(activeSlide + 1)} disabled={activeSlide === slideCount - 1} aria-label="Next slide">
				<Icon icon="iconoir:arrow-right" width="19" />
			</button>
		</div>
	</div><!-- end deck -->
</section>

<style>
	/* ── Shell & deck infrastructure ─────────────────────── */
	.presentation-shell {
		--deck-ink: #f4f7fb;
		--deck-muted: rgba(244, 247, 251, 0.62);
		--deck-line: rgba(255, 255, 255, 0.12);
		--deck-cyan: #6fe5ec;
		--deck-violet: #a594ff;
		--deck-amber: #f6c96b;
		--deck-emerald: #75dfa8;
		--deck-rose: #fb7185;
	}

	.deck {
		position: relative;
		overflow: hidden;
		min-height: min(80vh, 800px);
		border: 1px solid rgba(255, 255, 255, 0.12);
		border-radius: 2rem;
		background: #0d1017;
		color: var(--deck-ink);
		box-shadow: 0 40px 140px rgba(15, 23, 42, 0.32);
		isolation: isolate;
	}
	.deck:fullscreen { width: 100vw; height: 100vh; min-height: 100vh; border: 0; border-radius: 0; }

	.deck-icon-button {
		display: grid; place-items: center;
		width: 2rem; height: 2rem;
		border-radius: 0.5rem;
		border: 1px solid color-mix(in oklab, currentColor 20%, transparent);
		color: var(--muted-foreground);
		transition: color 200ms, background 200ms;
	}
	.deck-icon-button:hover { color: var(--foreground); background: color-mix(in oklab, currentColor 8%, transparent); }

	.deck-progress { position: absolute; top: 0; right: 0; left: 0; z-index: 20; height: 3px; background: rgba(255,255,255,0.06); }
	.deck-progress span { display: block; height: 100%; background: linear-gradient(90deg, var(--deck-violet), var(--deck-cyan)); transition: width 500ms cubic-bezier(0.22, 1, 0.36, 1); }

	.deck-track { display: flex; min-height: inherit; transition: transform 650ms cubic-bezier(0.22, 1, 0.36, 1); }

	.slide {
		position: relative; flex: 0 0 100%; width: 100%; min-height: inherit;
		overflow: hidden; padding: clamp(2rem, 4.5vw, 5rem); padding-bottom: clamp(6rem, 8vw, 7.5rem);
	}

	.deck-kicker {
		display: flex; align-items: center; gap: 0.65rem;
		color: var(--deck-cyan);
		font-size: 0.7rem; font-weight: 900; letter-spacing: 0.2em; text-transform: uppercase;
	}
	.deck-kicker span { width: 1.8rem; height: 2px; background: currentColor; }

	/* ── Deck controls ─────────────────────────────────────── */
	.deck-controls {
		position: absolute; bottom: 0; left: 0; right: 0;
		display: flex; align-items: center; justify-content: center; gap: 0.75rem;
		padding: 1.4rem 2rem;
		background: linear-gradient(to top, rgba(13,16,23,0.98) 60%, transparent);
		z-index: 10;
	}
	.deck-controls button { display: grid; place-items: center; width: 2.2rem; height: 2.2rem; border-radius: 50%; border: 1px solid rgba(255,255,255,0.15); color: rgba(244,247,251,0.7); transition: all 200ms; }
	.deck-controls button:hover:not(:disabled) { border-color: var(--deck-violet); color: var(--deck-violet); background: rgba(165,148,255,0.1); }
	.deck-controls button:disabled { opacity: 0.25; cursor: not-allowed; }

	.deck-dots { display: flex; gap: 0.4rem; }
	.deck-dots button { width: 0.45rem; height: 0.45rem; border-radius: 50%; background: rgba(255,255,255,0.2); border: 0; padding: 0; transition: background 250ms, transform 250ms; }
	.deck-dots button.active { background: var(--deck-violet); transform: scale(1.5); }

	.deck-counter { min-width: 4rem; text-align: center; font-size: 0.72rem; font-weight: 700; letter-spacing: 0.1em; color: var(--deck-muted); }

	/* ── Shared slide heading ─────────────────────────────── */
	.slide-heading { display: grid; grid-template-columns: 1fr 1fr; gap: 1.2rem 3rem; align-items: start; margin-bottom: 2.2rem; }
	.slide-heading-narrow { grid-template-columns: 1fr; max-width: 60rem; margin-bottom: 1.8rem; }
	.slide-heading h2 { font-family: var(--font-display); font-size: clamp(1.8rem, 3.5vw, 3rem); font-weight: 900; letter-spacing: -0.04em; line-height: 1.05; margin-top: 0.5rem; }
	.slide-heading h2 em { font-style: normal; color: var(--deck-cyan); }
	.slide-heading > p { color: var(--deck-muted); font-size: 0.93rem; line-height: 1.7; padding-top: 0.2rem; }

	/* ── Shared table styles ─────────────────────────────── */
	.result-table { width: 100%; border-collapse: collapse; font-size: 0.8rem; }
	.result-table th { text-align: left; padding: 0.55rem 0.85rem; color: var(--deck-muted); font-weight: 700; font-size: 0.7rem; letter-spacing: 0.08em; text-transform: uppercase; border-bottom: 1px solid rgba(255,255,255,0.08); white-space: nowrap; }
	.result-table td { padding: 0.6rem 0.85rem; color: var(--deck-ink); border-bottom: 1px solid rgba(255,255,255,0.05); white-space: nowrap; }
	.row-highlight td { background: rgba(165,148,255,0.07); }
	.row-bonus td { background: rgba(111,229,236,0.05); color: var(--deck-muted); font-style: italic; }
	.cell-best { color: var(--deck-cyan) !important; font-weight: 800; }
	.cell-delta { color: var(--deck-emerald) !important; font-weight: 700; }
	.result-table-sm { font-size: 0.77rem; }
	.table-note { margin-top: 0.6rem; font-size: 0.7rem; color: var(--deck-muted); }
	.std-tag { font-size: 0.67rem; color: var(--deck-muted); font-weight: 600; }

	/* Shared bullet list */
	.insight-bullets, .summary-bullets {
		list-style: none; padding: 0; margin: 0;
		display: grid; gap: 0.75rem;
	}
	.insight-bullets li, .summary-bullets li {
		display: flex; align-items: flex-start; gap: 0.6rem;
		font-size: 0.82rem; line-height: 1.65; color: var(--deck-muted);
	}
	.insight-bullets li strong, .summary-bullets li strong { color: var(--deck-ink); }
	.ib-dot { flex-shrink: 0; width: 0.5rem; height: 0.5rem; border-radius: 50%; margin-top: 0.42rem; }
	.ib-good    { background: var(--deck-emerald); }
	.ib-warn    { background: var(--deck-amber); }
	.ib-neutral { background: rgba(255,255,255,0.3); }

	/* ── SLIDE 01 — COVER ─────────────────────────────────── */
	.slide-cover {
		display: flex; flex-direction: column; justify-content: space-between;
		background:
			radial-gradient(circle at 80% 15%, rgba(165,148,255,0.22), transparent 34%),
			radial-gradient(circle at 10% 85%, rgba(111,229,236,0.17), transparent 32%),
			#0d1017;
	}
	.cover-grid { position: absolute; inset: 0; opacity: 0.15; background-image: linear-gradient(rgba(255,255,255,0.14) 1px, transparent 1px), linear-gradient(90deg, rgba(255,255,255,0.14) 1px, transparent 1px); background-size: 52px 52px; mask-image: radial-gradient(circle at 65% 35%, black, transparent 70%); }
	.cover-scatter { position: absolute; inset: 0; pointer-events: none; }
	.scatter-orbit { position: absolute; border: 1px dashed rgba(255,255,255,0.12); border-radius: 50%; animation: coverSpin 20s linear infinite; }
	.scatter-orbit-one { inset: 12% 6%; }
	.scatter-orbit-two { inset: 22% 14%; animation-direction: reverse; animation-duration: 14s; }
	.model-chip { position: absolute; padding: 0.28rem 0.7rem; border-radius: 999px; border: 1px solid rgba(255,255,255,0.14); background: rgba(255,255,255,0.05); font-size: 0.65rem; font-weight: 800; letter-spacing: 0.1em; color: var(--deck-muted); animation: chipFloat 5s ease-in-out infinite; animation-delay: calc(var(--chip-index) * -0.9s); }
	.model-chip:nth-child(1) { top: 18%; right: 22%; }
	.model-chip:nth-child(2) { top: 32%; right: 10%; }
	.model-chip:nth-child(3) { top: 55%; right: 18%; }
	.model-chip:nth-child(4) { top: 68%; right:  8%; }
	.model-chip:nth-child(5) { top: 78%; right: 24%; }
	.cover-copy { position: relative; z-index: 3; }
	.cover-copy h1 { max-width: 56rem; margin-top: 1.6rem; font-family: var(--font-display); font-size: clamp(3.5rem, 8vw, 8.5rem); font-weight: 900; letter-spacing: -0.07em; line-height: 0.82; }
	.cover-copy h1 strong { display: block; color: transparent; -webkit-text-stroke: 1.5px rgba(255,255,255,0.6); }
	.cover-copy p { max-width: 48rem; margin-top: 2rem; color: var(--deck-muted); font-size: clamp(0.95rem, 1.4vw, 1.2rem); line-height: 1.75; }
	.cover-footer { position: relative; z-index: 3; display: flex; align-items: flex-end; justify-content: space-between; gap: 2rem; flex-wrap: wrap; }
	.cover-footer > div:first-child { display: grid; gap: 0.2rem; }
	.cover-footer span, .cover-footer small { color: var(--deck-muted); font-size: 0.68rem; font-weight: 800; letter-spacing: 0.16em; text-transform: uppercase; }
	.cover-footer strong { font-size: 1.15rem; }
	.cover-stat-row { display: flex; gap: 1.2rem; }
	.cover-stat { display: grid; place-items: center; padding: 0.8rem 1.2rem; border: 1px solid var(--deck-line); border-radius: 1.1rem; background: rgba(255,255,255,0.04); gap: 0.2rem; }
	.cover-stat strong { font-family: var(--font-display); font-size: 1.8rem !important; font-weight: 900; color: var(--deck-violet) !important; letter-spacing: -0.04em; }
	.cover-stat small { font-size: 0.6rem !important; color: var(--deck-muted) !important; }

	/* ── SLIDE 02 — PROBLEM ───────────────────────────────── */
	.problem-layout { display: grid; grid-template-columns: 1.1fr 0.9fr; gap: 2rem; align-items: start; }
	.example-table { border: 1px solid var(--deck-line); border-radius: 1.2rem; overflow: hidden; }
	.example-head { display: flex; align-items: center; gap: 0.8rem; padding: 0.75rem 1.2rem; background: rgba(165,148,255,0.08); border-bottom: 1px solid var(--deck-line); font-size: 0.7rem; font-weight: 800; letter-spacing: 0.1em; text-transform: uppercase; color: var(--deck-violet); }
	.example-head :global(svg) { opacity: 0.5; }
	.example-row { display: grid; grid-template-columns: 1fr auto 1fr; align-items: center; gap: 0.8rem; padding: 0.75rem 1.2rem; border-bottom: 1px solid rgba(255,255,255,0.04); transition: background 200ms; }
	.example-row:last-child { border-bottom: 0; }
	.example-row:hover { background: rgba(255,255,255,0.03); }
	.row-arrow { opacity: 0.4; justify-self: center; }
	code.amharic { font-family: var(--font-sans); font-size: 0.88rem; color: var(--deck-cyan); }
	code.property { font-size: 0.8rem; color: var(--deck-emerald); background: rgba(117,223,168,0.08); padding: 0.15rem 0.45rem; border-radius: 0.4rem; }
	.dataset-panel { display: grid; gap: 1.1rem; }
	.dataset-name {
		font-family: var(--font-sans); font-size: 0.8rem; color: var(--deck-muted);
		padding: 0.6rem 0.9rem; border: 1px solid var(--deck-line); border-radius: 0.7rem;
		background: rgba(255,255,255,0.03);
	}
	.dataset-link {
		display: flex; align-items: center; gap: 0.4rem;
		text-decoration: none; transition: color 200ms, border-color 200ms;
	}
	.dataset-link:hover { color: var(--deck-cyan); border-color: rgba(111,229,236,0.35); }
	.dataset-link :global(.dataset-arrow) { opacity: 0.6; }
	.split-stats { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 0.7rem; }
	.split-stats article { display: grid; place-items: center; gap: 0.2rem; padding: 1rem 0.6rem; border: 1px solid var(--deck-line); border-radius: 1rem; text-align: center; }
	.split-stats strong { font-family: var(--font-display); font-size: 2rem; font-weight: 900; color: var(--deck-ink); letter-spacing: -0.04em; }
	.split-stats span { font-size: 0.65rem; font-weight: 700; color: var(--deck-muted); letter-spacing: 0.08em; text-transform: uppercase; }
	.split-stats small { font-size: 0.58rem; color: var(--deck-cyan); }
	.stat-highlight { border-color: rgba(111,229,236,0.3); background: rgba(111,229,236,0.05); }
	.stat-highlight strong { color: var(--deck-cyan); }
	.format-note { display: flex; align-items: center; gap: 0.6rem; font-size: 0.75rem; color: var(--deck-muted); padding: 0.6rem 0.9rem; border: 1px solid var(--deck-line); border-radius: 0.7rem; background: rgba(255,255,255,0.02); }
	.format-note code { color: var(--deck-amber); font-size: 0.78rem; }

	/* ── SLIDE 03 — PIPELINE ──────────────────────────────── */
	.arch-wrapper { display: grid; gap: 1.2rem; }

	.arch-flow {
		display: flex; align-items: center; gap: 0;
		padding: 1.8rem 2rem;
		border: 1px solid var(--deck-line);
		border-radius: 1.8rem;
		background: rgba(255,255,255,0.02);
	}

	.arch-node {
		display: grid; gap: 0.3rem; text-align: center;
		padding: 1rem 1.4rem;
		border-radius: 1.1rem;
		border: 1px solid var(--deck-line);
		background: rgba(255,255,255,0.04);
		flex-shrink: 0;
	}
	.arch-tag {
		font-size: 0.55rem; font-weight: 900; letter-spacing: 0.2em;
		text-transform: uppercase; color: var(--deck-muted);
	}
	.arch-tag-out { color: var(--deck-emerald); }
	.arch-input-text { font-family: var(--font-sans); font-size: 1rem; color: var(--deck-cyan); font-weight: 700; }
	.arch-input-sub { font-size: 0.6rem; color: var(--deck-muted); }
	.arch-output-text { font-size: 0.88rem; color: var(--deck-emerald); font-weight: 800; font-family: var(--font-sans); }

	.arch-arrow {
		display: flex; flex-direction: column; align-items: center; gap: 0.3rem;
		min-width: 3.5rem; flex: 0 0 auto;
	}
	.arch-line { width: 100%; height: 2px; background: linear-gradient(90deg, var(--deck-violet), var(--deck-cyan)); opacity: 0.45; }
	.arch-label { font-size: 0.58rem; font-weight: 800; letter-spacing: 0.1em; text-transform: uppercase; color: var(--deck-muted); }

	.arch-stage {
		flex: 1; display: grid; gap: 0.5rem;
		padding: 1.2rem 1.3rem; border-radius: 1.3rem;
	}
	.arch-stage-1 { border: 1px solid rgba(165,148,255,0.4); background: rgba(165,148,255,0.06); }
	.arch-stage-2 { border: 1px solid rgba(111,229,236,0.35); background: rgba(111,229,236,0.06); }
	.arch-stage-badge { font-size: 0.58rem; font-weight: 900; letter-spacing: 0.18em; color: var(--deck-violet); }
	.arch-badge-cyan { color: var(--deck-cyan); }
	.arch-stage-title { font-size: 1rem; font-weight: 800; }
	.arch-stage-model { font-size: 0.72rem; color: var(--deck-muted); }
	.arch-stage-out { font-size: 0.7rem; font-weight: 700; color: var(--deck-emerald); }

	.arch-dots { display: flex; flex-wrap: wrap; gap: 0.25rem; margin: 0.2rem 0; }
	.arch-dot { width: 0.5rem; height: 0.5rem; border-radius: 50%; background: var(--deck-violet); }

	.arch-candidates { display: grid; gap: 0.25rem; }
	.arch-cand { font-size: 0.62rem; color: var(--deck-cyan); background: rgba(111,229,236,0.07); padding: 0.15rem 0.4rem; border-radius: 0.3rem; font-family: var(--font-sans); }

	.arch-bottom {
		display: grid; grid-template-columns: 1fr 1fr; gap: 1rem;
	}
	.arch-bank, .arch-snap {
		display: flex; align-items: flex-start; gap: 0.7rem;
		padding: 0.85rem 1.1rem; border-radius: 0.9rem; font-size: 0.78rem; line-height: 1.6;
	}
	.arch-bank { border: 1px solid rgba(165,148,255,0.2); background: rgba(165,148,255,0.05); color: var(--deck-muted); }
	.arch-bank :global(svg) { color: var(--deck-violet); flex-shrink: 0; margin-top: 0.1rem; }
	.arch-bank strong { color: var(--deck-violet); }
	.arch-snap { border: 1px solid rgba(246,201,107,0.2); background: rgba(246,201,107,0.04); color: var(--deck-muted); }
	.arch-snap :global(svg) { color: var(--deck-amber); flex-shrink: 0; margin-top: 0.1rem; }
	.arch-snap strong { color: var(--deck-amber); }
	.arch-snap code { color: var(--deck-amber); font-size: 0.74rem; }

	/* ── SLIDE 04 — MATH ──────────────────────────────────── */
	.math-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; }
	.math-card { padding: 1.2rem 1.4rem; border-radius: 1.2rem; border: 1px solid var(--deck-line); display: grid; gap: 0.7rem; }
	.math-card-retrieval { border-color: rgba(165,148,255,0.3); background: rgba(165,148,255,0.05); }
	.math-card-metric    { border-color: rgba(111,229,236,0.3); background: rgba(111,229,236,0.05); }
	.math-card-seeds     { border-color: rgba(246,201,107,0.3); background: rgba(246,201,107,0.05); }
	.math-card-vote      { border-color: rgba(117,223,168,0.3); background: rgba(117,223,168,0.05); }
	.math-label { font-size: 0.62rem; font-weight: 900; letter-spacing: 0.18em; text-transform: uppercase; color: var(--deck-muted); }
	.math-card h3 { font-size: 0.9rem; font-weight: 800; line-height: 1.3; }
	.formula { display: grid; gap: 0.5rem; padding: 0.75rem; border-radius: 0.7rem; background: rgba(0,0,0,0.25); font-family: "Georgia", serif; }
	.formula-line { font-size: 0.85rem; color: var(--deck-ink); line-height: 1.8; }
	.main-formula { font-size: 1rem; }
	.frac { display: inline-grid; text-align: center; vertical-align: middle; gap: 1px; }
	.frac span:first-child { border-bottom: 1px solid currentColor; padding: 0 0.2rem; }
	.frac span:last-child  { padding: 0 0.2rem; }
	.sqrt-arg { border-top: 1px solid currentColor; padding: 0 0.2rem; }
	.code-snippet { font-size: 0.72rem; color: var(--deck-emerald); background: rgba(0,0,0,0.3); padding: 0.5rem 0.75rem; border-radius: 0.5rem; white-space: pre; display: block; line-height: 1.7; }
	.math-note { font-size: 0.75rem; color: var(--deck-muted); line-height: 1.6; }
	.math-note code { color: var(--deck-amber); }

	/* ── SLIDE 05 — BASELINE ──────────────────────────────── */
	.baseline-layout { display: grid; grid-template-columns: 0.9fr 1.1fr; gap: 2rem; align-items: start; }
	.bar-chart { display: grid; gap: 0.9rem; }
	.bar-row { display: grid; grid-template-columns: 7rem 1fr 3.5rem; align-items: center; gap: 0.75rem; }
	.bar-label { font-size: 0.75rem; font-weight: 700; color: var(--deck-muted); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
	.bar-track { position: relative; height: 1.7rem; border-radius: 0.5rem; background: rgba(255,255,255,0.05); overflow: hidden; }
	.bar-fill { height: 100%; border-radius: 0.5rem; transition: width 800ms cubic-bezier(0.22, 1, 0.36, 1); }
	.bar-fill-cyan    { background: linear-gradient(90deg, rgba(111,229,236,0.3), rgba(111,229,236,0.7)); }
	.bar-fill-violet  { background: linear-gradient(90deg, rgba(165,148,255,0.3), rgba(165,148,255,0.8)); }
	.bar-fill-amber   { background: linear-gradient(90deg, rgba(246,201,107,0.3), rgba(246,201,107,0.7)); }
	.bar-fill-emerald { background: linear-gradient(90deg, rgba(117,223,168,0.3), rgba(117,223,168,0.6)); }
	.bar-fill-rose    { background: linear-gradient(90deg, rgba(251,113,133,0.3), rgba(251,113,133,0.6)); }
	.bar-value { position: absolute; right: 0.5rem; top: 50%; translate: 0 -50%; font-size: 0.7rem; font-weight: 800; color: var(--deck-ink); }
	.bar-meta { font-size: 0.65rem; font-weight: 700; color: var(--deck-muted); text-align: right; }
	.baseline-table-wrap { overflow-x: auto; }

	/* ── SLIDE 06 — STRATEGIES ────────────────────────────── */
	.strategy-list { display: grid; gap: 0.6rem; margin-bottom: 1.2rem; }
	.strat-row {
		display: flex; align-items: flex-start; gap: 1rem;
		padding: 0.9rem 1.2rem;
		border-radius: 1rem;
		border: 1px solid rgba(255,255,255,0.07);
		background: rgba(255,255,255,0.025);
	}
	.strat-row-highlight {
		border-color: rgba(165,148,255,0.35);
		background: rgba(165,148,255,0.07);
	}
	.strat-num { flex-shrink: 0; font-size: 0.62rem; font-weight: 900; letter-spacing: 0.15em; color: var(--deck-muted); min-width: 1.8rem; margin-top: 0.15rem; }
	.strat-body { flex: 1; display: flex; flex-direction: column; gap: 0.25rem; }
	.strat-name { font-size: 0.9rem; font-weight: 800; }
	.strat-row-highlight .strat-name { color: var(--deck-violet); }
	.strat-desc { font-size: 0.78rem; color: var(--deck-muted); line-height: 1.6; }
	.strat-badge { flex-shrink: 0; padding: 0.18rem 0.55rem; border-radius: 999px; font-size: 0.58rem; font-weight: 900; letter-spacing: 0.12em; text-transform: uppercase; border: 1px solid rgba(165,148,255,0.45); background: rgba(165,148,255,0.1); color: var(--deck-violet); }

	.cot-note {
		display: flex; align-items: flex-start; gap: 0.6rem;
		padding: 0.75rem 1rem;
		border-radius: 0.8rem;
		border: 1px solid rgba(246,201,107,0.2);
		background: rgba(246,201,107,0.04);
		font-size: 0.78rem; color: var(--deck-muted); line-height: 1.55;
	}
	.cot-note :global(svg) { color: var(--deck-amber); flex-shrink: 0; margin-top: 0.05rem; }

	/* ── SLIDE 07 — PE RESULTS ────────────────────────────── */
	.pe-results-layout { display: grid; grid-template-columns: 1.1fr 0.9fr; gap: 2rem; align-items: start; }
	.pe-best-table-wrap { overflow-x: auto; }
	.pe-detail { display: grid; gap: 1.2rem; }
	.gemma-cot-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 0.5rem; }
	.cot-cell { display: grid; place-items: center; gap: 0.2rem; padding: 0.8rem 0.4rem; border-radius: 0.8rem; border: 1px solid var(--deck-line); background: rgba(255,255,255,0.025); }
	.cot-shots { font-size: 0.6rem; font-weight: 800; color: var(--deck-muted); letter-spacing: 0.1em; text-transform: uppercase; }
	.cot-cell strong { font-size: 0.95rem; font-weight: 800; }
	.cot-best { border-color: rgba(165,148,255,0.5); background: rgba(165,148,255,0.1); }
	.cot-best strong { color: var(--deck-violet); }
	.cot-hurts { opacity: 0.55; }
	.pe-callout { display: flex; align-items: center; gap: 1rem; padding: 1rem 1.4rem; border-radius: 1rem; border: 1px solid rgba(165,148,255,0.4); background: rgba(165,148,255,0.08); }
	.pe-callout strong { font-family: var(--font-display); font-size: 2rem; font-weight: 900; color: var(--deck-violet); letter-spacing: -0.04em; }
	.pe-callout span { font-size: 0.8rem; color: var(--deck-muted); line-height: 1.5; }

	/* ── SLIDE 08 — TRANSLATION ───────────────────────────── */
	.translation-layout { display: grid; grid-template-columns: 1fr 1fr; gap: 1.5rem; margin-bottom: 1.5rem; }
	.technique-card { display: grid; gap: 0.85rem; padding: 1.4rem; border-radius: 1.3rem; border: 1px solid var(--deck-line); }
	.technique-pivot { border-color: rgba(246,201,107,0.25); background: rgba(246,201,107,0.04); }
	.technique-augmented { border-color: rgba(117,223,168,0.3); background: rgba(117,223,168,0.05); }
	.technique-number { font-size: 0.62rem; font-weight: 900; letter-spacing: 0.18em; color: var(--deck-muted); }
	.technique-card h3 { font-size: 1.05rem; font-weight: 800; }
	.technique-desc { font-size: 0.8rem; color: var(--deck-muted); line-height: 1.65; }
	.technique-example { display: grid; gap: 0.4rem; padding: 0.75rem; border-radius: 0.7rem; background: rgba(0,0,0,0.25); }
	.ex-row { display: flex; gap: 0.6rem; align-items: center; font-size: 0.77rem; }
	.ex-label { font-weight: 800; color: var(--deck-muted); min-width: 5.5rem; font-size: 0.7rem; }
	.ex-row code { color: var(--deck-cyan); }
	code.amharic-sm { color: var(--deck-amber); font-family: var(--font-sans); }
	.technique-verdict { font-size: 0.77rem; color: var(--deck-muted); line-height: 1.6; padding: 0.6rem 0.8rem; border-radius: 0.6rem; }
	.verdict-neutral { background: rgba(255,255,255,0.04); border: 1px solid rgba(255,255,255,0.06); }
	.verdict-good { background: rgba(117,223,168,0.07); border: 1px solid rgba(117,223,168,0.2); color: var(--deck-emerald); }
	.translation-flow { display: flex; align-items: center; gap: 0.6rem; flex-wrap: wrap; padding: 0.9rem 1.2rem; border-radius: 0.9rem; border: 1px solid var(--deck-line); background: rgba(255,255,255,0.025); font-size: 0.77rem; }
	.translation-flow div { padding: 0.3rem 0.7rem; border-radius: 0.5rem; border: 1px solid rgba(255,255,255,0.1); background: rgba(255,255,255,0.04); }
	.translation-flow :global(svg) { opacity: 0.4; }

	/* ── SLIDE 09 — TRANSLATION RESULTS ──────────────────── */
	.tr-results-layout { display: grid; grid-template-columns: 1.1fr 0.9fr; gap: 2rem; align-items: start; }
	.tr-tables { display: grid; gap: 1.2rem; }
	.tr-table-label { font-size: 0.7rem; font-weight: 900; letter-spacing: 0.12em; text-transform: uppercase; color: var(--deck-muted); margin-bottom: 0.5rem; }
	.tr-table-label-good { color: var(--deck-emerald); }
	.tr-insights { display: grid; gap: 0.9rem; }

	/* ── SLIDE 10 — ENSEMBLE ──────────────────────────────── */
	.ensemble-layout { display: grid; grid-template-columns: 0.9fr 1.1fr; gap: 2rem; align-items: start; }
	.vote-diagram { display: grid; gap: 0.9rem; padding: 1.3rem; border: 1px solid var(--deck-line); border-radius: 1.3rem; background: rgba(255,255,255,0.025); }
	.vote-label { font-size: 0.7rem; font-weight: 700; color: var(--deck-muted); }
	.vote-members { display: grid; gap: 0.45rem; }
	.vote-member { display: flex; align-items: center; justify-content: space-between; gap: 0.5rem; padding: 0.5rem 0.75rem; border-radius: 0.7rem; border: 1px solid transparent; font-size: 0.78rem; }
	.vote-correct { border-color: rgba(117,223,168,0.25); background: rgba(117,223,168,0.06); }
	.vote-wrong   { border-color: rgba(255,255,255,0.06); background: rgba(255,255,255,0.025); opacity: 0.65; }
	.vote-correct :global(svg) { color: var(--deck-emerald); }
	.vote-wrong   :global(svg) { color: rgba(255,255,255,0.3); }
	.vote-model { font-weight: 700; min-width: 6rem; }
	.vote-pred { font-family: var(--font-sans); font-size: 0.73rem; color: var(--deck-cyan); flex: 1; text-align: center; }
	.vote-result { display: flex; align-items: center; gap: 0.7rem; padding: 0.65rem 0.9rem; border-radius: 0.7rem; background: rgba(117,223,168,0.08); border: 1px solid rgba(117,223,168,0.25); font-size: 0.82rem; }
	.vote-result strong { color: var(--deck-emerald); }
	.vote-result span { color: var(--deck-muted); font-size: 0.72rem; }
	.ensemble-results-panel { display: grid; gap: 1.1rem; }
	.ensemble-callouts { display: grid; gap: 0.7rem; }
	.e-callout { display: flex; gap: 1rem; align-items: center; padding: 0.8rem 1rem; border-radius: 0.9rem; border: 1px solid var(--deck-line); font-size: 0.78rem; }
	.e-callout-good { border-color: rgba(117,223,168,0.25); background: rgba(117,223,168,0.05); }
	.e-callout-warn { border-color: rgba(246,201,107,0.2); background: rgba(246,201,107,0.04); }
	.e-callout strong { font-size: 0.88rem; font-weight: 800; white-space: nowrap; color: var(--deck-emerald); }
	.e-callout-warn strong { color: var(--deck-amber); }
	.e-callout span { color: var(--deck-muted); line-height: 1.5; }

	/* ── SLIDE 11 — COMPARISON ────────────────────────────── */
	.comparison-layout { display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; align-items: start; }
	.best-table-wrap { overflow-x: auto; }
	.summary-panel { display: grid; gap: 1.2rem; }
	.ceiling-box {
		display: flex; align-items: center; gap: 1.2rem;
		padding: 1rem 1.4rem;
		border: 1px solid rgba(165,148,255,0.35); border-radius: 1.1rem;
		background: rgba(165,148,255,0.07);
	}
	.ceiling-num { font-family: var(--font-display); font-size: 2.2rem; font-weight: 900; color: var(--deck-violet); letter-spacing: -0.05em; line-height: 1; flex-shrink: 0; }
	.ceiling-box span { font-size: 0.78rem; color: var(--deck-muted); line-height: 1.6; }

	/* ── Keyframes ────────────────────────────────────────── */
	@keyframes coverSpin { to { rotate: 360deg; } }
	@keyframes chipFloat {
		0%, 100% { transform: translateY(0); }
		50% { transform: translateY(-8px); }
	}

	@media (prefers-reduced-motion: reduce) {
		.scatter-orbit, .model-chip { animation: none; }
	}
</style>
