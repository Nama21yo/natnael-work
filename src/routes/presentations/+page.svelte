<script lang="ts">
	import { base } from "$app/paths";
	import Icon from "@iconify/svelte";
	import ScrollReveal from "$lib/components/ScrollReveal.svelte";

	const miniBlocks = Array.from({ length: 12 }, (_, index) => index);
</script>

<svelte:head>
	<title>Presentations | Natnael Yohanes</title>
	<meta name="description" content="Interactive technical presentations by Natnael Yohanes." />
</svelte:head>

<section class="mx-auto max-w-7xl px-5 py-16 lg:px-8 lg:py-24">
	<ScrollReveal direction="down">
		<div class="max-w-4xl">
			<p class="section-kicker">Technical presentations</p>
			<h1 class="font-display text-5xl leading-tight font-black tracking-tight md:text-7xl">
				Complex systems,<br />
				<span class="text-cyan">made visible.</span>
			</h1>
			<p class="mt-6 max-w-2xl text-xl leading-9 text-muted-foreground">
				Interactive decks that turn protocol research, architecture, and engineering results into
				clear visual stories.
			</p>
		</div>
	</ScrollReveal>
</section>

<section class="mx-auto max-w-7xl px-5 pb-24 lg:px-8">
	<div class="grid gap-7">
		<ScrollReveal delay={120} direction="up">
			<a
				href={`${base}/presentations/cordial-miners`}
				class="presentation-card group relative block overflow-hidden rounded-[2.4rem] border border-foreground/10 bg-foreground text-background shadow-[0_35px_120px_rgba(15,23,42,0.18)]"
			>
				<div class="presentation-card-grid" aria-hidden="true"></div>
				<div class="relative z-10 grid gap-10 p-7 md:p-10 lg:grid-cols-[1fr_0.9fr] lg:items-center">
					<div>
						<div class="flex flex-wrap items-center gap-3">
							<span
								class="rounded-full bg-cyan px-3 py-1 text-xs font-black tracking-[0.16em] text-zinc-950 uppercase"
							>
								Quarter One
							</span>
							<span class="text-xs font-bold tracking-[0.16em] text-background/55 uppercase">
								3 slides · Interactive
							</span>
						</div>
						<h2 class="mt-6 max-w-2xl font-display text-4xl leading-[0.98] font-black md:text-6xl">
							Cordial Miners Quarter Report
						</h2>
						<p class="mt-5 max-w-xl text-base leading-7 text-background/65 md:text-lg">
							From the blocklace protocol to a selectable consensus path inside the f1r3node stack.
						</p>
						<span
							class="mt-8 inline-flex items-center gap-2 rounded-full bg-background px-5 py-3 text-sm font-black tracking-[0.12em] text-foreground uppercase transition group-hover:-translate-y-1"
						>
							Open presentation
							<Icon icon="iconoir:arrow-right" width="18" />
						</span>
					</div>

					<div class="mini-blocklace" aria-hidden="true">
						<div class="mini-wave mini-wave-one"></div>
						<div class="mini-wave mini-wave-two"></div>
						<div class="mini-wave mini-wave-three"></div>
						{#each miniBlocks as index (index)}
							<span
								class="mini-block"
								style={`--mini-index:${index}; --mini-column:${index % 4}; --mini-row:${Math.floor(
									index / 4
								)}`}
							></span>
						{/each}
						<div class="mini-tau">τ</div>
					</div>
				</div>
			</a>
		</ScrollReveal>

		<ScrollReveal delay={220} direction="up">
			<a
				href={`${base}/presentations/cordial-miners-interactive`}
				class="presentation-card primer-card group relative block overflow-hidden rounded-[2.4rem] border border-foreground/10 bg-stone-50 text-zinc-950 shadow-[0_35px_120px_rgba(15,23,42,0.12)] dark:bg-zinc-100"
			>
				<div class="primer-grid" aria-hidden="true"></div>
				<div class="relative z-10 grid gap-10 p-7 md:p-10 lg:grid-cols-[1fr_0.9fr] lg:items-center">
					<div>
						<div class="flex flex-wrap items-center gap-3">
							<span
								class="rounded-full bg-emerald-400 px-3 py-1 text-xs font-black tracking-[0.16em] text-zinc-950 uppercase"
							>
								Non-technical primer
							</span>
							<span class="text-xs font-bold tracking-[0.16em] text-zinc-500 uppercase">
								20 slides · Interactive
							</span>
						</div>
						<h2 class="mt-6 max-w-2xl font-display text-4xl leading-[0.98] font-black md:text-6xl">
							Cordial Miners Interactive Primer
						</h2>
						<p class="mt-5 max-w-xl text-base leading-7 text-zinc-600 md:text-lg">
							A visual story for curious non-engineers: blockchains, Byzantine agreement, blocklace,
							finality, stake, and pruning.
						</p>
						<span
							class="mt-8 inline-flex items-center gap-2 rounded-full bg-zinc-950 px-5 py-3 text-sm font-black tracking-[0.12em] text-white uppercase transition group-hover:-translate-y-1"
						>
							Open primer
							<Icon icon="iconoir:arrow-right" width="18" />
						</span>
					</div>

					<div class="mini-consensus" aria-hidden="true">
						<div class="mini-leader">2/3</div>
						{#each miniBlocks as index (index)}
							<span
								class={`primer-dot ${index > 8 ? "primer-red" : index > 5 ? "primer-amber" : "primer-green"}`}
								style={`--mini-index:${index}; --mini-angle:${index * 30}deg`}
							></span>
						{/each}
						<div class="mini-ledger">
							<span></span>
							<span></span>
							<span></span>
						</div>
					</div>
				</div>
			</a>
		</ScrollReveal>

		<ScrollReveal delay={320} direction="up">
			<a
				href={`${base}/presentations/multilingual-llm-benchmarking`}
				class="presentation-card llm-card group relative block overflow-hidden rounded-[2.4rem] border border-foreground/10 shadow-[0_35px_120px_rgba(15,23,42,0.22)]"
			>
				<div class="llm-grid" aria-hidden="true"></div>
				<div class="relative z-10 grid gap-10 p-7 md:p-10 lg:grid-cols-[1fr_0.9fr] lg:items-center">
					<div>
						<div class="flex flex-wrap items-center gap-3">
							<span
								class="rounded-full bg-violet-500 px-3 py-1 text-xs font-black tracking-[0.16em] text-white uppercase"
							>
								AI Benchmarking
							</span>
							<span class="text-xs font-bold tracking-[0.16em] text-white/50 uppercase">
								12 slides · Interactive
							</span>
						</div>
						<h2 class="mt-6 max-w-2xl font-display text-4xl leading-[0.98] font-black text-white md:text-6xl">
							Multilingual LLM Benchmarking
						</h2>
						<p class="mt-5 max-w-xl text-base leading-7 text-white/60 md:text-lg">
							Mapping Amharic Wikipedia infobox properties to the English DBpedia ontology — five
							models, four experiments, retrieve-then-rerank pipeline.
						</p>
						<span
							class="mt-8 inline-flex items-center gap-2 rounded-full bg-violet-500 px-5 py-3 text-sm font-black tracking-[0.12em] text-white uppercase transition group-hover:-translate-y-1"
						>
							Open presentation
							<Icon icon="iconoir:arrow-right" width="18" />
						</span>
					</div>

					<div class="mini-benchmark" aria-hidden="true">
						{#each [
							{ label: "Gemma 2 · CoT", val: 67.62, w: 100 },
							{ label: "Ens. Vote", val: 66.67, w: 98 },
							{ label: "Augmented", val: 66.90, w: 99 },
							{ label: "Llama 3.1", val: 61.41, w: 91 },
							{ label: "Aya Expanse", val: 48.99, w: 72 }
						] as row, i (i)}
							<div class="bench-row" style={`--bench-delay:${i * 80}ms`}>
								<span>{row.label}</span>
								<div class="bench-track">
									<div class="bench-bar" style={`width:${row.w}%`}></div>
								</div>
								<strong>{row.val}%</strong>
							</div>
						{/each}
					</div>
				</div>
			</a>
		</ScrollReveal>
	</div>
</section>

<style>
	.presentation-card-grid {
		position: absolute;
		inset: 0;
		opacity: 0.18;
		background-image:
			linear-gradient(rgba(255, 255, 255, 0.16) 1px, transparent 1px),
			linear-gradient(90deg, rgba(255, 255, 255, 0.16) 1px, transparent 1px);
		background-size: 48px 48px;
		mask-image: linear-gradient(to right, transparent, black);
	}

	.primer-grid {
		position: absolute;
		inset: 0;
		opacity: 0.48;
		background:
			radial-gradient(circle at 18% 20%, rgba(16, 185, 129, 0.18), transparent 28%),
			radial-gradient(circle at 78% 14%, rgba(79, 70, 229, 0.16), transparent 28%),
			linear-gradient(rgba(24, 24, 27, 0.07) 1px, transparent 1px),
			linear-gradient(90deg, rgba(24, 24, 27, 0.07) 1px, transparent 1px);
		background-size:
			auto,
			auto,
			44px 44px,
			44px 44px;
		mask-image: linear-gradient(to right, black, transparent);
	}

	.mini-consensus {
		position: relative;
		min-height: 21rem;
		border: 1px solid rgba(24, 24, 27, 0.1);
		border-radius: 2rem;
		background: rgba(255, 255, 255, 0.58);
		overflow: hidden;
	}

	.mini-consensus::before,
	.mini-consensus::after {
		content: "";
		position: absolute;
		inset: 16%;
		border: 1px dashed rgba(79, 70, 229, 0.25);
		border-radius: 50%;
		animation: orbit 18s linear infinite;
	}

	.mini-consensus::after {
		inset: 28%;
		border-color: rgba(16, 185, 129, 0.26);
		animation-direction: reverse;
		animation-duration: 11s;
	}

	.primer-dot {
		position: absolute;
		z-index: 2;
		top: calc(50% + sin(var(--mini-angle)) * 37% - 1.05rem);
		left: calc(50% + cos(var(--mini-angle)) * 37% - 1.05rem);
		width: 2.1rem;
		height: 2.1rem;
		border: 5px solid rgba(255, 255, 255, 0.7);
		border-radius: 50%;
		background: #4f46e5;
		box-shadow: 0 14px 34px rgba(15, 23, 42, 0.12);
		animation: miniConsensusPulse 2.8s ease-in-out infinite;
		animation-delay: calc(var(--mini-index) * -120ms);
	}

	.primer-green {
		background: #10b981;
	}

	.primer-amber {
		background: #f4b740;
	}

	.primer-red {
		background: #f43f5e;
	}

	.mini-leader {
		position: absolute;
		top: 50%;
		left: 50%;
		z-index: 4;
		display: grid;
		width: 6rem;
		height: 6rem;
		place-items: center;
		border-radius: 1.5rem;
		background: #18181b;
		color: white;
		font-family: var(--font-display);
		font-size: 1.55rem;
		font-weight: 900;
		translate: -50% -50%;
		box-shadow: 0 18px 55px rgba(15, 23, 42, 0.18);
	}

	.mini-ledger {
		position: absolute;
		right: 1rem;
		bottom: 1rem;
		z-index: 4;
		display: grid;
		gap: 0.35rem;
		width: 6.5rem;
	}

	.mini-ledger span {
		height: 0.8rem;
		border-radius: 999px;
		background: #18181b;
		opacity: 0.88;
	}

	.mini-blocklace {
		position: relative;
		min-height: 21rem;
		border: 1px solid rgba(255, 255, 255, 0.12);
		border-radius: 2rem;
		background: rgba(255, 255, 255, 0.055);
		overflow: hidden;
	}

	.mini-blocklace::before,
	.mini-blocklace::after {
		content: "";
		position: absolute;
		inset: 15% 12%;
		border: 1px dashed rgba(255, 255, 255, 0.2);
		border-radius: 999px;
		animation: orbit 18s linear infinite;
	}

	.mini-blocklace::after {
		inset: 28% 23%;
		animation-direction: reverse;
		animation-duration: 12s;
	}

	.mini-wave {
		position: absolute;
		top: 12%;
		bottom: 12%;
		width: 28%;
		border-left: 1px solid rgba(255, 255, 255, 0.12);
		border-right: 1px solid rgba(255, 255, 255, 0.12);
		background: linear-gradient(to bottom, transparent, rgba(102, 225, 235, 0.08), transparent);
	}

	.mini-wave-one {
		left: 8%;
	}

	.mini-wave-two {
		left: 36%;
	}

	.mini-wave-three {
		left: 64%;
	}

	.mini-block {
		--colors: #72e7ee;
		position: absolute;
		z-index: 2;
		top: calc(21% + var(--mini-row) * 29%);
		left: calc(13% + var(--mini-column) * 23%);
		width: 2.65rem;
		height: 2.65rem;
		border: 6px solid rgba(255, 255, 255, 0.08);
		border-radius: 50%;
		background: var(--colors);
		box-shadow: 0 0 28px color-mix(in srgb, var(--colors) 45%, transparent);
		animation: blockPulse 2.8s ease-in-out infinite;
		animation-delay: calc(var(--mini-index) * -180ms);
	}

	.mini-block:nth-of-type(4n + 2) {
		--colors: #f8ca69;
	}

	.mini-block:nth-of-type(4n + 3) {
		--colors: #9e8cff;
	}

	.mini-block:nth-of-type(4n + 4) {
		--colors: #78dfa9;
	}

	.mini-tau {
		position: absolute;
		right: 1rem;
		bottom: 1rem;
		z-index: 3;
		display: grid;
		width: 4rem;
		height: 4rem;
		place-items: center;
		border-radius: 1.25rem;
		background: white;
		color: #111827;
		font-family: var(--font-display);
		font-size: 2.4rem;
		font-weight: 900;
		rotate: 5deg;
		transition: transform 350ms ease;
	}

	.presentation-card:hover .mini-tau {
		transform: rotate(-10deg) scale(1.08);
	}

	@keyframes orbit {
		to {
			rotate: 360deg;
		}
	}

	@keyframes miniConsensusPulse {
		50% {
			transform: translateY(-6px);
			filter: brightness(1.08);
		}
	}

	@keyframes blockPulse {
		50% {
			scale: 1.14;
			filter: brightness(1.2);
		}
	}

	@media (prefers-reduced-motion: reduce) {
		.mini-blocklace::before,
		.mini-blocklace::after,
		.mini-block,
		.mini-consensus::before,
		.mini-consensus::after,
		.primer-dot {
			animation: none;
		}
	}

	/* ── LLM Benchmarking card ───────────────────────────── */
	.llm-card {
		background: #0d1017;
		color: white;
	}

	.llm-grid {
		position: absolute;
		inset: 0;
		opacity: 0.2;
		background:
			radial-gradient(circle at 78% 20%, rgba(165, 148, 255, 0.35), transparent 32%),
			radial-gradient(circle at 14% 78%, rgba(111, 229, 236, 0.2), transparent 28%),
			linear-gradient(rgba(255, 255, 255, 0.1) 1px, transparent 1px),
			linear-gradient(90deg, rgba(255, 255, 255, 0.1) 1px, transparent 1px);
		background-size: auto, auto, 46px 46px, 46px 46px;
		mask-image: linear-gradient(to left, black, transparent 55%);
	}

	.mini-benchmark {
		display: grid;
		gap: 0.65rem;
		padding: 1.4rem;
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 1.5rem;
		background: rgba(255, 255, 255, 0.04);
		min-height: 18rem;
		align-content: center;
	}

	.bench-row {
		display: grid;
		grid-template-columns: 7rem 1fr 4rem;
		align-items: center;
		gap: 0.6rem;
		animation: benchSlide 0.6s cubic-bezier(0.22, 1, 0.36, 1) both;
		animation-delay: var(--bench-delay);
	}

	.bench-row span {
		font-size: 0.72rem;
		font-weight: 700;
		color: rgba(255, 255, 255, 0.55);
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.bench-track {
		height: 1.5rem;
		border-radius: 0.4rem;
		background: rgba(255, 255, 255, 0.06);
		overflow: hidden;
	}

	.bench-bar {
		height: 100%;
		border-radius: 0.4rem;
		background: linear-gradient(90deg, rgba(165, 148, 255, 0.5), rgba(111, 229, 236, 0.7));
		transition: width 1s cubic-bezier(0.22, 1, 0.36, 1);
	}

	.bench-row strong {
		font-size: 0.78rem;
		font-weight: 800;
		color: rgba(255, 255, 255, 0.9);
		text-align: right;
	}

	@keyframes benchSlide {
		from {
			opacity: 0;
			transform: translateX(12px);
		}
		to {
			opacity: 1;
			transform: translateX(0);
		}
	}
</style>
