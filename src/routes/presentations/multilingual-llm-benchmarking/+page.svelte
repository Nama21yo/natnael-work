<script lang="ts">
	import { onMount } from "svelte";
	import { base } from "$app/paths";
	import Icon from "@iconify/svelte";
	import { profile } from "$lib/data/portfolio";

	const slideCount = 12;
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
			if (event.key === "Home") {
				event.preventDefault();
				goToSlide(0);
			}
			if (event.key === "End") {
				event.preventDefault();
				goToSlide(slideCount - 1);
			}
		};
		window.addEventListener("keydown", handleKeydown);
		return () => window.removeEventListener("keydown", handleKeydown);
	});

	const models = [
		{ alias: "Qwen 2.5 32B", color: "cyan", zero: 57.35, best: 60.21, bestShot: "2-shot" },
		{ alias: "Gemma 2 9B", color: "violet", zero: 57.71, best: 65.83, bestShot: "5-shot" },
		{ alias: "Llama 3.1 8B", color: "amber", zero: 42.65, best: 61.41, bestShot: "3-shot" },
		{ alias: "Llama 3.2 3B", color: "emerald", zero: 40.5, best: 52.69, bestShot: "1-shot" },
		{ alias: "Aya Expanse 8B", color: "rose", zero: 37.28, best: 48.99, bestShot: "1-shot" }
	];
</script>

<svelte:head>
	<title>Multilingual LLM Benchmarking | {profile.name}</title>
	<meta
		name="description"
		content="Benchmarking LLMs on Amharic DBpedia property mapping with a retrieve-then-rerank pipeline."
	/>
</svelte:head>

<section class="presentation-shell mx-auto max-w-[96rem] px-3 py-8 md:px-6 md:py-12">
	<div class="mb-4 flex flex-wrap items-center justify-between gap-3 px-2">
		<a
			href={`${base}/presentations`}
			class="inline-flex items-center gap-2 text-xs font-black tracking-[0.16em] text-muted-foreground uppercase transition hover:text-foreground"
		>
			<Icon icon="iconoir:arrow-left" width="16" />
			All presentations
		</a>
		<div class="flex items-center gap-2">
			<span class="hidden text-xs font-bold text-muted-foreground sm:inline">Use ← → or space</span>
			<button
				type="button"
				class="deck-icon-button"
				onclick={toggleFullscreen}
				aria-label="Toggle fullscreen"
			>
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
						retrieve-then-rerank pipeline — five models, four experiments, one Ge'ez script.
					</p>
				</div>

				<div class="cover-footer">
					<div>
						<span>Presented by</span>
						<strong>{profile.name}</strong>
						<small>{profile.role} · Addis Ababa, Ethiopia</small>
					</div>
					<div class="cover-stat-row">
						<div class="cover-stat">
							<strong>595</strong><small>DBpedia properties</small>
						</div>
						<div class="cover-stat">
							<strong>279</strong><small>test examples</small>
						</div>
						<div class="cover-stat">
							<strong>4</strong><small>experiments</small>
						</div>
					</div>
				</div>
			</section>

			<!-- SLIDE 02 — PROBLEM STATEMENT -->
			<section class="slide slide-problem" aria-hidden={activeSlide !== 1}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span>Slide 02 · The Challenge</p>
						<h2>Mapping <em>Ge'ez script</em> to a knowledge graph.</h2>
					</div>
					<p>
						DBpedia is a structured knowledge base extracted from Wikipedia. For Amharic Wikipedia to
						be part of it, every infobox property written in Ge'ez script must be linked to a
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
						<p class="dataset-name">dice-research / amharic-property-mapping</p>
						<div class="split-stats">
							<article>
								<strong>2,261</strong>
								<span>train examples</span>
							</article>
							<article>
								<strong>251</strong>
								<span>validation</span>
							</article>
							<article class="stat-highlight">
								<strong>279</strong>
								<span>test examples</span>
								<small>all results reported here</small>
							</article>
						</div>
						<div class="format-note">
							<Icon icon="iconoir:info-circle" width="15" />
							<span>Input format: <code>&lt;entity type&gt;'s &lt;property mention&gt;</code></span>
						</div>
					</div>
				</div>
			</section>

			<!-- SLIDE 03 — PIPELINE ARCHITECTURE -->
			<section class="slide slide-pipeline" aria-hidden={activeSlide !== 2}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span>Slide 03 · System Design</p>
						<h2>Retrieve, then <em>rerank.</em></h2>
					</div>
					<p>
						Searching all 595 labels per LLM call would be slow and exceed context limits. A dense
						retriever prunes the space to 10 candidates in milliseconds; the LLM then picks one.
					</p>
				</div>

				<div class="pipeline-layout">
					<div class="pipeline-flow" aria-label="Two-stage retrieve-then-rerank pipeline">
						<div class="pipeline-box pipeline-input">
							<span>Amharic premise</span>
							<small>e.g. ሀገር's ርዕሰ ከተማ</small>
						</div>
						<div class="pipeline-connector">
							<div class="connector-line"></div>
							<div class="connector-label">embed</div>
						</div>
						<div class="pipeline-stage stage-retriever">
							<div class="stage-number">01</div>
							<h3>Retriever</h3>
							<p>Afro-XLM-R encoder</p>
							<code>cosine similarity × 595 labels</code>
							<div class="stage-output">→ top-10 shortlist</div>
						</div>
						<div class="pipeline-connector">
							<div class="connector-line"></div>
							<div class="connector-label">10 candidates</div>
						</div>
						<div class="pipeline-stage stage-reranker">
							<div class="stage-number">02</div>
							<h3>Reranker</h3>
							<p>LLM via DSPy</p>
							<code>premise + 10 candidates → 1</code>
							<div class="stage-output">→ final answer</div>
						</div>
					</div>

					<div class="snapping-panel">
						<Icon icon="iconoir:magic-wand" width="22" class="snap-icon" />
						<div>
							<h3>Answer snapping</h3>
							<p>
								LLMs sometimes output slight variations of a candidate. A fuzzy-match step
								normalises the output and snaps it to the nearest candidate with
								<code>token_sort_ratio ≥ 85</code>. If nothing scores above the threshold, the
								top-1 retriever result is the safe fallback.
							</p>
						</div>
					</div>
				</div>
			</section>

			<!-- SLIDE 04 — THE MATH -->
			<section class="slide slide-math" aria-hidden={activeSlide !== 3}>
				<div class="slide-heading slide-heading-narrow">
					<div>
						<p class="deck-kicker"><span></span>Slide 04 · The Mathematics</p>
						<h2>Cosine similarity meets <em>fuzzy evaluation.</em></h2>
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
						`llm_raranker.py` — each model run at 0-shot through 5-shot with multiple seeds.
						Accuracy on 279 test examples; few-shot columns show mean ± std.
					</p>
				</div>

				<div class="baseline-layout">
					<div class="bar-chart" aria-label="Best accuracy per model">
						{#each models as m (m.alias)}
							<div class="bar-row">
								<span class="bar-label">{m.alias}</span>
								<div class="bar-track">
									<div
										class="bar-fill bar-fill-{m.color}"
										style={`width:${m.best}%`}
										aria-label="{m.alias}: {m.best}%"
									></div>
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
									<th>2-shot</th>
									<th>3-shot</th>
									<th>4-shot</th>
									<th>5-shot</th>
								</tr>
							</thead>
							<tbody>
								<tr>
									<td>Qwen 2.5 32B</td>
									<td>57.35</td><td>59.02</td><td>60.21</td><td>59.85</td><td>58.66</td><td>58.18</td>
								</tr>
								<tr class="row-highlight">
									<td>Gemma 2 9B</td>
									<td>57.71</td><td>60.22</td><td>62.84</td><td>61.65</td><td>62.37</td><td class="cell-best">65.83</td>
								</tr>
								<tr>
									<td>Llama 3.1 8B</td>
									<td>42.65</td><td>50.90</td><td>58.66</td><td class="cell-best">61.41</td><td>54.96</td><td>56.87</td>
								</tr>
								<tr>
									<td>Llama 3.2 3B</td>
									<td>40.50</td><td class="cell-best">52.69</td><td>52.33</td><td>47.19</td><td>47.07</td><td>52.09</td>
								</tr>
								<tr>
									<td>Aya Expanse 8B</td>
									<td>37.28</td><td class="cell-best">48.99</td><td>43.25</td><td>48.99</td><td>46.60</td><td>36.80</td>
								</tr>
							</tbody>
						</table>
						<p class="table-note">Bold: highest accuracy per model. Aya 5-shot (36.80%) is worse than 0-shot.</p>
					</div>
				</div>
			</section>

			<!-- SLIDE 06 — PROMPT ENGINEERING STRATEGIES -->
			<section class="slide slide-strategies" aria-hidden={activeSlide !== 5}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span>Slide 06 · Experiment 2 · Prompt Engineering</p>
						<h2>Four strategies, one model to <em>rule them all.</em></h2>
					</div>
					<p>
						`LLM_ranker_prompt_eng.py` — same retriever shortlist, four different ways to ask the LLM
						to pick. <code>max_tokens</code> raised from 256 → 384 to prevent CoT truncation.
					</p>
				</div>

				<div class="strategy-grid">
					<article class="strategy-card strategy-direct">
						<div class="strategy-badge">01</div>
						<Icon icon="iconoir:multiple-pages" width="26" class="strategy-icon" />
						<h3>direct</h3>
						<p>
							Plain multiple-choice prompt. Select the best matching property from the list. No
							reasoning steps. Baseline carried over from Experiment 1.
						</p>
						<div class="strategy-tag">baseline</div>
					</article>

					<article class="strategy-card strategy-cot">
						<div class="strategy-badge">02</div>
						<Icon icon="iconoir:brain" width="26" class="strategy-icon" />
						<h3>chain-of-thought</h3>
						<p>
							Reason in at most two concise sentences before answering. Forces the model to verbalise
							its selection process — effective with demonstrations, harmful at 0-shot.
						</p>
						<div class="strategy-tag strategy-tag-best">best strategy</div>
					</article>

					<article class="strategy-card strategy-persona">
						<div class="strategy-badge">03</div>
						<Icon icon="iconoir:user-badge-check" width="26" class="strategy-icon" />
						<h3>persona</h3>
						<p>
							System prompt: "You are a meticulous Semantic Web ontology engineer." Role framing
							shifts the output distribution toward technical DBpedia vocabulary.
						</p>
						<div class="strategy-tag">role framing</div>
					</article>

					<article class="strategy-card strategy-transcot">
						<div class="strategy-badge">04</div>
						<Icon icon="iconoir:language" width="26" class="strategy-icon" />
						<h3>translation-CoT</h3>
						<p>
							Mentally translate the Amharic property mention to English first, then reason to a
							candidate. Translation lives inside the chain of thought — no external call.
						</p>
						<div class="strategy-tag">internal pivot</div>
					</article>
				</div>

				<div class="cot-principle">
					<Icon icon="iconoir:warning-triangle" width="18" />
					<span>
						<strong>CoT at 0-shot hurts every model.</strong> Without demonstrations, unconstrained reasoning drifts.
						Add at least 2 shots before enabling chain-of-thought.
					</span>
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
						`LLM_ranker_translation.py` — the same model translates its own input, then reranks.
						Translation runs <strong>once up front</strong> and is reused across all
						technique/shot/seed combinations.
					</p>
				</div>

				<div class="translation-layout">
					<article class="technique-card technique-pivot">
						<div class="technique-number">01</div>
						<h3>Pivot</h3>
						<p class="technique-desc">
							The Amharic premise is <strong>fully replaced</strong> by its English translation. The
							LLM never sees Ge'ez script.
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
						<article class="insight-card insight-good">
							<Icon icon="iconoir:arrow-up-circle" width="22" />
							<div>
								<h3>Helps weaker models</h3>
								<p>
									Llama 3.1 0-shot: <strong>42.65% → 53.41%</strong> (+10.76 pp).
									Ge'ez-challenged models gain a crutch from the English hint.
								</p>
							</div>
						</article>
						<article class="insight-card insight-warn">
							<Icon icon="iconoir:arrow-down-circle" width="22" />
							<div>
								<h3>Hurts strong models at few-shot</h3>
								<p>
									Qwen 2.5 32B drops from 60.21% → 54.12% at 3-shot. The extra field adds
									noise for models already capable of cross-lingual reasoning.
								</p>
							</div>
						</article>
						<article class="insight-card insight-good">
							<Icon icon="iconoir:star" width="22" />
							<div>
								<h3>Gemma 2 augmented peak</h3>
								<p>
									<strong>66.90% at 4-shot</strong> — second-highest single-model result in the
									entire project, trailing only CoT prompting.
								</p>
							</div>
						</article>
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
						`LLM_ranker_ensemble.py` — member predictions collected once per (shots, seed) and reused
						for both strategies. No recomputation on strategy switch.
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
						<p class="deck-kicker"><span></span>Slide 11 · Cross-Experiment Synthesis</p>
						<h2>Every approach raises <em>the floor.</em></h2>
					</div>
				</div>

				<div class="comparison-layout">
					<div class="best-table-wrap">
						<table class="result-table">
							<thead>
								<tr><th>Experiment</th><th>Best Configuration</th><th>Accuracy</th><th>Std</th></tr>
							</thead>
							<tbody>
								<tr>
									<td>1 · Baseline</td>
									<td>Gemma 2 · 5-shot</td>
									<td>65.83%</td>
									<td>±2.84</td>
								</tr>
								<tr>
									<td>3 · Translation Augmented</td>
									<td>Gemma 2 + Augmented · 4-shot</td>
									<td>66.90%</td>
									<td>±1.50</td>
								</tr>
								<tr>
									<td>4 · Ensemble Vote</td>
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
									<td>Vote · 0-shot — no demos</td>
									<td>63.08%</td>
									<td>—</td>
								</tr>
							</tbody>
						</table>
					</div>

					<div class="cost-panel">
						<p class="deck-kicker" style="color:var(--deck-amber)"><span></span>Accuracy vs inference cost</p>
						<div class="cost-rows">
							{#each [
								{ label: "Solo model, 0-shot", cost: "1× call", acc: "57–57.7%", tier: 1 },
								{ label: "Ensemble vote, 0-shot", cost: "5× calls", acc: "63.1%", tier: 2 },
								{ label: "Solo model, 5-shot", cost: "1× call", acc: "65.8%", tier: 3 },
								{ label: "Translation augmented", cost: "1× translate + 1× rerank", acc: "66.9%", tier: 3 },
								{ label: "CoT, 5-shot ✓ best", cost: "1× call (longer)", acc: "67.6%", tier: 4 },
								{ label: "Ensemble vote, 3-shot", cost: "5× calls", acc: "66.7%", tier: 3 }
							] as row (row.label)}
								<div class="cost-row cost-tier-{row.tier}">
									<span class="cost-label">{row.label}</span>
									<span class="cost-cost">{row.cost}</span>
									<span class="cost-acc">{row.acc}</span>
								</div>
							{/each}
						</div>
						<p class="cost-verdict">
							<Icon icon="iconoir:check-circle" width="15" />
							Best cost-efficiency: CoT + few-shot on one capable model.
						</p>
					</div>
				</div>
			</section>

			<!-- SLIDE 12 — KEY TAKEAWAYS -->
			<section class="slide slide-takeaways" aria-hidden={activeSlide !== 11}>
				<div class="slide-heading slide-heading-narrow">
					<div>
						<p class="deck-kicker"><span></span>Slide 12 · Key Takeaways</p>
						<h2>A ceiling of 67.62% — and <em>where to go next.</em></h2>
					</div>
				</div>

				<div class="takeaways-layout">
					<div class="takeaway-list">
						<article class="takeaway">
							<span class="tk-num">01</span>
							<div>
								<h3>Retriever quality is the ceiling</h3>
								<p>
									All experiments share a top-10 shortlist. If the correct answer isn't in
									the top-10, no LLM can recover it. Fine-tuning Afro-XLM-R on the training
									split is the highest-leverage next step.
								</p>
							</div>
						</article>
						<article class="takeaway">
							<span class="tk-num">02</span>
							<div>
								<h3>CoT needs demonstrations</h3>
								<p>
									Chain-of-Thought is the best strategy — but hurts at 0-shot. Without
									demonstrations to anchor it, reasoning drifts. Use ≥ 2 shots before enabling.
								</p>
							</div>
						</article>
						<article class="takeaway">
							<span class="tk-num">03</span>
							<div>
								<h3>Translation: helps weak models, hurts strong ones</h3>
								<p>
									Weaker models gain a crutch from an English hint. Stronger models
									(Qwen, Gemma 2) are already capable cross-lingual reasoners — the extra field
									adds noise.
								</p>
							</div>
						</article>
						<article class="takeaway">
							<span class="tk-num">04</span>
							<div>
								<h3>Ensemble vote is a reliable floor-raiser</h3>
								<p>
									At 0-shot, majority vote outperforms every solo model by ~5 pp and cuts
									variance substantially. Default to it when prompt engineering hasn't been tuned.
								</p>
							</div>
						</article>
						<article class="takeaway">
							<span class="tk-num">05</span>
							<div>
								<h3>Variance is a signal</h3>
								<p>
									High σ (Aya: ±8–9 pp) means the model pattern-matches demonstrations rather
									than learning the task format. Low σ (Gemma 2 CoT: ±0.89) means the strategy
									is robust and reproducible.
								</p>
							</div>
						</article>
						<article class="takeaway">
							<span class="tk-num">06</span>
							<div>
								<h3>Next: retriever fine-tuning + semantic few-shot selection</h3>
								<p>
									Random demo sampling has high variance. Choosing demonstrations by semantic
									similarity to the premise (nearest-neighbour in embedding space) should reduce
									σ and raise μ simultaneously.
								</p>
							</div>
						</article>
					</div>

					<div class="ceiling-panel">
						<div class="ceiling-number">67.62%</div>
						<p>Current project ceiling</p>
						<small>Gemma 2 · Chain-of-Thought · 5-shot</small>
						<div class="ceiling-stack">
							<div class="cs-row" style="width:57%"><span>Baseline 0-shot</span><strong>57%</strong></div>
							<div class="cs-row" style="width:63%"><span>Ensemble 0-shot</span><strong>63%</strong></div>
							<div class="cs-row" style="width:65.83%"><span>Baseline best</span><strong>65.8%</strong></div>
							<div class="cs-row cs-row-peak" style="width:67.62%"><span>CoT best ✓</span><strong>67.6%</strong></div>
						</div>
					</div>
				</div>
			</section>

		</div><!-- end deck-track -->

		<div class="deck-controls">
			<button
				type="button"
				onclick={() => goToSlide(activeSlide - 1)}
				disabled={activeSlide === 0}
				aria-label="Previous slide"
			>
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

			<button
				type="button"
				onclick={() => goToSlide(activeSlide + 1)}
				disabled={activeSlide === slideCount - 1}
				aria-label="Next slide"
			>
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

	.deck:fullscreen {
		width: 100vw;
		height: 100vh;
		min-height: 100vh;
		border: 0;
		border-radius: 0;
	}

	.deck-icon-button {
		display: grid;
		place-items: center;
		width: 2rem;
		height: 2rem;
		border-radius: 0.5rem;
		border: 1px solid color-mix(in oklab, currentColor 20%, transparent);
		color: var(--muted-foreground);
		transition: color 200ms, background 200ms;
	}
	.deck-icon-button:hover {
		color: var(--foreground);
		background: color-mix(in oklab, currentColor 8%, transparent);
	}

	.deck-progress {
		position: absolute;
		top: 0;
		right: 0;
		left: 0;
		z-index: 20;
		height: 3px;
		background: rgba(255, 255, 255, 0.06);
	}
	.deck-progress span {
		display: block;
		height: 100%;
		background: linear-gradient(90deg, var(--deck-violet), var(--deck-cyan));
		transition: width 500ms cubic-bezier(0.22, 1, 0.36, 1);
	}

	.deck-track {
		display: flex;
		min-height: inherit;
		transition: transform 650ms cubic-bezier(0.22, 1, 0.36, 1);
	}

	.slide {
		position: relative;
		flex: 0 0 100%;
		width: 100%;
		min-height: inherit;
		overflow: hidden;
		padding: clamp(2rem, 4.5vw, 5rem);
		padding-bottom: clamp(6rem, 8vw, 7.5rem);
	}

	.deck-kicker {
		display: flex;
		align-items: center;
		gap: 0.65rem;
		color: var(--deck-cyan);
		font-size: 0.7rem;
		font-weight: 900;
		letter-spacing: 0.2em;
		text-transform: uppercase;
	}
	.deck-kicker span {
		width: 1.8rem;
		height: 2px;
		background: currentColor;
	}

	/* ── Deck controls ─────────────────────────────────────── */
	.deck-controls {
		position: absolute;
		bottom: 0;
		left: 0;
		right: 0;
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 0.75rem;
		padding: 1.4rem 2rem;
		background: linear-gradient(to top, rgba(13, 16, 23, 0.98) 60%, transparent);
		z-index: 10;
	}

	.deck-controls button {
		display: grid;
		place-items: center;
		width: 2.2rem;
		height: 2.2rem;
		border-radius: 50%;
		border: 1px solid rgba(255, 255, 255, 0.15);
		color: rgba(244, 247, 251, 0.7);
		transition: all 200ms;
	}
	.deck-controls button:hover:not(:disabled) {
		border-color: var(--deck-violet);
		color: var(--deck-violet);
		background: rgba(165, 148, 255, 0.1);
	}
	.deck-controls button:disabled {
		opacity: 0.25;
		cursor: not-allowed;
	}

	.deck-dots {
		display: flex;
		gap: 0.4rem;
	}
	.deck-dots button {
		width: 0.45rem;
		height: 0.45rem;
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.2);
		border: 0;
		padding: 0;
		transition: background 250ms, transform 250ms;
	}
	.deck-dots button.active {
		background: var(--deck-violet);
		transform: scale(1.5);
	}

	.deck-counter {
		min-width: 4rem;
		text-align: center;
		font-size: 0.72rem;
		font-weight: 700;
		letter-spacing: 0.1em;
		color: var(--deck-muted);
	}

	/* ── Shared slide heading ─────────────────────────────── */
	.slide-heading {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 1.2rem 3rem;
		align-items: start;
		margin-bottom: 2.2rem;
	}
	.slide-heading-narrow {
		grid-template-columns: 1fr;
		max-width: 60rem;
		margin-bottom: 1.8rem;
	}
	.slide-heading h2 {
		font-family: var(--font-display);
		font-size: clamp(1.8rem, 3.5vw, 3rem);
		font-weight: 900;
		letter-spacing: -0.04em;
		line-height: 1.05;
		margin-top: 0.5rem;
	}
	.slide-heading h2 em {
		font-style: normal;
		color: var(--deck-cyan);
	}
	.slide-heading > p {
		color: var(--deck-muted);
		font-size: 0.93rem;
		line-height: 1.7;
		padding-top: 0.2rem;
	}

	/* ── Shared table styles ─────────────────────────────── */
	.result-table {
		width: 100%;
		border-collapse: collapse;
		font-size: 0.8rem;
	}
	.result-table th {
		text-align: left;
		padding: 0.55rem 0.85rem;
		color: var(--deck-muted);
		font-weight: 700;
		font-size: 0.7rem;
		letter-spacing: 0.08em;
		text-transform: uppercase;
		border-bottom: 1px solid rgba(255, 255, 255, 0.08);
		white-space: nowrap;
	}
	.result-table td {
		padding: 0.6rem 0.85rem;
		color: var(--deck-ink);
		border-bottom: 1px solid rgba(255, 255, 255, 0.05);
		white-space: nowrap;
	}
	.row-highlight td {
		background: rgba(165, 148, 255, 0.07);
	}
	.row-bonus td {
		background: rgba(111, 229, 236, 0.05);
		color: var(--deck-muted);
		font-style: italic;
	}
	.cell-best {
		color: var(--deck-cyan) !important;
		font-weight: 800;
	}
	.cell-delta {
		color: var(--deck-emerald) !important;
		font-weight: 700;
	}
	.result-table-sm { font-size: 0.77rem; }
	.table-note {
		margin-top: 0.6rem;
		font-size: 0.7rem;
		color: var(--deck-muted);
	}

	/* ── SLIDE 01 — COVER ─────────────────────────────────── */
	.slide-cover {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		background:
			radial-gradient(circle at 80% 15%, rgba(165, 148, 255, 0.22), transparent 34%),
			radial-gradient(circle at 10% 85%, rgba(111, 229, 236, 0.17), transparent 32%),
			#0d1017;
	}

	.cover-grid {
		position: absolute;
		inset: 0;
		opacity: 0.15;
		background-image:
			linear-gradient(rgba(255, 255, 255, 0.14) 1px, transparent 1px),
			linear-gradient(90deg, rgba(255, 255, 255, 0.14) 1px, transparent 1px);
		background-size: 52px 52px;
		mask-image: radial-gradient(circle at 65% 35%, black, transparent 70%);
	}

	.cover-scatter {
		position: absolute;
		inset: 0;
		pointer-events: none;
	}
	.scatter-orbit {
		position: absolute;
		border: 1px dashed rgba(255, 255, 255, 0.12);
		border-radius: 50%;
		animation: coverSpin 20s linear infinite;
	}
	.scatter-orbit-one { inset: 12% 6%; }
	.scatter-orbit-two { inset: 22% 14%; animation-direction: reverse; animation-duration: 14s; }

	.model-chip {
		position: absolute;
		padding: 0.28rem 0.7rem;
		border-radius: 999px;
		border: 1px solid rgba(255, 255, 255, 0.14);
		background: rgba(255, 255, 255, 0.05);
		font-size: 0.65rem;
		font-weight: 800;
		letter-spacing: 0.1em;
		color: var(--deck-muted);
		animation: chipFloat 5s ease-in-out infinite;
		animation-delay: calc(var(--chip-index) * -0.9s);
	}
	.model-chip:nth-child(1) { top: 18%; right: 22%; }
	.model-chip:nth-child(2) { top: 32%; right: 10%; }
	.model-chip:nth-child(3) { top: 55%; right: 18%; }
	.model-chip:nth-child(4) { top: 68%; right: 8%; }
	.model-chip:nth-child(5) { top: 78%; right: 24%; }

	.cover-copy {
		position: relative;
		z-index: 3;
	}
	.cover-copy h1 {
		max-width: 56rem;
		margin-top: 1.6rem;
		font-family: var(--font-display);
		font-size: clamp(3.5rem, 8vw, 8.5rem);
		font-weight: 900;
		letter-spacing: -0.07em;
		line-height: 0.82;
	}
	.cover-copy h1 strong {
		display: block;
		color: transparent;
		-webkit-text-stroke: 1.5px rgba(255, 255, 255, 0.6);
	}
	.cover-copy p {
		max-width: 48rem;
		margin-top: 2rem;
		color: var(--deck-muted);
		font-size: clamp(0.95rem, 1.4vw, 1.2rem);
		line-height: 1.75;
	}

	.cover-footer {
		position: relative;
		z-index: 3;
		display: flex;
		align-items: flex-end;
		justify-content: space-between;
		gap: 2rem;
		flex-wrap: wrap;
	}
	.cover-footer > div:first-child {
		display: grid;
		gap: 0.2rem;
	}
	.cover-footer span, .cover-footer small {
		color: var(--deck-muted);
		font-size: 0.68rem;
		font-weight: 800;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}
	.cover-footer strong { font-size: 1.15rem; }

	.cover-stat-row {
		display: flex;
		gap: 1.2rem;
	}
	.cover-stat {
		display: grid;
		place-items: center;
		padding: 0.8rem 1.2rem;
		border: 1px solid var(--deck-line);
		border-radius: 1.1rem;
		background: rgba(255, 255, 255, 0.04);
		gap: 0.2rem;
	}
	.cover-stat strong {
		font-family: var(--font-display);
		font-size: 1.8rem !important;
		font-weight: 900;
		color: var(--deck-violet) !important;
		letter-spacing: -0.04em;
	}
	.cover-stat small {
		font-size: 0.6rem !important;
		color: var(--deck-muted) !important;
	}

	/* ── SLIDE 02 — PROBLEM ───────────────────────────────── */
	.problem-layout {
		display: grid;
		grid-template-columns: 1.1fr 0.9fr;
		gap: 2rem;
		align-items: start;
	}

	.example-table {
		border: 1px solid var(--deck-line);
		border-radius: 1.2rem;
		overflow: hidden;
	}
	.example-head {
		display: flex;
		align-items: center;
		gap: 0.8rem;
		padding: 0.75rem 1.2rem;
		background: rgba(165, 148, 255, 0.08);
		border-bottom: 1px solid var(--deck-line);
		font-size: 0.7rem;
		font-weight: 800;
		letter-spacing: 0.1em;
		text-transform: uppercase;
		color: var(--deck-violet);
	}
	.example-head :global(svg) { opacity: 0.5; }
	.example-row {
		display: grid;
		grid-template-columns: 1fr auto 1fr;
		align-items: center;
		gap: 0.8rem;
		padding: 0.75rem 1.2rem;
		border-bottom: 1px solid rgba(255, 255, 255, 0.04);
		transition: background 200ms;
	}
	.example-row:last-child { border-bottom: 0; }
	.example-row:hover { background: rgba(255, 255, 255, 0.03); }
	.row-arrow { opacity: 0.4; justify-self: center; }
	code.amharic {
		font-family: var(--font-sans);
		font-size: 0.88rem;
		color: var(--deck-cyan);
	}
	code.property {
		font-size: 0.8rem;
		color: var(--deck-emerald);
		background: rgba(117, 223, 168, 0.08);
		padding: 0.15rem 0.45rem;
		border-radius: 0.4rem;
	}

	.dataset-panel {
		display: grid;
		gap: 1.1rem;
	}
	.dataset-name {
		font-family: var(--font-sans);
		font-size: 0.8rem;
		color: var(--deck-muted);
		padding: 0.6rem 0.9rem;
		border: 1px solid var(--deck-line);
		border-radius: 0.7rem;
		background: rgba(255, 255, 255, 0.03);
	}
	.split-stats {
		display: grid;
		grid-template-columns: 1fr 1fr 1fr;
		gap: 0.7rem;
	}
	.split-stats article {
		display: grid;
		place-items: center;
		gap: 0.2rem;
		padding: 1rem 0.6rem;
		border: 1px solid var(--deck-line);
		border-radius: 1rem;
		text-align: center;
	}
	.split-stats strong {
		font-family: var(--font-display);
		font-size: 2rem;
		font-weight: 900;
		color: var(--deck-ink);
		letter-spacing: -0.04em;
	}
	.split-stats span {
		font-size: 0.65rem;
		font-weight: 700;
		color: var(--deck-muted);
		letter-spacing: 0.08em;
		text-transform: uppercase;
	}
	.split-stats small {
		font-size: 0.58rem;
		color: var(--deck-cyan);
	}
	.stat-highlight {
		border-color: rgba(111, 229, 236, 0.3);
		background: rgba(111, 229, 236, 0.05);
	}
	.stat-highlight strong { color: var(--deck-cyan); }
	.format-note {
		display: flex;
		align-items: center;
		gap: 0.6rem;
		font-size: 0.75rem;
		color: var(--deck-muted);
		padding: 0.6rem 0.9rem;
		border: 1px solid var(--deck-line);
		border-radius: 0.7rem;
		background: rgba(255, 255, 255, 0.02);
	}
	.format-note code { color: var(--deck-amber); font-size: 0.78rem; }

	/* ── SLIDE 03 — PIPELINE ──────────────────────────────── */
	.pipeline-layout {
		display: grid;
		gap: 2rem;
	}

	.pipeline-flow {
		display: flex;
		align-items: center;
		gap: 0;
		padding: 2rem;
		border: 1px solid var(--deck-line);
		border-radius: 1.5rem;
		background: rgba(255, 255, 255, 0.025);
	}
	.pipeline-box {
		display: grid;
		place-items: center;
		padding: 1.1rem 1.5rem;
		border: 1px solid var(--deck-line);
		border-radius: 1rem;
		text-align: center;
		gap: 0.3rem;
	}
	.pipeline-input span { font-size: 0.82rem; font-weight: 700; }
	.pipeline-input small { font-family: var(--font-sans); font-size: 0.75rem; color: var(--deck-cyan); }

	.pipeline-connector {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.3rem;
		min-width: 4rem;
	}
	.connector-line {
		width: 100%;
		height: 2px;
		background: linear-gradient(90deg, var(--deck-violet), var(--deck-cyan));
		opacity: 0.5;
	}
	.connector-label {
		font-size: 0.6rem;
		font-weight: 800;
		letter-spacing: 0.1em;
		text-transform: uppercase;
		color: var(--deck-muted);
	}

	.pipeline-stage {
		flex: 1;
		display: grid;
		gap: 0.4rem;
		padding: 1.2rem 1.4rem;
		border-radius: 1.2rem;
	}
	.stage-retriever {
		border: 1px solid rgba(165, 148, 255, 0.3);
		background: rgba(165, 148, 255, 0.06);
	}
	.stage-reranker {
		border: 1px solid rgba(111, 229, 236, 0.3);
		background: rgba(111, 229, 236, 0.06);
	}
	.stage-number {
		font-size: 0.65rem;
		font-weight: 900;
		color: var(--deck-violet);
		letter-spacing: 0.15em;
	}
	.stage-retriever .stage-number { color: var(--deck-violet); }
	.stage-reranker .stage-number { color: var(--deck-cyan); }
	.pipeline-stage h3 { font-size: 1rem; font-weight: 800; }
	.pipeline-stage p { font-size: 0.75rem; color: var(--deck-muted); }
	.pipeline-stage code { font-size: 0.72rem; background: rgba(255,255,255,0.06); padding: 0.2rem 0.5rem; border-radius: 0.3rem; }
	.stage-output { font-size: 0.75rem; font-weight: 700; color: var(--deck-emerald); }

	.snapping-panel {
		display: flex;
		gap: 1rem;
		align-items: flex-start;
		padding: 1.1rem 1.4rem;
		border: 1px solid rgba(246, 201, 107, 0.25);
		border-radius: 1rem;
		background: rgba(246, 201, 107, 0.05);
	}
	.snapping-panel :global(.snap-icon) { color: var(--deck-amber); flex-shrink: 0; margin-top: 0.1rem; }
	.snapping-panel h3 { font-size: 0.88rem; font-weight: 800; margin-bottom: 0.3rem; }
	.snapping-panel p { font-size: 0.8rem; color: var(--deck-muted); line-height: 1.65; }
	.snapping-panel code { color: var(--deck-amber); }

	/* ── SLIDE 04 — MATH ──────────────────────────────────── */
	.math-grid {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 1rem;
	}

	.math-card {
		padding: 1.2rem 1.4rem;
		border-radius: 1.2rem;
		border: 1px solid var(--deck-line);
		display: grid;
		gap: 0.7rem;
	}
	.math-card-retrieval { border-color: rgba(165, 148, 255, 0.3); background: rgba(165, 148, 255, 0.05); }
	.math-card-metric    { border-color: rgba(111, 229, 236, 0.3); background: rgba(111, 229, 236, 0.05); }
	.math-card-seeds     { border-color: rgba(246, 201, 107, 0.3); background: rgba(246, 201, 107, 0.05); }
	.math-card-vote      { border-color: rgba(117, 223, 168, 0.3); background: rgba(117, 223, 168, 0.05); }

	.math-label {
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.18em;
		text-transform: uppercase;
		color: var(--deck-muted);
	}
	.math-card h3 {
		font-size: 0.9rem;
		font-weight: 800;
		line-height: 1.3;
	}
	.formula {
		display: grid;
		gap: 0.5rem;
		padding: 0.75rem;
		border-radius: 0.7rem;
		background: rgba(0,0,0,0.25);
		font-family: "Georgia", serif;
	}
	.formula-line {
		font-size: 0.85rem;
		color: var(--deck-ink);
		line-height: 1.8;
	}
	.main-formula { font-size: 1rem; }
	.frac {
		display: inline-grid;
		text-align: center;
		vertical-align: middle;
		gap: 1px;
	}
	.frac span:first-child { border-bottom: 1px solid currentColor; padding: 0 0.2rem; }
	.frac span:last-child  { padding: 0 0.2rem; }
	.sqrt-arg { border-top: 1px solid currentColor; padding: 0 0.2rem; }

	.code-snippet {
		font-size: 0.72rem;
		color: var(--deck-emerald);
		background: rgba(0,0,0,0.3);
		padding: 0.5rem 0.75rem;
		border-radius: 0.5rem;
		white-space: pre;
		display: block;
		line-height: 1.7;
	}
	.math-note {
		font-size: 0.75rem;
		color: var(--deck-muted);
		line-height: 1.6;
	}
	.math-note code { color: var(--deck-amber); }

	/* ── SLIDE 05 — BASELINE ──────────────────────────────── */
	.baseline-layout {
		display: grid;
		grid-template-columns: 0.9fr 1.1fr;
		gap: 2rem;
		align-items: start;
	}

	.bar-chart { display: grid; gap: 0.9rem; }
	.bar-row {
		display: grid;
		grid-template-columns: 7rem 1fr 3.5rem;
		align-items: center;
		gap: 0.75rem;
	}
	.bar-label { font-size: 0.75rem; font-weight: 700; color: var(--deck-muted); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
	.bar-track { position: relative; height: 1.7rem; border-radius: 0.5rem; background: rgba(255,255,255,0.05); overflow: hidden; }
	.bar-fill {
		height: 100%;
		border-radius: 0.5rem;
		transition: width 800ms cubic-bezier(0.22, 1, 0.36, 1);
	}
	.bar-fill-cyan    { background: linear-gradient(90deg, rgba(111,229,236,0.3), rgba(111,229,236,0.7)); }
	.bar-fill-violet  { background: linear-gradient(90deg, rgba(165,148,255,0.3), rgba(165,148,255,0.8)); }
	.bar-fill-amber   { background: linear-gradient(90deg, rgba(246,201,107,0.3), rgba(246,201,107,0.7)); }
	.bar-fill-emerald { background: linear-gradient(90deg, rgba(117,223,168,0.3), rgba(117,223,168,0.6)); }
	.bar-fill-rose    { background: linear-gradient(90deg, rgba(251,113,133,0.3), rgba(251,113,133,0.6)); }
	.bar-value {
		position: absolute;
		right: 0.5rem;
		top: 50%;
		translate: 0 -50%;
		font-size: 0.7rem;
		font-weight: 800;
		color: var(--deck-ink);
	}
	.bar-meta { font-size: 0.65rem; font-weight: 700; color: var(--deck-muted); text-align: right; }

	.baseline-table-wrap { overflow-x: auto; }

	/* ── SLIDE 06 — STRATEGIES ────────────────────────────── */
	.strategy-grid {
		display: grid;
		grid-template-columns: 1fr 1fr 1fr 1fr;
		gap: 1rem;
		margin-bottom: 1.2rem;
	}
	.strategy-card {
		display: grid;
		gap: 0.65rem;
		padding: 1.2rem;
		border-radius: 1.2rem;
		border: 1px solid var(--deck-line);
		background: rgba(255,255,255,0.025);
		position: relative;
	}
	.strategy-card h3 { font-size: 0.88rem; font-weight: 800; }
	.strategy-card p { font-size: 0.77rem; color: var(--deck-muted); line-height: 1.6; }
	.strategy-badge {
		font-size: 0.6rem;
		font-weight: 900;
		letter-spacing: 0.15em;
		color: var(--deck-muted);
	}
	.strategy-card :global(.strategy-icon) { color: var(--deck-cyan); }
	.strategy-cot { border-color: rgba(165, 148, 255, 0.35); background: rgba(165, 148, 255, 0.06); }
	.strategy-cot :global(.strategy-icon) { color: var(--deck-violet); }

	.strategy-tag {
		display: inline-block;
		padding: 0.2rem 0.55rem;
		border-radius: 999px;
		font-size: 0.6rem;
		font-weight: 800;
		letter-spacing: 0.1em;
		text-transform: uppercase;
		border: 1px solid rgba(255,255,255,0.15);
		color: var(--deck-muted);
	}
	.strategy-tag-best {
		border-color: rgba(165, 148, 255, 0.5);
		background: rgba(165, 148, 255, 0.1);
		color: var(--deck-violet);
	}

	.cot-principle {
		display: flex;
		align-items: flex-start;
		gap: 0.7rem;
		padding: 0.9rem 1.2rem;
		border-radius: 0.9rem;
		border: 1px solid rgba(246, 201, 107, 0.25);
		background: rgba(246, 201, 107, 0.05);
		font-size: 0.8rem;
		color: var(--deck-muted);
		line-height: 1.6;
	}
	.cot-principle :global(svg) { color: var(--deck-amber); flex-shrink: 0; margin-top: 0.1rem; }
	.cot-principle strong { color: var(--deck-amber); }

	/* ── SLIDE 07 — PE RESULTS ────────────────────────────── */
	.pe-results-layout {
		display: grid;
		grid-template-columns: 1.1fr 0.9fr;
		gap: 2rem;
		align-items: start;
	}
	.pe-best-table-wrap { overflow-x: auto; }

	.pe-detail { display: grid; gap: 1.2rem; }
	.gemma-cot-grid {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 0.5rem;
	}
	.cot-cell {
		display: grid;
		place-items: center;
		gap: 0.2rem;
		padding: 0.8rem 0.4rem;
		border-radius: 0.8rem;
		border: 1px solid var(--deck-line);
		background: rgba(255,255,255,0.025);
	}
	.cot-shots { font-size: 0.6rem; font-weight: 800; color: var(--deck-muted); letter-spacing: 0.1em; text-transform: uppercase; }
	.cot-cell strong { font-size: 0.95rem; font-weight: 800; }
	.cot-best { border-color: rgba(165, 148, 255, 0.5); background: rgba(165, 148, 255, 0.1); }
	.cot-best strong { color: var(--deck-violet); }
	.cot-hurts { opacity: 0.55; }

	.pe-callout {
		display: flex;
		align-items: center;
		gap: 1rem;
		padding: 1rem 1.4rem;
		border-radius: 1rem;
		border: 1px solid rgba(165, 148, 255, 0.4);
		background: rgba(165, 148, 255, 0.08);
	}
	.pe-callout strong {
		font-family: var(--font-display);
		font-size: 2rem;
		font-weight: 900;
		color: var(--deck-violet);
		letter-spacing: -0.04em;
	}
	.pe-callout span { font-size: 0.8rem; color: var(--deck-muted); line-height: 1.5; }

	/* ── SLIDE 08 — TRANSLATION ───────────────────────────── */
	.translation-layout {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 1.5rem;
		margin-bottom: 1.5rem;
	}
	.technique-card {
		display: grid;
		gap: 0.85rem;
		padding: 1.4rem;
		border-radius: 1.3rem;
		border: 1px solid var(--deck-line);
	}
	.technique-pivot { border-color: rgba(246, 201, 107, 0.25); background: rgba(246, 201, 107, 0.04); }
	.technique-augmented { border-color: rgba(117, 223, 168, 0.3); background: rgba(117, 223, 168, 0.05); }
	.technique-number { font-size: 0.62rem; font-weight: 900; letter-spacing: 0.18em; color: var(--deck-muted); }
	.technique-card h3 { font-size: 1.05rem; font-weight: 800; }
	.technique-desc { font-size: 0.8rem; color: var(--deck-muted); line-height: 1.65; }
	.technique-example {
		display: grid;
		gap: 0.4rem;
		padding: 0.75rem;
		border-radius: 0.7rem;
		background: rgba(0,0,0,0.25);
	}
	.ex-row { display: flex; gap: 0.6rem; align-items: center; font-size: 0.77rem; }
	.ex-label { font-weight: 800; color: var(--deck-muted); min-width: 5.5rem; font-size: 0.7rem; }
	.ex-row code { color: var(--deck-cyan); }
	code.amharic-sm { color: var(--deck-amber); font-family: var(--font-sans); }
	.technique-verdict { font-size: 0.77rem; color: var(--deck-muted); line-height: 1.6; padding: 0.6rem 0.8rem; border-radius: 0.6rem; }
	.verdict-neutral { background: rgba(255,255,255,0.04); border: 1px solid rgba(255,255,255,0.06); }
	.verdict-good { background: rgba(117, 223, 168, 0.07); border: 1px solid rgba(117, 223, 168, 0.2); color: var(--deck-emerald); }

	.translation-flow {
		display: flex;
		align-items: center;
		gap: 0.6rem;
		flex-wrap: wrap;
		padding: 0.9rem 1.2rem;
		border-radius: 0.9rem;
		border: 1px solid var(--deck-line);
		background: rgba(255,255,255,0.025);
		font-size: 0.77rem;
	}
	.translation-flow div {
		padding: 0.3rem 0.7rem;
		border-radius: 0.5rem;
		border: 1px solid rgba(255,255,255,0.1);
		background: rgba(255,255,255,0.04);
	}
	.translation-flow :global(svg) { opacity: 0.4; }

	/* ── SLIDE 09 — TRANSLATION RESULTS ──────────────────── */
	.tr-results-layout {
		display: grid;
		grid-template-columns: 1.1fr 0.9fr;
		gap: 2rem;
		align-items: start;
	}
	.tr-tables { display: grid; gap: 1.2rem; }
	.tr-table-label {
		font-size: 0.7rem;
		font-weight: 900;
		letter-spacing: 0.12em;
		text-transform: uppercase;
		color: var(--deck-muted);
		margin-bottom: 0.5rem;
	}
	.tr-table-label-good { color: var(--deck-emerald); }

	.tr-insights { display: grid; gap: 0.8rem; }
	.insight-card {
		display: flex;
		gap: 0.8rem;
		align-items: flex-start;
		padding: 0.9rem 1rem;
		border-radius: 0.9rem;
		border: 1px solid var(--deck-line);
	}
	.insight-good { border-color: rgba(117, 223, 168, 0.25); background: rgba(117, 223, 168, 0.05); }
	.insight-warn { border-color: rgba(246, 201, 107, 0.25); background: rgba(246, 201, 107, 0.04); }
	.insight-good :global(svg) { color: var(--deck-emerald); flex-shrink: 0; }
	.insight-warn :global(svg) { color: var(--deck-amber); flex-shrink: 0; }
	.insight-card h3 { font-size: 0.82rem; font-weight: 800; margin-bottom: 0.3rem; }
	.insight-card p { font-size: 0.77rem; color: var(--deck-muted); line-height: 1.6; }
	.insight-card strong { color: var(--deck-emerald); }

	/* ── SLIDE 10 — ENSEMBLE ──────────────────────────────── */
	.ensemble-layout {
		display: grid;
		grid-template-columns: 0.9fr 1.1fr;
		gap: 2rem;
		align-items: start;
	}

	.vote-diagram {
		display: grid;
		gap: 0.9rem;
		padding: 1.3rem;
		border: 1px solid var(--deck-line);
		border-radius: 1.3rem;
		background: rgba(255,255,255,0.025);
	}
	.vote-label { font-size: 0.7rem; font-weight: 700; color: var(--deck-muted); }
	.vote-members { display: grid; gap: 0.45rem; }
	.vote-member {
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 0.5rem;
		padding: 0.5rem 0.75rem;
		border-radius: 0.7rem;
		border: 1px solid transparent;
		font-size: 0.78rem;
	}
	.vote-correct { border-color: rgba(117, 223, 168, 0.25); background: rgba(117, 223, 168, 0.06); }
	.vote-wrong   { border-color: rgba(255,255,255,0.06); background: rgba(255,255,255,0.025); opacity: 0.65; }
	.vote-correct :global(svg) { color: var(--deck-emerald); }
	.vote-wrong   :global(svg) { color: rgba(255,255,255,0.3); }
	.vote-model { font-weight: 700; min-width: 6rem; }
	.vote-pred { font-family: var(--font-sans); font-size: 0.73rem; color: var(--deck-cyan); flex: 1; text-align: center; }

	.vote-result {
		display: flex;
		align-items: center;
		gap: 0.7rem;
		padding: 0.65rem 0.9rem;
		border-radius: 0.7rem;
		background: rgba(117, 223, 168, 0.08);
		border: 1px solid rgba(117, 223, 168, 0.25);
		font-size: 0.82rem;
	}
	.vote-result strong { color: var(--deck-emerald); }
	.vote-result span { color: var(--deck-muted); font-size: 0.72rem; }

	.ensemble-results-panel { display: grid; gap: 1.1rem; }
	.ensemble-callouts { display: grid; gap: 0.7rem; }
	.e-callout {
		display: flex;
		gap: 1rem;
		align-items: center;
		padding: 0.8rem 1rem;
		border-radius: 0.9rem;
		border: 1px solid var(--deck-line);
		font-size: 0.78rem;
	}
	.e-callout-good { border-color: rgba(117, 223, 168, 0.25); background: rgba(117, 223, 168, 0.05); }
	.e-callout-warn { border-color: rgba(246, 201, 107, 0.2); background: rgba(246, 201, 107, 0.04); }
	.e-callout strong { font-size: 0.88rem; font-weight: 800; white-space: nowrap; color: var(--deck-emerald); }
	.e-callout-warn strong { color: var(--deck-amber); }
	.e-callout span { color: var(--deck-muted); line-height: 1.5; }

	/* ── SLIDE 11 — COMPARISON ────────────────────────────── */
	.comparison-layout {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 2rem;
		align-items: start;
	}
	.best-table-wrap { overflow-x: auto; }

	.cost-panel { display: grid; gap: 1.1rem; }
	.cost-rows { display: grid; gap: 0.4rem; }
	.cost-row {
		display: grid;
		grid-template-columns: 1fr auto auto;
		gap: 0.8rem;
		align-items: center;
		padding: 0.55rem 0.8rem;
		border-radius: 0.6rem;
		border: 1px solid rgba(255,255,255,0.04);
		background: rgba(255,255,255,0.02);
		font-size: 0.76rem;
	}
	.cost-tier-4 { border-color: rgba(165, 148, 255, 0.3); background: rgba(165, 148, 255, 0.06); }
	.cost-label { font-weight: 600; }
	.cost-tier-4 .cost-label { color: var(--deck-violet); font-weight: 800; }
	.cost-cost { color: var(--deck-muted); font-size: 0.68rem; white-space: nowrap; }
	.cost-acc { font-weight: 800; font-size: 0.8rem; white-space: nowrap; }
	.cost-tier-4 .cost-acc { color: var(--deck-violet); }
	.cost-verdict {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		font-size: 0.78rem;
		color: var(--deck-emerald);
		font-weight: 700;
	}

	/* ── SLIDE 12 — TAKEAWAYS ─────────────────────────────── */
	.takeaways-layout {
		display: grid;
		grid-template-columns: 1.2fr 0.8fr;
		gap: 2.5rem;
		align-items: start;
	}

	.takeaway-list { display: grid; gap: 0.75rem; }
	.takeaway {
		display: flex;
		gap: 1rem;
		align-items: flex-start;
	}
	.tk-num {
		flex-shrink: 0;
		font-family: var(--font-display);
		font-size: 1rem;
		font-weight: 900;
		color: var(--deck-violet);
		min-width: 2rem;
	}
	.takeaway h3 { font-size: 0.82rem; font-weight: 800; margin-bottom: 0.2rem; }
	.takeaway p { font-size: 0.76rem; color: var(--deck-muted); line-height: 1.6; }

	.ceiling-panel {
		display: grid;
		gap: 0.8rem;
		padding: 1.5rem;
		border: 1px solid rgba(165, 148, 255, 0.35);
		border-radius: 1.5rem;
		background: rgba(165, 148, 255, 0.06);
		text-align: center;
	}
	.ceiling-number {
		font-family: var(--font-display);
		font-size: clamp(2.5rem, 6vw, 4.5rem);
		font-weight: 900;
		color: var(--deck-violet);
		letter-spacing: -0.06em;
		line-height: 1;
	}
	.ceiling-panel p { font-size: 0.85rem; font-weight: 700; }
	.ceiling-panel small { font-size: 0.7rem; color: var(--deck-muted); }

	.ceiling-stack {
		display: grid;
		gap: 0.4rem;
		margin-top: 0.5rem;
	}
	.cs-row {
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 0.35rem 0.7rem;
		border-radius: 0.5rem;
		background: rgba(255,255,255,0.05);
		font-size: 0.68rem;
		min-width: 0;
	}
	.cs-row span { color: var(--deck-muted); }
	.cs-row strong { font-weight: 800; }
	.cs-row-peak {
		background: rgba(165, 148, 255, 0.12);
		border: 1px solid rgba(165, 148, 255, 0.35);
	}
	.cs-row-peak strong { color: var(--deck-violet); }

	/* ── Keyframes ────────────────────────────────────────── */
	@keyframes coverSpin { to { rotate: 360deg; } }
	@keyframes chipFloat {
		0%, 100% { transform: translateY(0); }
		50% { transform: translateY(-8px); }
	}

	@media (prefers-reduced-motion: reduce) {
		.scatter-orbit,
		.model-chip { animation: none; }
	}
</style>
