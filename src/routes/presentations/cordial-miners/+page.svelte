<script lang="ts">
	import { onMount } from "svelte";
	import { base } from "$app/paths";
	import Icon from "@iconify/svelte";
	import { profile } from "$lib/data/portfolio";

	const slideCount = 20;
	const slideNumbers = Array.from({ length: slideCount }, (_, index) => index);
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
</script>

<svelte:head>
	<title>Cordial Miners Quarter Report | {profile.name}</title>
	<meta
		name="description"
		content="An interactive presentation about Cordial Miners and its integration with f1r3node."
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
			<span class="hidden text-xs font-bold text-muted-foreground sm:inline">
				Use ← → or space
			</span>
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
			<section class="slide slide-cover" aria-hidden={activeSlide !== 0}>
				<div class="cover-grid" aria-hidden="true"></div>
				<div class="cover-orbit cover-orbit-one" aria-hidden="true"></div>
				<div class="cover-orbit cover-orbit-two" aria-hidden="true"></div>

				<div class="cover-copy">
					<div class="deck-kicker">
						<span></span>
						Quarter One · Engineering Report
					</div>
					<h1>
						Cordial Miners
						<strong>Quarter Report</strong>
					</h1>
					<p>
						From protocol research to a non-breaking consensus prototype inside the f1r3node
						ecosystem.
					</p>
				</div>

				<div class="cover-footer">
					<div>
						<span>Presented by</span>
						<strong>{profile.name}</strong>
						<small>{profile.role} · Addis Ababa, Ethiopia</small>
					</div>
					<div class="cover-mark" aria-hidden="true">
						<span>τ</span>
						<small>blocklace → order</small>
					</div>
				</div>
			</section>

			<section class="slide slide-protocol" aria-hidden={activeSlide !== 1}>
				<div class="slide-heading">
					<div>
						<p class="deck-kicker"><span></span> The protocol</p>
						<h2>Consensus grows from a <em>blocklace.</em></h2>
					</div>
					<p>
						Cordial Miners is a family of Byzantine Atomic Broadcast protocols. Instead of forcing
						every event into one chain, miners cooperatively build a DAG and derive the same safe
						order locally.
					</p>
				</div>

				<div class="protocol-layout">
					<div class="blocklace-panel">
						<div class="blocklace-label">
							<span>Local partial view</span>
							<strong>One DAG, many parents</strong>
						</div>
						<svg
							class="blocklace-svg"
							viewBox="0 0 640 350"
							role="img"
							aria-label="Animated blocklace DAG"
						>
							<defs>
								<linearGradient id="pathGradient" x1="0" x2="1">
									<stop offset="0" stop-color="#68dce6" />
									<stop offset="1" stop-color="#a68cff" />
								</linearGradient>
							</defs>
							<g class="dag-paths">
								<path d="M98 70 L220 70 L340 70 L466 70" />
								<path d="M98 145 L220 145 L340 145 L466 145" />
								<path d="M98 220 L220 220 L340 220 L466 220" />
								<path d="M98 295 L220 295 L340 295 L466 295" />
								<path d="M98 70 L220 145 M98 145 L220 70 M98 220 L220 145 M98 295 L220 220" />
								<path d="M220 70 L340 145 M220 145 L340 220 M220 220 L340 295 M220 295 L340 220" />
								<path d="M340 70 L466 145 M340 145 L466 70 M340 220 L466 145 M340 295 L466 220" />
								<path class="equivocation-path" d="M220 145 L335 118 M220 145 L335 172" />
								<path
									class="tau-path"
									d="M466 70 C530 70 522 145 570 145 C522 145 530 220 466 220"
								/>
							</g>
							<g class="round-labels">
								<text x="82" y="330">r</text>
								<text x="198" y="330">r + 1</text>
								<text x="318" y="330">r + 2</text>
								<text x="444" y="330">r + 3</text>
								<text x="555" y="330">τ</text>
							</g>
							<g class="dag-nodes">
								{#each [[98, 70, "cyan"], [98, 145, "amber"], [98, 220, "violet"], [98, 295, "emerald"], [220, 70, "cyan"], [220, 145, "amber"], [220, 220, "violet"], [220, 295, "emerald"], [340, 70, "cyan"], [340, 220, "violet"], [340, 295, "emerald"], [466, 70, "cyan"], [466, 145, "amber"], [466, 220, "violet"], [466, 295, "emerald"]] as node, index (index)}
									<circle
										class={`dag-node node-${node[2]}`}
										cx={node[0]}
										cy={node[1]}
										r="13"
										style={`--node-delay:${index * -110}ms`}
									/>
								{/each}
								<circle class="dag-node node-equivocation" cx="340" cy="118" r="13" />
								<circle class="dag-node node-equivocation" cx="340" cy="172" r="13" />
								<circle class="tau-node" cx="570" cy="145" r="25" />
								<text class="tau-symbol" x="570" y="153" text-anchor="middle">τ</text>
							</g>
						</svg>
						<div class="blocklace-note">
							<span class="note-dot"></span>
							Blocks may reference several predecessors; each node can hold a different partial view.
						</div>
					</div>

					<div class="protocol-pillars">
						<article class="pillar pillar-dissemination">
							<span class="pillar-number">01</span>
							<div>
								<h3>Dissemination</h3>
								<p>
									Send new blocks directly. Their pointers reveal what a peer knows, so missing
									history can be repaired.
								</p>
							</div>
							<Icon class="pillar-icon" icon="iconoir:network-right" width="26" />
						</article>
						<article class="pillar pillar-equivocation">
							<span class="pillar-number">02</span>
							<div>
								<h3>Equivocation exclusion</h3>
								<p>
									Conflicting blocks may exist in the DAG, but approval rules keep tainted branches
									out of final output.
								</p>
							</div>
							<Icon class="pillar-icon" icon="iconoir:warning-triangle" width="26" />
						</article>
						<article class="pillar pillar-ordering">
							<span class="pillar-number">03</span>
							<div>
								<h3>Ordering</h3>
								<p>
									Final leaders anchor τ, which topologically converts eligible DAG history into a
									stable local sequence.
								</p>
							</div>
							<Icon class="pillar-icon" icon="iconoir:sort-down" width="26" />
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-stack" aria-hidden={activeSlide !== 2}>
				<div class="shard-sky" aria-hidden="true">
					<div class="shard-node shard-rchain"><span>RChain</span></div>
					<div class="shard-node shard-asi"><span>ASIChain</span></div>
					<div class="shard-node shard-domain"><span>Your domain</span></div>
					<div class="shard-hub">
						<span>f1r3</span>
						<small>node</small>
					</div>
				</div>

				<div class="stack-layout">
					<div class="stack-copy">
						<p class="deck-kicker"><span></span> The host stack</p>
						<h2>f1r3node is not <em>one blockchain.</em></h2>
						<p class="stack-lead">
							It is a reusable node stack for quickly building the shard that best fits a domain.
						</p>
						<p>
							A domain chain can reuse networking, APIs, execution, storage, and validator
							infrastructure while selecting the consensus path and policies it needs. Cordial
							Miners proves that boundary by running as an explicit prototype mode alongside CBC
							Casper.
						</p>
						<div class="stack-callout">
							<Icon class="callout-icon" icon="iconoir:git-branch" width="22" />
							<span><strong>Same host capabilities.</strong> Different purpose-built shards.</span>
						</div>
					</div>

					<div class="stack-diagram">
						<div class="stack-layer layer-domain">
							<span class="layer-index">05</span>
							<div>
								<small>Domain shard</small>
								<strong>RChain · ASIChain · your chain</strong>
							</div>
							<Icon class="layer-icon" icon="iconoir:community" width="24" />
						</div>
						<div class="stack-layer layer-api">
							<span class="layer-index">04</span>
							<div>
								<small>Node-facing services</small>
								<strong>gRPC · HTTP · config · queries</strong>
							</div>
							<Icon class="layer-icon" icon="iconoir:server" width="24" />
						</div>
						<div class="stack-layer layer-runtime">
							<span class="layer-index">03</span>
							<div>
								<small>Execution and state</small>
								<strong>Rholang · RuntimeManager · RSpace</strong>
							</div>
							<Icon class="layer-icon" icon="iconoir:code-brackets" width="24" />
						</div>
						<div class="stack-layer layer-consensus">
							<span class="layer-index">02</span>
							<div>
								<small>Selectable consensus</small>
								<strong>CBC Casper <b>or</b> Cordial Miners</strong>
							</div>
							<Icon class="layer-icon" icon="iconoir:git-merge" width="24" />
						</div>
						<div class="stack-layer layer-infra">
							<span class="layer-index">01</span>
							<div>
								<small>Node infrastructure</small>
								<strong>Transport · persistence · validator identity</strong>
							</div>
							<Icon class="layer-icon" icon="iconoir:database" width="24" />
						</div>
					</div>
				</div>
			</section>

			<section class="slide slide-architecture" aria-hidden={activeSlide !== 3}>
				<div class="architecture-layout">
					<div class="architecture-copy">
						<p class="deck-kicker"><span></span> slide 04 · consensus architecture</p>
						<h2>Finality is more than <em>ancestry.</em></h2>
						<p class="architecture-lead">
							The core lesson from DAG/BFT study: reachability tells what a block can see,
							but finality also needs Byzantine-aware support predicates.
						</p>
						<p>
							Cordial Miners cannot treat simple DAG ancestry as enough. It must combine
							causal reachability, validator support, equivocation exclusion, and safe
							leader selection before a local τ can become final output.
						</p>
					</div>

					<div class="finality-board" aria-label="DAG reachability and Byzantine support concepts">
						<div class="finality-rail">
							<div class="finality-node node-a">A</div>
							<div class="finality-node node-b">B</div>
							<div class="finality-node node-c">C</div>
							<div class="finality-node node-d">D</div>
							<div class="finality-line line-one"></div>
							<div class="finality-line line-two"></div>
							<div class="finality-line line-three"></div>
							<div class="support-ring">support</div>
						</div>

						<div class="predicate-grid">
							<article class="predicate-card">
								<Icon class="predicate-icon" icon="iconoir:graph-up" width="24" />
								<span>01</span>
								<h3>DAG reachability</h3>
								<p>Tracks causal evidence across the blocklace and the partial view each miner holds.</p>
							</article>
							<article class="predicate-card">
								<Icon class="predicate-icon" icon="iconoir:shield-check" width="24" />
								<span>02</span>
								<h3>BFT support</h3>
								<p>Asks whether enough non-equivocating validator support makes that evidence safe.</p>
							</article>
							<article class="predicate-card">
								<Icon class="predicate-icon" icon="iconoir:branch" width="24" />
								<span>03</span>
								<h3>Linear-chain contrast</h3>
								<p>Chains inherit one parent; Cordial Miners reasons over many parents and conflicts.</p>
							</article>
						</div>
					</div>

					<div class="integration-strip">
						<article>
							<Icon class="integration-icon" icon="iconoir:component" width="24" />
							<div>
								<h3>Architecture audit</h3>
								<p>
									Reviewed how consensus-facing state stays separated from runtime-facing
									execution, storage, and API components in f1r3node.
								</p>
							</div>
						</article>
						<article>
							<Icon class="integration-icon" icon="iconoir:people-tag" width="24" />
							<div>
								<h3>Integration meeting</h3>
								<p>
									Discussed the intended Cordial Miners integration shape with the f1r3node team
									and aligned around clear architecture boundaries.
								</p>
							</div>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-issues" aria-hidden={activeSlide !== 4}>
				<div class="issue-slide-layout">
					<div class="issue-copy">
						<p class="deck-kicker"><span></span> slide 05 · intern guidance</p>
						<h2>Planning became <em>work tracks.</em></h2>
						<p>
							Created the first major issue structure so interns could work from clear
							boundaries: workspace split first, protocol math second, consensus safety third.
						</p>
					</div>

					<div class="issue-card-grid">
						<article class="issue-card issue-card-cyan">
							<a
								class="issue-link"
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/issues/3"
								target="_blank"
								rel="noreferrer"
							>
								#3
							</a>
							<span>Part 1</span>
							<h3>Workspace & pluggable consensus boundaries</h3>
							<p>
								Defined the Cargo workspace split: keep consensus math isolated in
								<code>cordial-miners-core</code> and node wiring in
								<code>cordial-miners-adapter</code>.
							</p>
						</article>

						<article class="issue-card issue-card-violet">
							<a
								class="issue-link"
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/issues/7"
								target="_blank"
								rel="noreferrer"
							>
								#7
							</a>
							<span>Part 2</span>
							<h3>Core DAG math</h3>
							<p>
								Scoped the generic blocklace data model and deterministic causal traversal
								primitives needed before higher-level Cordial Miners rules can be trusted.
							</p>
						</article>

						<article class="issue-card issue-card-amber">
							<a
								class="issue-link"
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/issues/12"
								target="_blank"
								rel="noreferrer"
							>
								#12
							</a>
							<span>Part 3</span>
							<h3>Consensus rules & economic safety</h3>
							<p>
								Separated core cordiality/equivocation predicates and durable evidence tracking
								from adapter-side slashing transaction encoding.
							</p>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-issues slide-issues-integration" aria-hidden={activeSlide !== 5}>
				<div class="issue-slide-layout issue-slide-layout-wide">
					<div class="issue-copy">
						<p class="deck-kicker"><span></span> slide 06 · F1R3FLY integration</p>
						<h2>Guidance connected protocol to <em>node reality.</em></h2>
						<p>
							The later issues made the plan concrete: finality and ordering rules had to end in
							a bridge that f1r3node can propose, persist, test, and operate.
						</p>
					</div>

					<div class="issue-integration-grid">
						<article class="issue-card issue-card-emerald">
							<a
								class="issue-link"
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/issues/16"
								target="_blank"
								rel="noreferrer"
							>
								#16
							</a>
							<span>Part 4</span>
							<h3>Finality & total ordering</h3>
							<p>
								Framed weighted cordial operators, leader finality, deterministic tau, and
								pruning/checkpoint behavior as the path from DAG evidence to stable output.
							</p>
						</article>

						<article class="issue-card issue-card-cyan">
							<a
								class="issue-link"
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/issues/22"
								target="_blank"
								rel="noreferrer"
							>
								#22
							</a>
							<span>Part 5</span>
							<h3>Integration, proposer, persistence bridge</h3>
							<p>
								Connected core protocol math to live-node concerns: gRPC ingest, proposer flow
								with RSpace, crypto, storage, and conformance testing.
							</p>
						</article>
					</div>

					<div class="planning-map" aria-label="Issue planning sequence">
						<div class="planning-step">workspace</div>
						<div class="planning-step">DAG math</div>
						<div class="planning-step">safety rules</div>
						<div class="planning-step">finality</div>
						<div class="planning-step">F1R3FLY bridge</div>
					</div>
				</div>
			</section>

			<section class="slide slide-refactor" aria-hidden={activeSlide !== 6}>
				<div class="refactor-layout">
					<div class="refactor-copy">
						<p class="deck-kicker"><span></span> slide 07 · architecture refactor</p>
						<h2>#32 Implement Generic <em>Traits.</em></h2>
						<p>
							<a
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/pull/32"
								target="_blank"
								rel="noreferrer"
							>
								feat: core/adapter workspace setup with generic consensus traits #32
							</a>
							turned the planning issues into Rust architecture: pure protocol math in core,
							f1r3node-facing wiring in adapters, and a factory seam for runtime selection.
						</p>
					</div>

					<div class="refactor-grid">
						<article class="refactor-card refactor-core">
							<span>01</span>
							<h3>Workspace split</h3>
							<p>
								<code>cordial-miners-core</code> owns blocklace, reachability, cordiality,
								approval, ratification, finality, ordering, pruning, and validation.
							</p>
						</article>

						<article class="refactor-card refactor-traits">
							<span>02</span>
							<h3>Boundary traits</h3>
							<p>
								<code>ConsensusEngine</code>, <code>BlockProvider</code>, and
								<code>ValidatorSet</code> let tests, repositories, RSpace-backed nodes, and
								future replay tools call the same core abstractly.
							</p>
						</article>

						<article class="refactor-card refactor-adapter">
							<span>03</span>
							<h3>Adapter ownership</h3>
							<p>
								<code>cordial-f1r3node-adapter</code> and
								<code>cordial-f1r3space-adapter</code> own translation, ingestion, proposer,
								persistence, runtime, crypto, and slashing bridges.
							</p>
						</article>
					</div>

					<div class="factory-panel">
						<div class="factory-mode">
							<small>runtime selector</small>
							<strong>Legacy</strong>
							<span>or</span>
							<strong>CordialMiners</strong>
						</div>
						<div class="factory-arrow" aria-hidden="true">
							<Icon icon="iconoir:arrow-right" width="24" />
						</div>
						<div class="factory-code">
							<small>factory seam</small>
							<code>ConsensusFactory.build(kind) → Box&lt;dyn AdapterConsensusEngine&gt;</code>
						</div>
					</div>

					<div class="workspace-strip" aria-label="Current workspace crate members">
						<span>workspace members</span>
						<code>crates/cordial-miners-core</code>
						<code>crates/cordial-f1r3node-adapter</code>
						<code>crates/cordial-f1r3space-adapter</code>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-ingest" aria-hidden={activeSlide !== 7}>
				<div class="review-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 08 · intern PR review</p>
						<h2>PR #37 built the <em>ingestion gate.</em></h2>
						<p>
							<a
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/pull/37"
								target="_blank"
								rel="noreferrer"
							>
								Issue 1: Build gRPC-to-Blocklace Adapter
							</a>
							It created the first clean boundary from
							f1r3node network block messages into Cordial Miners blocklace blocks.
						</p>
					</div>

					<div class="pipeline-board">
						<div class="pipeline-step">BlockMessage</div>
						<div class="pipeline-arrow"><Icon icon="iconoir:arrow-right" width="21" /></div>
						<div class="pipeline-step">GrpcBlockMapper</div>
						<div class="pipeline-arrow"><Icon icon="iconoir:arrow-right" width="21" /></div>
						<div class="pipeline-step">Structural validation</div>
						<div class="pipeline-arrow"><Icon icon="iconoir:arrow-right" width="21" /></div>
						<div class="pipeline-step">Cordial Block</div>
						<div class="pipeline-arrow"><Icon icon="iconoir:arrow-right" width="21" /></div>
						<div class="pipeline-step">BlocklaceAdapter.on_block</div>
					</div>

					<div class="review-grid">
						<article class="review-card">
							<span>mapper</span>
							<h3>Pure translation</h3>
							<p>
								<code>message_to_block(...)</code> maps protobuf/wire shape into core blocklace
								format without database writes, inserts, or finality updates.
							</p>
						</article>
						<article class="review-card">
							<span>validation</span>
							<h3>Fail-fast safety</h3>
							<p>
								Checks 32-byte hashes, recomputed content hash, identity hash, signatures, and
								structurally valid parent creator keys before consensus mutation.
							</p>
						</article>
						<article class="review-card">
							<span>tests</span>
							<h3>Rejected bad input</h3>
							<p>
								Covered valid mapping, corrupted hashes, invalid signatures, wrong creator keys,
								predecessor chains, determinism, and adapter rejection.
							</p>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-crypto" aria-hidden={activeSlide !== 8}>
				<div class="review-layout review-layout-split">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 09 · crypto adapter</p>
						<h2>PR #53 kept crypto <em>outside core.</em></h2>
						<p>
							<a
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/pull/53"
								target="_blank"
								rel="noreferrer"
							>
								F1r3fly Crypto Adapter
							</a>
							implemented the core
							<code>CryptoVerifier</code> trait with f1r3node-compatible signature checking.
						</p>
					</div>

					<div class="crypto-flow">
						<div><code>blocklace.insert(block, verifier)</code></div>
						<div><code>verify_block(content, signature, creator)</code></div>
						<div><code>recompute hash(content)</code></div>
						<div><code>Secp256k1Scheme / Ed25519Scheme</code></div>
						<div><strong>accept</strong><span>or</span><strong>reject</strong></div>
					</div>

					<div class="review-grid review-grid-two">
						<article class="review-card">
							<span>algorithms</span>
							<h3>Secp256k1 and Ed25519</h3>
							<p>
								Empty algorithm names default to Secp256k1, explicit strings select the scheme,
								and unknown algorithms fail instead of silently accepting bad input.
							</p>
						</article>
						<article class="review-card">
							<span>trust model</span>
							<h3>Never trust supplied hashes</h3>
							<p>
								The adapter recomputes the block hash from content during verification, rejects
								empty signatures, and catches tampered content after signing.
							</p>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-safety" aria-hidden={activeSlide !== 9}>
				<div class="safety-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 10 · PR #54 safety ladder</p>
						<h2>PR #54 added <em>support predicates.</em></h2>
						<p>
							<a
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/pull/54"
								target="_blank"
								rel="noreferrer"
							>
								Approval, Ratification and Super Ratification
							</a>
							it moved the project from valid block
							ingestion toward Byzantine-aware support inside
							<code>cordial-miners-core</code>.
						</p>
					</div>

					<div class="synchrony-panel">
						<h3>Eventual synchrony</h3>
						<p>
							Messages may arrive late, so every block carries a local DAG view through its
							predecessor closure. The predicates must be safe on partial views and still
							converge as honest nodes eventually receive the same blocklace evidence.
						</p>
					</div>

					<div class="ladder-grid">
						<article>
							<span>01</span>
							<h3>Approval</h3>
							<p>A block sees the target and does not see an incomparable conflict from that creator.</p>
						</article>
						<article>
							<span>02</span>
							<h3>Ratification</h3>
							<p>A block's closure contains a stake supermajority of blocks approving the target.</p>
						</article>
						<article>
							<span>03</span>
							<h3>Super-ratification</h3>
							<p>A witness set contains a stake supermajority of blocks ratifying the target.</p>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-approval-math" aria-hidden={activeSlide !== 10}>
				<div class="math-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 11 · approver model</p>
						<h2>The approver is a <em>block.</em></h2>
						<p>
							In <code>approves(blocklace, approver, target)</code>, the approver is not a
							special role or vote message. It is the block whose predecessor history judges
							whether it safely supports another block.
						</p>
					</div>

					<div class="formula-panel">
						<div>
							<span>observed conflict</span>
							<code>
								conflict(t,t') := same_creator and incomparable(t,t') and observed_by(approver,t')
							</code>
						</div>
						<div>
							<span>approval</span>
							<code>
								approves(b,t) := observes(b,t) and no observed t' conflicts with t
							</code>
						</div>
					</div>

					<div class="math-card-row">
						<article>
							<h3>Block-local view</h3>
							<p>
								The creator may make many blocks. Each block has its own DAG view, so approval
								is checked from a block identity and its observed predecessor closure.
							</p>
						</article>
						<article>
							<h3>Why same-round was not enough</h3>
							<p>
								PR #54 corrected the rule toward same creator, incomparable with target, and
								observed by the approver. Conflicts can happen across branches, not only rounds.
							</p>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-ratification-math" aria-hidden={activeSlide !== 11}>
				<div class="math-layout math-layout-wide">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 12 · ratification math</p>
						<h2>Supermajority creates <em>finality pressure.</em></h2>
						<p>
							Ratification aggregates approval. Super-ratification aggregates knowledge that
							ratification happened. Under eventual synchrony, lagging honest nodes can catch up
							to the same final leader evidence before tau ordering.
						</p>
					</div>

					<div class="formula-panel formula-panel-vertical">
						<div>
							<span>ratification</span>
							<code>sum(weight(approvers)) * 3 &gt; total_weight * 2</code>
						</div>
						<div>
							<span>super-ratification</span>
							<code>sum(weight(ratifiers)) * 3 &gt; total_weight * 2</code>
						</div>
					</div>

					<div class="finality-summary">
						<article>
							<h3>Why strictly above two-thirds?</h3>
							<p>
								Two conflicting ratifications would need overlapping supermajorities. Honest
								overlap cannot approve both because approval rejects observed equivocation.
							</p>
						</article>
						<article>
							<h3>What super-ratification adds</h3>
							<p>
								It proves that enough stake has seen enough ratification. That meta-knowledge is
								what later finality logic can use before tau ordering.
							</p>
						</article>
						<article>
							<h3>Layer boundary</h3>
							<p>
								gRPC ingestion prepares valid core blocks. Approval, ratification, and
								super-ratification interpret those blocks after they enter the blocklace.
							</p>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-weighted" aria-hidden={activeSlide !== 12}>
				<div class="weighted-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 13 · PR #62 weighted ratification</p>
						<h2>My focus: <em>stake-weighted support.</em></h2>
						<p>
							<a
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/pull/62"
								target="_blank"
								rel="noreferrer"
							>
								PR #62 Add weighted ratification variants
							</a>
						    it preserved the
							paper-native predicates while adding f1r3node-compatible stake accounting.
						</p>
					</div>

					<div class="weight-contrast">
						<article>
							<span>paper-native</span>
							<h3>Count distinct creators</h3>
							<p>
								The original predicates treat each miner as one support unit. That is useful for
								proving the Cordial Miners protocol shape.
							</p>
						</article>
						<article>
							<span>f1r3node-compatible</span>
							<h3>Sum bonded stake</h3>
							<p>
								f1r3node is Proof-of-Stake oriented, so the weighted variants measure support by
								total validator bond weight.
							</p>
						</article>
					</div>

					<div class="weighted-principle">
						<Icon icon="iconoir:scale" width="28" />
						<p>
							Approval stays DAG and Byzantine-aware. Only the support accounting changes from
							"how many creators" to "how much bonded stake."
						</p>
					</div>

					<div class="weighted-functions">
						<article>
							<code>weighted_approving_creators(...)</code>
							<p>Collects positive-weight creators whose blocks approve the target.</p>
						</article>
						<article>
							<code>weighted_ratifies(...)</code>
							<p>Checks whether a ratifier's closure has enough weighted approval.</p>
						</article>
						<article>
							<code>weighted_super_ratifies(...)</code>
							<p>Checks whether witnesses contain enough weighted ratifiers.</p>
						</article>
						<article>
							<code>is_weighted_supermajority(...)</code>
							<p>Applies the strict stake threshold without floating point math.</p>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-weighted-detail" aria-hidden={activeSlide !== 13}>
				<div class="weighted-detail-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 14 · weighted implementation</p>
						<h2>Weight comes from <em>bond state.</em></h2>
						<p>
							The weighted path receives <code>HashMap&lt;NodeId, u64&gt;</code>: a decision-context
							bond map. Unknown and zero-weight creators are ignored, and each creator is counted
							once with <code>HashSet&lt;NodeId&gt;</code>.
						</p>
					</div>

					<div class="threshold-panel">
						<div>
							<span>strict supermajority</span>
							<code>support_weight * 3 &gt; total_weight * 2</code>
						</div>
						<div>
							<span>example</span>
							<code>A=4, B=3, C=3; &#123;A,B&#125;=7/10 passes, &#123;B,C&#125;=6/10 fails</code>
						</div>
					</div>

					<div class="bond-flow">
						<div>PoS / RSpace state</div>
						<Icon icon="iconoir:arrow-right" width="19" />
						<div>computeBonds(postStateHash)</div>
						<Icon icon="iconoir:arrow-right" width="19" />
						<div>block.body.state.bonds</div>
						<Icon icon="iconoir:arrow-right" width="19" />
						<div>Cordial bonds_map()</div>
						<Icon icon="iconoir:arrow-right" width="19" />
						<div>weighted predicates</div>
					</div>

					<div class="weighted-detail-grid">
						<article>
							<h3>No double counting</h3>
							<p>
								If one validator creates multiple approving blocks, its bond contributes once.
								Support is by creator weight, not block count.
							</p>
						</article>
						<article>
							<h3>Fail-closed arithmetic</h3>
							<p>
								Weights are <code>u64</code>, but sums and multiplication use <code>u128</code>
								checked arithmetic so overflow cannot create a false supermajority.
							</p>
						</article>
						<article>
							<h3>Scope boundary</h3>
							<p>
								PR #62 did not implement leader finality, tau ordering, networking, or proposal.
								It added the stake-aware support predicates those later steps can use.
							</p>
						</article>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-checkpoint" aria-hidden={activeSlide !== 14}>
				<div class="checkpoint-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 15 · PR #79 checkpoint pruning</p>
						<h2>Finalized history becomes a <em>boundary.</em></h2>
						<p>
							<a
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/pull/79"
								target="_blank"
								rel="noreferrer"
							>
								PR #79 Add Checkpoint DAG pruning
							</a>
							keeps the active blocklace bounded: after a final leader is known, old ancestors
							can leave the in-memory <code>HashMap</code> while tau output is preserved.
						</p>
					</div>

					<div class="checkpoint-diagram">
						<div class="checkpoint-row">
							<span>before</span>
							<div>old history</div>
							<Icon icon="iconoir:arrow-right" width="18" />
							<div>finalized checkpoint</div>
							<Icon icon="iconoir:arrow-right" width="18" />
							<div>live future blocks</div>
						</div>
						<div class="checkpoint-row checkpoint-row-after">
							<span>after</span>
							<div>checkpoint boundary</div>
							<Icon icon="iconoir:arrow-right" width="18" />
							<div>live future blocks</div>
							<strong>old contents pruned</strong>
						</div>
					</div>

					<div class="checkpoint-grid">
						<article>
							<span>01</span>
							<h3>Preserve ordering</h3>
							<p>
								Before removing ancestors, the blocklace stores compact
								<code>checkpoint_order_prefix</code> and
								<code>checkpoint_weighted_order_prefix</code> identity sequences.
							</p>
						</article>
						<article>
							<span>02</span>
							<h3>Weighted path matters</h3>
							<p>
								<code>checkpoint_after_weighted_finality(...)</code> finds the weighted final
								leader, computes <code>weighted_tau</code>, stores its prefix, then prunes.
							</p>
						</article>
						<article>
							<span>03</span>
							<h3>Safe garbage collection</h3>
							<p>
								Unknown, backward, or disconnected checkpoints are rejected, and retained live
								blocks keep any predecessors they still reference.
							</p>
						</article>
					</div>

					<div class="checkpoint-flow">
						<code>final leader</code>
						<Icon icon="iconoir:arrow-right" width="18" />
						<code>tau / weighted_tau prefix</code>
						<Icon icon="iconoir:arrow-right" width="18" />
						<code>prune observed ancestors</code>
						<Icon icon="iconoir:arrow-right" width="18" />
						<code>observe() stops at checkpoint</code>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-lmdb" aria-hidden={activeSlide !== 15}>
				<div class="lmdb-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 16 · PR #90 LMDB persistence</p>
						<h2>Blocklace moved from <em>memory to disk.</em></h2>
						<p>
							<a
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/pull/90"
								target="_blank"
								rel="noreferrer"
							>
								PR #90 Feat lmdb
							</a>
							added a disk-backed repository for Cordial blocks. The consensus core still owns
							blocklace rules; the adapter owns durable storage and restart recovery.
						</p>
					</div>

					<div class="lmdb-flow">
						<div>network block</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>validate / translate</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>persist to LMDB</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>insert Blocklace</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>process consensus</div>
					</div>

					<div class="lmdb-grid">
						<article>
							<span>location</span>
							<h3><code>&lt;data_dir&gt;/blocklace/</code></h3>
							<p>
								The node supplies the data directory. LMDB creates its environment files under
								the Cordial blocklace subdirectory.
							</p>
						</article>
						<article>
							<span>blocks DB</span>
							<h3><code>cordial-blocks</code></h3>
							<p>
								Stores <code>bincode(BlockIdentity)</code> to <code>bincode(Block)</code>, enough
								to reconstruct predecessor links after restart.
							</p>
						</article>
						<article>
							<span>meta DB</span>
							<h3><code>cordial-meta</code></h3>
							<p>
								Stores <code>finalized_cursor</code> as a compact finality bookmark, not as the
								block body itself.
							</p>
						</article>
					</div>

					<div class="lmdb-note">
						<Icon icon="iconoir:database" width="24" />
						<p>
							<code>bincode</code> is local disk serialization for LMDB persistence. It is not the
							node-to-node wire format; f1r3node network boundaries use protobuf/prost shapes.
						</p>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-lmdb-recovery" aria-hidden={activeSlide !== 16}>
				<div class="lmdb-recovery-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 17 · restart recovery</p>
						<h2>Recovery replays blocks <em>parents first.</em></h2>
						<p>
							PR #90 makes restart possible by reopening LMDB, reading persisted Cordial blocks,
							sorting them so predecessors appear before children, then replaying them into the
							in-memory blocklace.
						</p>
					</div>

					<div class="recovery-flow">
						<div>open repository</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>read cursor</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>scan blocks</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>topological sort</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>replay Blocklace</div>
					</div>

					<div class="recovery-grid">
						<article>
							<h3>Disk before memory</h3>
							<p>
								The write path persists a block before in-memory insertion. If the process
								crashes after commit, recovery can replay the durable block.
							</p>
						</article>
						<article>
							<h3>Closure invariant</h3>
							<p>
								<code>Blocklace::insert</code> requires predecessors to exist first, so recovery
								sorts by DAG height before replay.
							</p>
						</article>
						<article>
							<h3>Trait boundary</h3>
							<p>
								<code>BlocklaceRepository</code> sits behind <code>Arc&lt;dyn ...&gt;</code>, so
								LMDB can be attached without coupling consensus math to <code>heed</code>.
							</p>
						</article>
					</div>

					<div class="recovery-invariants">
						<code>put_block(block) before engine.insert(block)</code>
						<code>Ok(None) means missing; Err means storage or decode failure</code>
						<code>corrupt scan entries warn and skip; direct corrupt get returns Bincode error</code>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-slashing" aria-hidden={activeSlide !== 17}>
				<div class="slashing-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 18 · slashing boundary</p>
						<h2>Equivocation becomes <em>slashable evidence.</em></h2>
						<p>
							The f1r3node adapter keeps slashing outside the pure blocklace protocol. Core
							consensus detects conflicting same-creator blocks; the adapter prepares that
							evidence for f1r3node-facing consequences.
						</p>
					</div>

					<div class="slashing-map">
						<article>
							<span>core</span>
							<h3>Detect conflict</h3>
							<p>
								Approval rejects a target when the approver observes an incomparable block from
								the same creator. That is the DAG-level equivocation signal.
							</p>
						</article>
						<article>
							<span>adapter</span>
							<h3>Translate evidence</h3>
							<p>
								The <code>slashing</code> module belongs with f1r3node wiring, alongside block
								translation, crypto bridge, proposer, repository, and runtime bridge modules.
							</p>
						</article>
						<article>
							<span>node</span>
							<h3>Apply policy</h3>
							<p>
								f1r3node-specific code can decide how equivocation evidence maps to validator
								accounting, penalties, or transaction/runtime behavior.
							</p>
						</article>
					</div>

					<div class="module-strip">
						<code>block_translation</code>
						<code>crypto_bridge</code>
						<code>grpc_ingest</code>
						<code>repository</code>
						<code>runtime_bridge</code>
						<code>slashing</code>
					</div>

					<div class="slashing-flow">
						<div>same creator</div>
						<Icon icon="iconoir:plus" width="18" />
						<div>incomparable blocks</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>equivocation evidence</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>adapter slashing bridge</div>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-docker-demo" aria-hidden={activeSlide !== 18}>
				<div class="docker-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 19 · PR #98 Docker demo</p>
						<h2>Docker made the prototype <em>observable.</em></h2>
						<p>
							<a
								href="https://github.com/iCog-Labs-Dev/cordial-f1r3node/pull/98"
								target="_blank"
								rel="noreferrer"
							>
								PR #98 Integrate Cordial Miners runtime, demos, and conformance coverage
							</a>
							packaged the runtime-facing integration surface: adapter conformance tests,
							standalone Cordial startup, and a four-node local-intercept demo.
						</p>
					</div>

					<div class="docker-flow">
						<div>Justfile target</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>Compose config</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>4 local runtimes</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>proposal APIs</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>same ordered view</div>
					</div>

					<div class="docker-grid">
						<article>
							<span>runtime selector</span>
							<h3><code>--consensus cordial-miners</code></h3>
							<p>
								CBC Casper stays the default. The demo starts the Cordial path only through the
								explicit consensus selection and adapter wiring.
							</p>
						</article>
						<article>
							<span>demo surface</span>
							<h3><code>docker/four-node-intercept.yml</code></h3>
							<p>
								Four local runtimes intercept proposals, expose block views, and exercise the
								Cordial ordering path without claiming production peer replacement.
							</p>
						</article>
						<article>
							<span>verifier</span>
							<h3><code>verify-four-node-order.sh</code></h3>
							<p>
								The script waits for HTTP APIs, checks network and validator status, reads each
								block view, then compares the ordered projection across all nodes.
							</p>
						</article>
					</div>

					<div class="docker-note">
						<Icon icon="iconoir:server" width="24" />
						<p>
							The evidence target is local convergence: all four runtimes report the same ordered
							Cordial view. That is useful demo proof, while full production f1r3node networking
							hardening remains out of scope for this PR.
						</p>
					</div>
				</div>
			</section>

			<section class="slide slide-review slide-f1r3node-track" aria-hidden={activeSlide !== 19}>
				<div class="f1r3node-layout">
					<div class="review-copy">
						<p class="deck-kicker"><span></span> slide 20 · f1r3node contribution track</p>
						<h2>Recovery work met <em>adapter abstraction.</em></h2>
						<p>
							The quarter had two connected f1r3node tracks: upstream Rust-node recovery in
							<a
								href="https://github.com/F1R3FLY-io/f1r3node-rust/pull/63"
								target="_blank"
								rel="noreferrer"
							>
								PR #63
							</a>
							and fork-side runtime abstraction in
							<a href="https://github.com/Nama21yo/f1r3node" target="_blank" rel="noreferrer">
								Nama21yo/f1r3node
							</a>.
							Both kept CBC Casper as the default path while making failure recovery and
							Cordial integration easier to test.
						</p>
					</div>

					<div class="f1r3node-track-grid">
						<article>
							<span>upstream PR #63</span>
							<h3>Minority-fork recovery</h3>
							<p>
								A stale validator leaves <code>Running</code>, re-enters initialization,
								requests <code>ApprovedBlock</code>, and rebuilds from the majority chain.
							</p>
						</article>
						<article>
							<span>fork branch</span>
							<h3>Selectable consensus adapters</h3>
							<p>
								The fork adds <code>ConsensusKind</code>, adapter traits, packet routing, and
								runtime setup so Cordial Miners can be selected explicitly.
							</p>
						</article>
					</div>

					<div class="f1r3node-flow">
						<div>stale latest message</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>initialization recovery</div>
						<Icon icon="iconoir:plus" width="18" />
						<div>consensus factory</div>
						<Icon icon="iconoir:arrow-right" width="18" />
						<div>Casper or Cordial adapter</div>
					</div>

					<div class="f1r3node-boundary">
						<article>
							<span>verified status</span>
							<h3><code>42 passed, 0 failed</code></h3>
							<p>
								PR #63 includes stale recovery tests, minority-fork regression coverage,
								Rholang payload-capture coverage, and CI/advisory follow-up.
							</p>
						</article>
						<article>
							<span>status boundary</span>
							<h3>Fork-side prototype</h3>
							<p>
								The adapter abstraction is contribution work on the fork. Describe it as
								runtime-selectable integration work, not merged upstream f1r3node behavior.
							</p>
						</article>
					</div>

					<div class="f1r3node-note">
						<Icon icon="iconoir:branch" width="24" />
						<p>
							The shared value is a safer integration boundary: validators can recover from a
							dead minority branch, while node APIs gain a clear place to route Casper or
							Cordial behavior without changing the default consensus mode.
						</p>
					</div>

					<div class="f1r3node-next-plan">
						<div class="f1r3node-next-plan-head">
							<span>next plan</span>
							<h3>What comes after this prototype</h3>
						</div>
						<ol>
							<li>Change the standalone Cordial blocklace prototype to use the f1r3node DAG implementation directly.</li>
							<li>Implement Proof of Reputation as the validator/reputation weighting layer.</li>
							<li>Implement the proof-based consensus protocol written by Ben.</li>
						</ol>
					</div>
				</div>
			</section>
		</div>

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

			<span class="deck-counter">{String(activeSlide + 1).padStart(2, "0")} / 20</span>

			<button
				type="button"
				onclick={() => goToSlide(activeSlide + 1)}
				disabled={activeSlide === slideCount - 1}
				aria-label="Next slide"
			>
				<Icon icon="iconoir:arrow-right" width="19" />
			</button>
		</div>
	</div>
</section>

<style>
	.presentation-shell {
		--deck-ink: #f4f7fb;
		--deck-muted: rgba(244, 247, 251, 0.62);
		--deck-line: rgba(255, 255, 255, 0.12);
		--deck-cyan: #6fe5ec;
		--deck-amber: #f6c96b;
		--deck-violet: #a594ff;
		--deck-emerald: #75dfa8;
	}

	.deck {
		position: relative;
		overflow: hidden;
		min-height: min(79vh, 780px);
		border: 1px solid rgba(255, 255, 255, 0.12);
		border-radius: 2rem;
		background: #11141c;
		color: var(--deck-ink);
		box-shadow: 0 40px 140px rgba(15, 23, 42, 0.28);
		isolation: isolate;
	}

	.deck:fullscreen {
		width: 100vw;
		height: 100vh;
		min-height: 100vh;
		border: 0;
		border-radius: 0;
	}

	.deck-progress {
		position: absolute;
		top: 0;
		right: 0;
		left: 0;
		z-index: 20;
		height: 4px;
		background: rgba(255, 255, 255, 0.08);
	}

	.deck-progress span {
		display: block;
		height: 100%;
		background: linear-gradient(90deg, var(--deck-cyan), var(--deck-violet));
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
		padding: clamp(2rem, 5vw, 5.4rem);
		padding-bottom: clamp(6rem, 8vw, 7rem);
	}

	.slide-cover {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		background:
			radial-gradient(circle at 82% 20%, rgba(111, 229, 236, 0.2), transparent 30%),
			radial-gradient(circle at 12% 88%, rgba(165, 148, 255, 0.17), transparent 34%), #10131a;
	}

	.cover-grid {
		position: absolute;
		inset: 0;
		opacity: 0.2;
		background-image:
			linear-gradient(rgba(255, 255, 255, 0.12) 1px, transparent 1px),
			linear-gradient(90deg, rgba(255, 255, 255, 0.12) 1px, transparent 1px);
		background-size: 54px 54px;
		mask-image: radial-gradient(circle at 70% 40%, black, transparent 72%);
	}

	.cover-orbit {
		position: absolute;
		right: -7rem;
		top: 50%;
		border: 1px dashed rgba(255, 255, 255, 0.2);
		border-radius: 50%;
		animation: coverOrbit 22s linear infinite;
	}

	.cover-orbit::before,
	.cover-orbit::after {
		content: "";
		position: absolute;
		width: 1rem;
		height: 1rem;
		border-radius: 50%;
		background: var(--deck-cyan);
		box-shadow: 0 0 28px var(--deck-cyan);
	}

	.cover-orbit::before {
		top: 8%;
		left: 23%;
	}

	.cover-orbit::after {
		right: 7%;
		bottom: 25%;
		background: var(--deck-violet);
		box-shadow: 0 0 28px var(--deck-violet);
	}

	.cover-orbit-one {
		width: 36rem;
		height: 36rem;
	}

	.cover-orbit-two {
		right: 1rem;
		width: 25rem;
		height: 25rem;
		animation-direction: reverse;
		animation-duration: 16s;
	}

	.cover-copy,
	.cover-footer {
		position: relative;
		z-index: 3;
	}

	.deck-kicker {
		display: flex;
		align-items: center;
		gap: 0.65rem;
		color: var(--deck-cyan);
		font-size: 0.72rem;
		font-weight: 900;
		letter-spacing: 0.2em;
		text-transform: uppercase;
	}

	.deck-kicker span {
		width: 1.8rem;
		height: 2px;
		background: currentColor;
	}

	.cover-copy h1 {
		max-width: 58rem;
		margin-top: 1.8rem;
		font-family: var(--font-display);
		font-size: clamp(4rem, 8.8vw, 9rem);
		font-weight: 900;
		letter-spacing: -0.07em;
		line-height: 0.79;
	}

	.cover-copy h1 strong {
		display: block;
		color: transparent;
		-webkit-text-stroke: 1.5px rgba(255, 255, 255, 0.65);
	}

	.cover-copy p {
		max-width: 43rem;
		margin-top: 2rem;
		color: var(--deck-muted);
		font-size: clamp(1rem, 1.5vw, 1.3rem);
		line-height: 1.7;
	}

	.cover-footer {
		display: flex;
		align-items: end;
		justify-content: space-between;
		gap: 2rem;
	}

	.cover-footer > div:first-child {
		display: grid;
		gap: 0.2rem;
	}

	.cover-footer span,
	.cover-footer small {
		color: var(--deck-muted);
		font-size: 0.69rem;
		font-weight: 800;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.cover-footer strong {
		font-size: 1.2rem;
	}

	.cover-mark {
		display: grid;
		min-width: 9rem;
		place-items: center;
		border: 1px solid var(--deck-line);
		border-radius: 1.4rem;
		background: rgba(255, 255, 255, 0.055);
		padding: 1rem 1.4rem;
		backdrop-filter: blur(12px);
		rotate: -3deg;
		animation: markFloat 4s ease-in-out infinite;
	}

	.cover-mark span {
		color: var(--deck-ink);
		font-family: var(--font-display);
		font-size: 3rem;
		line-height: 1;
	}

	.slide-protocol {
		background:
			radial-gradient(circle at 15% 20%, rgba(111, 229, 236, 0.12), transparent 28%), #11141c;
	}

	.slide-heading {
		position: relative;
		z-index: 2;
		display: grid;
		gap: 1.5rem;
		margin-bottom: 2.2rem;
	}

	.slide-heading h2,
	.stack-copy h2 {
		max-width: 48rem;
		margin-top: 0.8rem;
		font-family: var(--font-display);
		font-size: clamp(2.65rem, 5vw, 5.1rem);
		font-weight: 900;
		letter-spacing: -0.05em;
		line-height: 0.95;
	}

	.slide-heading h2 em,
	.stack-copy h2 em {
		color: var(--deck-cyan);
		font-style: normal;
	}

	.slide-heading > p {
		max-width: 42rem;
		align-self: end;
		color: var(--deck-muted);
		font-size: 0.96rem;
		line-height: 1.7;
	}

	.protocol-layout {
		position: relative;
		z-index: 2;
		display: grid;
		gap: 1.2rem;
	}

	.blocklace-panel {
		position: relative;
		overflow: hidden;
		min-height: 26rem;
		border: 1px solid var(--deck-line);
		border-radius: 1.7rem;
		background:
			linear-gradient(rgba(255, 255, 255, 0.04) 1px, transparent 1px),
			linear-gradient(90deg, rgba(255, 255, 255, 0.04) 1px, transparent 1px),
			rgba(255, 255, 255, 0.025);
		background-size: 32px 32px;
	}

	.blocklace-label {
		position: absolute;
		top: 1.1rem;
		left: 1.2rem;
		z-index: 2;
		display: grid;
	}

	.blocklace-label span {
		color: var(--deck-cyan);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.17em;
		text-transform: uppercase;
	}

	.blocklace-label strong {
		margin-top: 0.25rem;
		font-size: 0.92rem;
	}

	.blocklace-svg {
		width: 100%;
		height: 100%;
		min-height: 25rem;
		padding: 2rem 0.8rem 0.3rem;
	}

	.dag-paths path {
		fill: none;
		stroke: url("#pathGradient");
		stroke-dasharray: 8 8;
		stroke-width: 2;
		opacity: 0.45;
		animation: pathMove 4s linear infinite;
	}

	.dag-paths .equivocation-path {
		stroke: #ff7e8c;
		opacity: 0.8;
	}

	.dag-paths .tau-path {
		stroke: var(--deck-amber);
		stroke-width: 3;
	}

	.round-labels {
		fill: rgba(255, 255, 255, 0.4);
		font-size: 12px;
		font-weight: 800;
	}

	.dag-node {
		stroke: rgba(255, 255, 255, 0.25);
		stroke-width: 6;
		transform-box: fill-box;
		transform-origin: center;
		animation: dagPulse 2.7s ease-in-out infinite;
		animation-delay: var(--node-delay);
	}

	.node-cyan {
		fill: var(--deck-cyan);
	}

	.node-amber {
		fill: var(--deck-amber);
	}

	.node-violet {
		fill: var(--deck-violet);
	}

	.node-emerald {
		fill: var(--deck-emerald);
	}

	.node-equivocation {
		fill: #ff7e8c;
		stroke: rgba(255, 126, 140, 0.25);
		stroke-width: 9;
		animation: equivocationPulse 1.5s ease-in-out infinite;
	}

	.tau-node {
		fill: var(--deck-amber);
		stroke: rgba(246, 201, 107, 0.2);
		stroke-width: 10;
		animation: tauPulse 2s ease-in-out infinite;
	}

	.tau-symbol {
		fill: #11141c;
		font-family: var(--font-display);
		font-size: 27px;
		font-weight: 900;
	}

	.blocklace-note {
		position: absolute;
		right: 1rem;
		bottom: 1rem;
		left: 1rem;
		display: flex;
		align-items: center;
		gap: 0.6rem;
		border: 1px solid var(--deck-line);
		border-radius: 999px;
		background: rgba(17, 20, 28, 0.82);
		padding: 0.65rem 0.9rem;
		color: var(--deck-muted);
		font-size: 0.7rem;
		font-weight: 700;
		backdrop-filter: blur(12px);
	}

	.note-dot {
		width: 0.5rem;
		height: 0.5rem;
		flex: 0 0 auto;
		border-radius: 50%;
		background: var(--deck-cyan);
		box-shadow: 0 0 16px var(--deck-cyan);
	}

	.protocol-pillars {
		display: grid;
		gap: 0.8rem;
	}

	.pillar {
		position: relative;
		display: grid;
		grid-template-columns: auto 1fr auto;
		gap: 1rem;
		align-items: center;
		overflow: hidden;
		border: 1px solid var(--deck-line);
		border-radius: 1.45rem;
		background: rgba(255, 255, 255, 0.04);
		padding: 1.1rem;
		animation: pillarGlow 9s ease-in-out infinite;
	}

	.pillar::before {
		content: "";
		position: absolute;
		inset: 0 auto 0 0;
		width: 3px;
		background: var(--pillar-color);
	}

	.pillar-dissemination {
		--pillar-color: var(--deck-cyan);
	}

	.pillar-equivocation {
		--pillar-color: #ff7e8c;
		animation-delay: -6s;
	}

	.pillar-ordering {
		--pillar-color: var(--deck-amber);
		animation-delay: -3s;
	}

	.pillar-number {
		color: var(--pillar-color);
		font-family: var(--font-display);
		font-size: 1.5rem;
		font-weight: 900;
	}

	.pillar h3 {
		font-size: 0.95rem;
		font-weight: 900;
	}

	.pillar p {
		margin-top: 0.25rem;
		color: var(--deck-muted);
		font-size: 0.69rem;
		line-height: 1.55;
	}

	.pillar :global(.pillar-icon) {
		color: var(--pillar-color);
	}

	.slide-stack {
		background:
			radial-gradient(circle at 75% 8%, rgba(165, 148, 255, 0.15), transparent 26%), #11141c;
	}

	.shard-sky {
		position: absolute;
		top: 1.5rem;
		right: 4%;
		width: min(34rem, 48vw);
		height: 11rem;
		opacity: 0.95;
	}

	.shard-sky::before,
	.shard-sky::after {
		content: "";
		position: absolute;
		top: 50%;
		left: 50%;
		border: 1px dashed rgba(255, 255, 255, 0.13);
		border-radius: 50%;
		translate: -50% -50%;
		animation: coverOrbit 20s linear infinite;
	}

	.shard-sky::before {
		width: 25rem;
		height: 9rem;
	}

	.shard-sky::after {
		width: 17rem;
		height: 6rem;
		animation-direction: reverse;
		animation-duration: 13s;
	}

	.shard-node,
	.shard-hub {
		position: absolute;
		z-index: 2;
		display: grid;
		place-items: center;
		border: 1px solid var(--deck-line);
		background: rgba(255, 255, 255, 0.065);
		box-shadow: 0 18px 50px rgba(0, 0, 0, 0.18);
		backdrop-filter: blur(12px);
		animation: shardFloat 4s ease-in-out infinite;
	}

	.shard-node {
		width: 5.2rem;
		height: 2.2rem;
		border-radius: 999px;
		font-size: 0.58rem;
		font-weight: 900;
		letter-spacing: 0.1em;
		text-transform: uppercase;
	}

	.shard-rchain {
		top: 9%;
		left: 10%;
		color: var(--deck-cyan);
	}

	.shard-asi {
		top: 16%;
		right: 4%;
		color: var(--deck-violet);
		animation-delay: -1.5s;
	}

	.shard-domain {
		right: 18%;
		bottom: 3%;
		width: 6.4rem;
		color: var(--deck-amber);
		animation-delay: -2.8s;
	}

	.shard-hub {
		top: 50%;
		left: 50%;
		width: 5.2rem;
		height: 5.2rem;
		border-radius: 1.7rem;
		background: var(--deck-ink);
		color: #11141c;
		translate: -50% -50%;
		rotate: 5deg;
		animation-name: hubFloat;
	}

	.shard-hub span {
		font-family: var(--font-display);
		font-size: 1.35rem;
		font-weight: 900;
		letter-spacing: -0.08em;
	}

	.shard-hub small {
		margin-top: -1.15rem;
		font-size: 0.53rem;
		font-weight: 900;
		letter-spacing: 0.18em;
		text-transform: uppercase;
	}

	.stack-layout {
		position: relative;
		z-index: 3;
		display: grid;
		gap: 2rem;
		align-items: end;
		min-height: 100%;
		padding-top: 7rem;
	}

	.stack-copy {
		max-width: 42rem;
	}

	.stack-lead {
		margin-top: 1.5rem;
		color: var(--deck-ink) !important;
		font-size: clamp(1.05rem, 1.8vw, 1.45rem) !important;
		font-weight: 800;
	}

	.stack-copy > p:not(.deck-kicker):not(.stack-lead) {
		max-width: 39rem;
		margin-top: 1rem;
		color: var(--deck-muted);
		font-size: 0.87rem;
		line-height: 1.7;
	}

	.stack-callout {
		display: flex;
		gap: 0.75rem;
		align-items: center;
		width: fit-content;
		margin-top: 1.4rem;
		border: 1px solid rgba(111, 229, 236, 0.22);
		border-radius: 999px;
		background: rgba(111, 229, 236, 0.08);
		padding: 0.75rem 1rem;
		color: var(--deck-muted);
		font-size: 0.7rem;
	}

	.stack-callout :global(.callout-icon),
	.stack-callout strong {
		color: var(--deck-cyan);
	}

	.stack-diagram {
		display: flex;
		flex-direction: column;
		gap: 0.55rem;
		perspective: 900px;
	}

	.stack-layer {
		--layer-color: var(--deck-cyan);
		display: grid;
		grid-template-columns: auto 1fr auto;
		gap: 0.9rem;
		align-items: center;
		border: 1px solid color-mix(in srgb, var(--layer-color) 26%, transparent);
		border-radius: 1.15rem;
		background: color-mix(in srgb, var(--layer-color) 8%, rgba(255, 255, 255, 0.025));
		padding: 0.85rem 1rem;
		transform: rotateX(6deg) translateY(0);
		transform-origin: bottom;
		animation: stackRise 4.5s ease-in-out infinite;
	}

	.layer-api {
		--layer-color: var(--deck-violet);
		animation-delay: -0.7s;
	}

	.layer-runtime {
		--layer-color: var(--deck-amber);
		animation-delay: -1.4s;
	}

	.layer-consensus {
		--layer-color: #ff8a96;
		animation-delay: -2.1s;
	}

	.layer-infra {
		--layer-color: var(--deck-emerald);
		animation-delay: -2.8s;
	}

	.layer-index {
		color: var(--layer-color);
		font-family: var(--font-display);
		font-size: 1.15rem;
		font-weight: 900;
	}

	.stack-layer div {
		display: grid;
	}

	.stack-layer small {
		color: var(--layer-color);
		font-size: 0.56rem;
		font-weight: 900;
		letter-spacing: 0.14em;
		text-transform: uppercase;
	}

	.stack-layer strong {
		margin-top: 0.15rem;
		font-size: 0.73rem;
	}

	.stack-layer b {
		color: var(--layer-color);
		font-weight: 900;
	}

	.stack-layer :global(.layer-icon) {
		color: var(--layer-color);
	}

	.slide-architecture {
		background:
			radial-gradient(circle at 80% 14%, rgba(117, 223, 168, 0.13), transparent 28%),
			radial-gradient(circle at 18% 86%, rgba(246, 201, 107, 0.12), transparent 25%),
			#11141c;
	}

	.architecture-layout {
		position: relative;
		z-index: 2;
		display: grid;
		gap: 1.25rem;
		min-height: 100%;
	}

	.architecture-copy {
		max-width: 55rem;
	}

	.architecture-copy h2 {
		max-width: 52rem;
		margin-top: 0.8rem;
		font-family: var(--font-display);
		font-size: clamp(2.65rem, 5vw, 5.1rem);
		font-weight: 900;
		letter-spacing: -0.05em;
		line-height: 0.95;
	}

	.architecture-copy h2 em {
		color: var(--deck-emerald);
		font-style: normal;
	}

	.architecture-lead {
		margin-top: 1.45rem;
		max-width: 47rem;
		color: var(--deck-ink) !important;
		font-size: clamp(1.02rem, 1.7vw, 1.34rem) !important;
		font-weight: 800;
		line-height: 1.45 !important;
	}

	.architecture-copy > p:not(.deck-kicker):not(.architecture-lead) {
		max-width: 45rem;
		margin-top: 0.9rem;
		color: var(--deck-muted);
		font-size: 0.86rem;
		line-height: 1.7;
	}

	.finality-board {
		display: grid;
		gap: 1rem;
		align-items: stretch;
	}

	.finality-rail {
		position: relative;
		overflow: hidden;
		min-height: 15.5rem;
		border: 1px solid var(--deck-line);
		border-radius: 1.5rem;
		background:
			linear-gradient(rgba(255, 255, 255, 0.045) 1px, transparent 1px),
			linear-gradient(90deg, rgba(255, 255, 255, 0.045) 1px, transparent 1px),
			rgba(255, 255, 255, 0.04);
		background-size: 2rem 2rem;
	}

	.finality-node,
	.support-ring {
		position: absolute;
		z-index: 2;
		display: grid;
		place-items: center;
		border: 1px solid rgba(255, 255, 255, 0.2);
		box-shadow: 0 16px 45px rgba(0, 0, 0, 0.22);
		font-weight: 900;
	}

	.finality-node {
		width: 3.1rem;
		height: 3.1rem;
		border-radius: 1rem;
		background: #f4f7fb;
		color: #11141c;
		animation: nodeLift 3.2s ease-in-out infinite;
	}

	.node-a {
		top: 68%;
		left: 9%;
		color: #11141c;
	}

	.node-b {
		top: 23%;
		left: 32%;
		background: var(--deck-cyan);
		animation-delay: -0.8s;
	}

	.node-c {
		top: 55%;
		left: 54%;
		background: var(--deck-amber);
		animation-delay: -1.6s;
	}

	.node-d {
		top: 18%;
		right: 12%;
		background: var(--deck-emerald);
		animation-delay: -2.4s;
	}

	.finality-line {
		position: absolute;
		height: 2px;
		background: linear-gradient(90deg, rgba(111, 229, 236, 0.2), var(--deck-cyan));
		transform-origin: left center;
	}

	.line-one {
		top: 62%;
		left: 18%;
		width: 20%;
		rotate: -31deg;
	}

	.line-two {
		top: 39%;
		left: 40%;
		width: 22%;
		rotate: 27deg;
		background: linear-gradient(90deg, rgba(246, 201, 107, 0.2), var(--deck-amber));
	}

	.line-three {
		top: 47%;
		left: 61%;
		width: 19%;
		rotate: -24deg;
		background: linear-gradient(90deg, rgba(117, 223, 168, 0.2), var(--deck-emerald));
	}

	.support-ring {
		right: 7%;
		bottom: 12%;
		width: 7.2rem;
		height: 7.2rem;
		border-radius: 50%;
		border-color: rgba(117, 223, 168, 0.42);
		background: rgba(117, 223, 168, 0.09);
		color: var(--deck-emerald);
		font-size: 0.64rem;
		letter-spacing: 0.16em;
		text-transform: uppercase;
		animation: supportPulse 2.8s ease-in-out infinite;
	}

	.predicate-grid {
		display: grid;
		gap: 0.75rem;
	}

	.predicate-card,
	.integration-strip article {
		border: 1px solid var(--deck-line);
		border-radius: 1.1rem;
		background: rgba(255, 255, 255, 0.055);
		backdrop-filter: blur(12px);
	}

	.predicate-card {
		position: relative;
		min-height: 9rem;
		padding: 1rem;
	}

	.predicate-card span {
		color: var(--deck-emerald);
		font-family: var(--font-display);
		font-size: 1.45rem;
		font-weight: 900;
	}

	.predicate-card h3,
	.integration-strip h3 {
		margin-top: 0.45rem;
		font-size: 0.92rem;
		font-weight: 900;
	}

	.predicate-card p,
	.integration-strip p {
		margin-top: 0.45rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.55;
	}

	.predicate-card :global(.predicate-icon) {
		position: absolute;
		top: 1rem;
		right: 1rem;
		color: var(--deck-emerald);
	}

	.integration-strip {
		display: grid;
		gap: 0.8rem;
	}

	.integration-strip article {
		display: grid;
		grid-template-columns: auto 1fr;
		gap: 0.85rem;
		align-items: start;
		padding: 0.95rem 1rem;
	}

	.integration-strip :global(.integration-icon) {
		color: var(--deck-amber);
	}

	.slide-issues {
		background:
			radial-gradient(circle at 16% 20%, rgba(111, 229, 236, 0.12), transparent 27%),
			radial-gradient(circle at 84% 78%, rgba(165, 148, 255, 0.14), transparent 28%),
			#11141c;
	}

	.slide-issues-integration {
		background:
			radial-gradient(circle at 78% 14%, rgba(117, 223, 168, 0.13), transparent 28%),
			radial-gradient(circle at 18% 82%, rgba(246, 201, 107, 0.12), transparent 28%),
			#11141c;
	}

	.issue-slide-layout {
		position: relative;
		z-index: 2;
		display: grid;
		gap: 1.3rem;
		min-height: 100%;
	}

	.issue-copy {
		max-width: 58rem;
	}

	.issue-copy h2 {
		max-width: 54rem;
		margin-top: 0.8rem;
		font-family: var(--font-display);
		font-size: clamp(2.65rem, 5vw, 5.1rem);
		font-weight: 900;
		letter-spacing: -0.05em;
		line-height: 0.95;
	}

	.issue-copy h2 em {
		color: var(--deck-amber);
		font-style: normal;
	}

	.slide-issues-integration .issue-copy h2 em {
		color: var(--deck-emerald);
	}

	.issue-copy > p:not(.deck-kicker) {
		max-width: 45rem;
		margin-top: 1.1rem;
		color: var(--deck-muted);
		font-size: 0.95rem;
		line-height: 1.7;
	}

	.issue-card-grid,
	.issue-integration-grid {
		display: grid;
		gap: 0.9rem;
	}

	.issue-card {
		--issue-color: var(--deck-cyan);
		position: relative;
		overflow: hidden;
		min-height: 14rem;
		border: 1px solid color-mix(in srgb, var(--issue-color) 34%, transparent);
		border-radius: 1.25rem;
		background:
			linear-gradient(135deg, color-mix(in srgb, var(--issue-color) 14%, transparent), transparent 58%),
			rgba(255, 255, 255, 0.052);
		padding: 1.2rem;
		backdrop-filter: blur(12px);
		box-shadow: 0 24px 70px rgba(0, 0, 0, 0.18);
	}

	.issue-card::after {
		content: "";
		position: absolute;
		right: -2.8rem;
		bottom: -2.8rem;
		width: 8rem;
		height: 8rem;
		border: 1px solid color-mix(in srgb, var(--issue-color) 32%, transparent);
		border-radius: 50%;
		opacity: 0.55;
	}

	.issue-card-cyan {
		--issue-color: var(--deck-cyan);
	}

	.issue-card-violet {
		--issue-color: var(--deck-violet);
	}

	.issue-card-amber {
		--issue-color: var(--deck-amber);
	}

	.issue-card-emerald {
		--issue-color: var(--deck-emerald);
	}

	.issue-link {
		display: inline-grid;
		min-width: 3.2rem;
		height: 2rem;
		place-items: center;
		border: 1px solid color-mix(in srgb, var(--issue-color) 52%, transparent);
		border-radius: 999px;
		background: color-mix(in srgb, var(--issue-color) 13%, transparent);
		color: var(--issue-color);
		font-family: var(--font-display);
		font-size: 0.96rem;
		font-weight: 900;
		text-decoration: none;
		transition:
			transform 180ms ease,
			background 180ms ease,
			color 180ms ease;
	}

	.issue-link:hover {
		transform: translateY(-2px);
		background: var(--issue-color);
		color: #11141c;
	}

	.issue-card span {
		display: block;
		margin-top: 1.1rem;
		color: var(--issue-color);
		font-size: 0.64rem;
		font-weight: 900;
		letter-spacing: 0.17em;
		text-transform: uppercase;
	}

	.issue-card h3 {
		max-width: 20rem;
		margin-top: 0.45rem;
		font-size: clamp(1.05rem, 1.8vw, 1.45rem);
		font-weight: 900;
		line-height: 1.08;
	}

	.issue-card p {
		position: relative;
		z-index: 1;
		margin-top: 0.85rem;
		color: var(--deck-muted);
		font-size: 0.76rem;
		line-height: 1.62;
	}

	.issue-card code {
		color: var(--deck-ink);
		font-size: 0.72rem;
	}

	.planning-map {
		display: grid;
		gap: 0.65rem;
		border: 1px solid var(--deck-line);
		border-radius: 1.25rem;
		background: rgba(255, 255, 255, 0.05);
		padding: 0.9rem;
	}

	.planning-step {
		position: relative;
		display: flex;
		align-items: center;
		min-height: 2.65rem;
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 999px;
		background: rgba(255, 255, 255, 0.055);
		padding: 0.72rem 0.95rem;
		color: var(--deck-ink);
		font-size: 0.66rem;
		font-weight: 900;
		letter-spacing: 0.13em;
		text-transform: uppercase;
	}

	.planning-step::before {
		content: "";
		width: 0.5rem;
		height: 0.5rem;
		margin-right: 0.7rem;
		border-radius: 50%;
		background: var(--deck-emerald);
		box-shadow: 0 0 16px rgba(117, 223, 168, 0.7);
	}

	.slide-refactor {
		background:
			radial-gradient(circle at 16% 14%, rgba(111, 229, 236, 0.13), transparent 28%),
			radial-gradient(circle at 88% 80%, rgba(246, 201, 107, 0.13), transparent 28%),
			#11141c;
	}

	.refactor-layout {
		position: relative;
		z-index: 2;
		display: grid;
		gap: 1rem;
		min-height: 100%;
	}

	.refactor-copy {
		max-width: 65rem;
	}

	.refactor-copy h2 {
		max-width: 58rem;
		margin-top: 0.8rem;
		font-family: var(--font-display);
		font-size: clamp(2.65rem, 5vw, 5.1rem);
		font-weight: 900;
		letter-spacing: -0.05em;
		line-height: 0.95;
	}

	.refactor-copy h2 em {
		color: var(--deck-cyan);
		font-style: normal;
	}

	.refactor-copy p {
		max-width: 58rem;
		margin-top: 1rem;
		color: var(--deck-muted);
		font-size: 0.9rem;
		line-height: 1.7;
	}

	.refactor-copy a {
		color: var(--deck-cyan);
		font-weight: 900;
		text-decoration: none;
	}

	.refactor-copy a:hover {
		text-decoration: underline;
	}

	.refactor-grid {
		display: grid;
		gap: 0.85rem;
	}

	.refactor-card {
		--refactor-color: var(--deck-cyan);
		position: relative;
		overflow: hidden;
		min-height: 11.5rem;
		border: 1px solid color-mix(in srgb, var(--refactor-color) 34%, transparent);
		border-radius: 1.2rem;
		background:
			linear-gradient(140deg, color-mix(in srgb, var(--refactor-color) 12%, transparent), transparent 58%),
			rgba(255, 255, 255, 0.052);
		padding: 1rem;
		backdrop-filter: blur(12px);
	}

	.refactor-core {
		--refactor-color: var(--deck-cyan);
	}

	.refactor-traits {
		--refactor-color: var(--deck-emerald);
	}

	.refactor-adapter {
		--refactor-color: var(--deck-amber);
	}

	.refactor-card span {
		color: var(--refactor-color);
		font-family: var(--font-display);
		font-size: 1.35rem;
		font-weight: 900;
	}

	.refactor-card h3 {
		margin-top: 0.45rem;
		font-size: 1.08rem;
		font-weight: 900;
	}

	.refactor-card p {
		margin-top: 0.65rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.56;
	}

	.refactor-card code,
	.factory-code code,
	.workspace-strip code {
		color: var(--deck-ink);
		font-size: 0.7rem;
	}

	.factory-panel {
		display: grid;
		gap: 0.7rem;
		align-items: center;
		border: 1px solid var(--deck-line);
		border-radius: 1.2rem;
		background: rgba(255, 255, 255, 0.055);
		padding: 0.95rem;
	}

	.factory-mode,
	.factory-code {
		display: grid;
		gap: 0.3rem;
	}

	.factory-mode small,
	.factory-code small,
	.workspace-strip span {
		color: var(--deck-muted);
		font-size: 0.58rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.factory-mode {
		grid-template-columns: auto auto auto;
		align-items: end;
		width: fit-content;
		column-gap: 0.55rem;
	}

	.factory-mode small {
		grid-column: 1 / -1;
	}

	.factory-mode strong {
		color: var(--deck-ink);
		font-family: var(--font-display);
		font-size: 1.25rem;
		font-weight: 900;
	}

	.factory-mode span {
		color: var(--deck-muted);
		font-size: 0.72rem;
		font-weight: 900;
		text-transform: uppercase;
	}

	.factory-arrow {
		display: grid;
		width: 2.4rem;
		height: 2.4rem;
		place-items: center;
		border-radius: 50%;
		background: var(--deck-cyan);
		color: #11141c;
	}

	.workspace-strip {
		display: flex;
		flex-wrap: wrap;
		gap: 0.55rem;
		align-items: center;
		border: 1px solid rgba(111, 229, 236, 0.2);
		border-radius: 999px;
		background: rgba(111, 229, 236, 0.07);
		padding: 0.75rem 0.9rem;
	}

	.workspace-strip code {
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 999px;
		background: rgba(255, 255, 255, 0.06);
		padding: 0.38rem 0.55rem;
	}

	.slide-review {
		background:
			radial-gradient(circle at 16% 14%, rgba(111, 229, 236, 0.12), transparent 28%),
			radial-gradient(circle at 82% 82%, rgba(165, 148, 255, 0.12), transparent 28%),
			#11141c;
	}

	.slide-crypto,
	.slide-ratification-math {
		background:
			radial-gradient(circle at 82% 12%, rgba(117, 223, 168, 0.13), transparent 28%),
			radial-gradient(circle at 16% 82%, rgba(246, 201, 107, 0.12), transparent 28%),
			#11141c;
	}

	.slide-safety,
	.slide-approval-math {
		background:
			radial-gradient(circle at 14% 18%, rgba(246, 201, 107, 0.13), transparent 28%),
			radial-gradient(circle at 88% 76%, rgba(111, 229, 236, 0.12), transparent 28%),
			#11141c;
	}

	.slide-weighted {
		background:
			radial-gradient(circle at 14% 20%, rgba(117, 223, 168, 0.14), transparent 28%),
			radial-gradient(circle at 86% 78%, rgba(111, 229, 236, 0.12), transparent 28%),
			#11141c;
	}

	.slide-weighted-detail {
		background:
			radial-gradient(circle at 82% 14%, rgba(246, 201, 107, 0.13), transparent 28%),
			radial-gradient(circle at 16% 82%, rgba(117, 223, 168, 0.12), transparent 28%),
			#11141c;
	}

	.slide-checkpoint {
		background:
			radial-gradient(circle at 18% 16%, rgba(165, 148, 255, 0.13), transparent 28%),
			radial-gradient(circle at 84% 80%, rgba(111, 229, 236, 0.12), transparent 28%),
			#11141c;
	}

	.slide-lmdb {
		background:
			radial-gradient(circle at 16% 16%, rgba(111, 229, 236, 0.13), transparent 28%),
			radial-gradient(circle at 86% 82%, rgba(117, 223, 168, 0.12), transparent 28%),
			#11141c;
	}

	.slide-lmdb-recovery {
		background:
			radial-gradient(circle at 80% 12%, rgba(246, 201, 107, 0.13), transparent 28%),
			radial-gradient(circle at 14% 84%, rgba(111, 229, 236, 0.12), transparent 28%),
			#11141c;
	}

	.slide-slashing {
		background:
			radial-gradient(circle at 16% 16%, rgba(255, 126, 140, 0.13), transparent 28%),
			radial-gradient(circle at 86% 82%, rgba(165, 148, 255, 0.13), transparent 28%),
			#11141c;
	}

	.review-layout,
	.safety-layout,
	.math-layout,
	.weighted-layout,
	.weighted-detail-layout,
	.checkpoint-layout,
	.lmdb-layout,
	.lmdb-recovery-layout,
	.slashing-layout {
		position: relative;
		z-index: 2;
		display: grid;
		gap: 1rem;
		min-height: 100%;
	}

	.review-copy {
		max-width: 64rem;
	}

	.review-copy h2 {
		max-width: 58rem;
		margin-top: 0.8rem;
		font-family: var(--font-display);
		font-size: clamp(2.55rem, 4.8vw, 4.85rem);
		font-weight: 900;
		letter-spacing: -0.05em;
		line-height: 0.96;
	}

	.review-copy h2 em {
		color: var(--deck-cyan);
		font-style: normal;
	}

	.slide-crypto .review-copy h2 em,
	.slide-ratification-math .review-copy h2 em {
		color: var(--deck-emerald);
	}

	.slide-safety .review-copy h2 em,
	.slide-approval-math .review-copy h2 em {
		color: var(--deck-amber);
	}

	.review-copy p {
		max-width: 56rem;
		margin-top: 1rem;
		color: var(--deck-muted);
		font-size: 0.89rem;
		line-height: 1.68;
	}

	.review-copy a {
		color: var(--deck-cyan);
		font-weight: 900;
		text-decoration: none;
	}

	.review-copy a:hover {
		text-decoration: underline;
	}

	.pipeline-board,
	.review-card,
	.crypto-flow,
	.synchrony-panel,
	.ladder-grid article,
	.formula-panel,
	.math-card-row article,
	.finality-summary article {
		border: 1px solid var(--deck-line);
		border-radius: 1.2rem;
		background: rgba(255, 255, 255, 0.055);
		backdrop-filter: blur(12px);
	}

	.pipeline-board {
		display: grid;
		gap: 0.55rem;
		align-items: center;
		padding: 0.85rem;
	}

	.pipeline-step {
		display: grid;
		min-height: 3rem;
		place-items: center;
		border: 1px solid rgba(111, 229, 236, 0.24);
		border-radius: 999px;
		background: rgba(111, 229, 236, 0.08);
		color: var(--deck-ink);
		font-size: 0.64rem;
		font-weight: 900;
		letter-spacing: 0.12em;
		text-align: center;
		text-transform: uppercase;
	}

	.pipeline-arrow {
		display: grid;
		place-items: center;
		color: var(--deck-cyan);
	}

	.review-grid,
	.math-card-row,
	.finality-summary,
	.ladder-grid {
		display: grid;
		gap: 0.85rem;
	}

	.review-card {
		min-height: 10rem;
		padding: 1rem;
	}

	.review-card span,
	.ladder-grid span,
	.formula-panel span {
		color: var(--deck-cyan);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.review-card h3,
	.synchrony-panel h3,
	.ladder-grid h3,
	.math-card-row h3,
	.finality-summary h3 {
		margin-top: 0.45rem;
		font-size: 1rem;
		font-weight: 900;
	}

	.review-card p,
	.synchrony-panel p,
	.ladder-grid p,
	.math-card-row p,
	.finality-summary p {
		margin-top: 0.55rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.58;
	}

	.review-card code,
	.crypto-flow code,
	.formula-panel code {
		color: var(--deck-ink);
		font-size: 0.7rem;
	}

	.crypto-flow {
		display: grid;
		gap: 0.55rem;
		padding: 1rem;
	}

	.crypto-flow div {
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 0.75rem;
		border: 1px solid rgba(117, 223, 168, 0.2);
		border-radius: 0.9rem;
		background: rgba(117, 223, 168, 0.07);
		padding: 0.75rem 0.85rem;
	}

	.crypto-flow strong {
		color: var(--deck-emerald);
		font-family: var(--font-display);
		font-size: 1rem;
		font-weight: 900;
	}

	.crypto-flow span {
		color: var(--deck-muted);
		font-size: 0.62rem;
		font-weight: 900;
		text-transform: uppercase;
	}

	.synchrony-panel {
		padding: 1.1rem;
	}

	.ladder-grid article,
	.math-card-row article,
	.finality-summary article {
		padding: 1rem;
	}

	.ladder-grid span {
		color: var(--deck-amber);
		font-family: var(--font-display);
		font-size: 1.5rem;
		letter-spacing: 0;
	}

	.formula-panel {
		display: grid;
		gap: 0.85rem;
		padding: 1rem;
	}

	.formula-panel div {
		display: grid;
		gap: 0.45rem;
	}

	.formula-panel code {
		display: block;
		overflow-wrap: anywhere;
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 0.95rem;
		background: rgba(255, 255, 255, 0.06);
		padding: 0.9rem;
		line-height: 1.55;
	}

	.formula-panel-vertical span {
		color: var(--deck-emerald);
	}

	.weight-contrast,
	.weighted-functions,
	.weighted-detail-grid {
		display: grid;
		gap: 0.85rem;
	}

	.weight-contrast article,
	.weighted-functions article,
	.weighted-detail-grid article,
	.weighted-principle,
	.threshold-panel,
	.bond-flow {
		border: 1px solid var(--deck-line);
		border-radius: 1.2rem;
		background: rgba(255, 255, 255, 0.055);
		backdrop-filter: blur(12px);
	}

	.weight-contrast article,
	.weighted-functions article,
	.weighted-detail-grid article {
		padding: 1rem;
	}

	.weight-contrast span,
	.threshold-panel span {
		color: var(--deck-emerald);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.weight-contrast h3,
	.weighted-detail-grid h3 {
		margin-top: 0.45rem;
		font-size: 1rem;
		font-weight: 900;
	}

	.weight-contrast p,
	.weighted-principle p,
	.weighted-functions p,
	.weighted-detail-grid p {
		margin-top: 0.55rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.58;
	}

	.weighted-principle {
		display: grid;
		grid-template-columns: auto 1fr;
		gap: 0.8rem;
		align-items: center;
		padding: 0.9rem 1rem;
	}

	.weighted-principle :global(svg) {
		color: var(--deck-emerald);
	}

	.weighted-functions code,
	.threshold-panel code,
	.weighted-detail-grid code {
		color: var(--deck-ink);
		font-size: 0.7rem;
	}

	.weighted-functions article {
		min-height: 7.5rem;
	}

	.threshold-panel {
		display: grid;
		gap: 0.85rem;
		padding: 1rem;
	}

	.threshold-panel div {
		display: grid;
		gap: 0.45rem;
	}

	.threshold-panel code {
		display: block;
		overflow-wrap: anywhere;
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 0.95rem;
		background: rgba(255, 255, 255, 0.06);
		padding: 0.85rem;
		line-height: 1.55;
	}

	.bond-flow {
		display: grid;
		gap: 0.55rem;
		align-items: center;
		padding: 0.85rem;
	}

	.bond-flow div {
		display: grid;
		min-height: 2.8rem;
		place-items: center;
		border: 1px solid rgba(246, 201, 107, 0.22);
		border-radius: 999px;
		background: rgba(246, 201, 107, 0.08);
		color: var(--deck-ink);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.11em;
		text-align: center;
		text-transform: uppercase;
	}

	.bond-flow :global(svg) {
		justify-self: center;
		color: var(--deck-amber);
	}

	.checkpoint-diagram,
	.checkpoint-grid article,
	.checkpoint-flow {
		border: 1px solid var(--deck-line);
		border-radius: 1.2rem;
		background: rgba(255, 255, 255, 0.055);
		backdrop-filter: blur(12px);
	}

	.checkpoint-diagram {
		display: grid;
		gap: 0.7rem;
		padding: 1rem;
	}

	.checkpoint-row {
		display: grid;
		gap: 0.55rem;
		align-items: center;
	}

	.checkpoint-row span,
	.checkpoint-grid span {
		color: var(--deck-violet);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.checkpoint-row div,
	.checkpoint-row strong {
		display: grid;
		min-height: 2.7rem;
		place-items: center;
		border: 1px solid rgba(165, 148, 255, 0.24);
		border-radius: 999px;
		background: rgba(165, 148, 255, 0.08);
		color: var(--deck-ink);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.1em;
		text-align: center;
		text-transform: uppercase;
	}

	.checkpoint-row strong {
		border-color: rgba(246, 201, 107, 0.26);
		background: rgba(246, 201, 107, 0.08);
		color: var(--deck-amber);
	}

	.checkpoint-row :global(svg) {
		justify-self: center;
		color: var(--deck-violet);
	}

	.checkpoint-grid {
		display: grid;
		gap: 0.85rem;
	}

	.checkpoint-grid article {
		padding: 1rem;
	}

	.checkpoint-grid h3 {
		margin-top: 0.45rem;
		font-size: 1rem;
		font-weight: 900;
	}

	.checkpoint-grid p {
		margin-top: 0.55rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.58;
	}

	.checkpoint-grid code,
	.checkpoint-flow code {
		color: var(--deck-ink);
		font-size: 0.7rem;
	}

	.checkpoint-flow {
		display: grid;
		gap: 0.55rem;
		align-items: center;
		padding: 0.85rem;
	}

	.checkpoint-flow code {
		display: grid;
		min-height: 2.65rem;
		place-items: center;
		border: 1px solid rgba(111, 229, 236, 0.22);
		border-radius: 999px;
		background: rgba(111, 229, 236, 0.08);
		text-align: center;
	}

	.checkpoint-flow :global(svg) {
		justify-self: center;
		color: var(--deck-cyan);
	}

	.lmdb-flow,
	.recovery-flow,
	.lmdb-grid article,
	.recovery-grid article,
	.lmdb-note,
	.recovery-invariants {
		border: 1px solid var(--deck-line);
		border-radius: 1.2rem;
		background: rgba(255, 255, 255, 0.055);
		backdrop-filter: blur(12px);
	}

	.lmdb-flow,
	.recovery-flow {
		display: grid;
		gap: 0.55rem;
		align-items: center;
		padding: 0.85rem;
	}

	.lmdb-flow div,
	.recovery-flow div {
		display: grid;
		min-height: 2.8rem;
		place-items: center;
		border: 1px solid rgba(111, 229, 236, 0.22);
		border-radius: 999px;
		background: rgba(111, 229, 236, 0.08);
		color: var(--deck-ink);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.11em;
		text-align: center;
		text-transform: uppercase;
	}

	.recovery-flow div {
		border-color: rgba(246, 201, 107, 0.24);
		background: rgba(246, 201, 107, 0.08);
	}

	.lmdb-flow :global(svg),
	.recovery-flow :global(svg) {
		justify-self: center;
		color: var(--deck-cyan);
	}

	.recovery-flow :global(svg) {
		color: var(--deck-amber);
	}

	.lmdb-grid,
	.recovery-grid {
		display: grid;
		gap: 0.85rem;
	}

	.lmdb-grid article,
	.recovery-grid article {
		padding: 1rem;
	}

	.lmdb-grid span {
		color: var(--deck-cyan);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.lmdb-grid h3,
	.recovery-grid h3 {
		margin-top: 0.45rem;
		font-size: 1rem;
		font-weight: 900;
	}

	.lmdb-grid p,
	.recovery-grid p,
	.lmdb-note p {
		margin-top: 0.55rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.58;
	}

	.lmdb-grid code,
	.recovery-grid code,
	.lmdb-note code,
	.recovery-invariants code {
		color: var(--deck-ink);
		font-size: 0.7rem;
	}

	.lmdb-note {
		display: grid;
		grid-template-columns: auto 1fr;
		gap: 0.8rem;
		align-items: center;
		padding: 0.9rem 1rem;
	}

	.lmdb-note :global(svg) {
		color: var(--deck-emerald);
	}

	.recovery-invariants {
		display: grid;
		gap: 0.55rem;
		padding: 0.85rem;
	}

	.recovery-invariants code {
		display: block;
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 0.9rem;
		background: rgba(255, 255, 255, 0.06);
		padding: 0.7rem 0.8rem;
		line-height: 1.45;
	}

	.slashing-map,
	.module-strip,
	.slashing-flow,
	.docker-flow,
	.docker-grid,
	.docker-note,
	.f1r3node-track-grid,
	.f1r3node-flow,
	.f1r3node-boundary,
	.f1r3node-note,
	.f1r3node-next-plan {
		display: grid;
		gap: 0.85rem;
	}

	.slashing-map article,
	.module-strip,
	.slashing-flow,
	.docker-flow,
	.docker-grid article,
	.docker-note,
	.f1r3node-track-grid article,
	.f1r3node-flow,
	.f1r3node-boundary article,
	.f1r3node-note,
	.f1r3node-next-plan {
		border: 1px solid var(--deck-line);
		border-radius: 1.2rem;
		background: rgba(255, 255, 255, 0.055);
		backdrop-filter: blur(12px);
	}

	.slashing-map article {
		padding: 1rem;
	}

	.slashing-map span {
		color: #ff7e8c;
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.slashing-map h3 {
		margin-top: 0.45rem;
		font-size: 1rem;
		font-weight: 900;
	}

	.slashing-map p {
		margin-top: 0.55rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.58;
	}

	.slashing-map code,
	.module-strip code {
		color: var(--deck-ink);
		font-size: 0.7rem;
	}

	.module-strip {
		grid-template-columns: repeat(auto-fit, minmax(8rem, 1fr));
		padding: 0.85rem;
	}

	.module-strip code {
		display: grid;
		min-height: 2.4rem;
		place-items: center;
		border: 1px solid rgba(255, 126, 140, 0.22);
		border-radius: 999px;
		background: rgba(255, 126, 140, 0.08);
		text-align: center;
	}

	.slashing-flow {
		align-items: center;
		padding: 0.85rem;
	}

	.slashing-flow div {
		display: grid;
		min-height: 2.8rem;
		place-items: center;
		border: 1px solid rgba(165, 148, 255, 0.24);
		border-radius: 999px;
		background: rgba(165, 148, 255, 0.08);
		color: var(--deck-ink);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.11em;
		text-align: center;
		text-transform: uppercase;
	}

	.slashing-flow :global(svg) {
		justify-self: center;
		color: #ff7e8c;
	}

	.docker-flow {
		align-items: center;
		padding: 0.85rem;
	}

	.docker-flow div {
		display: grid;
		min-height: 2.8rem;
		place-items: center;
		border: 1px solid rgba(111, 229, 236, 0.22);
		border-radius: 999px;
		background: rgba(111, 229, 236, 0.08);
		color: var(--deck-ink);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.11em;
		text-align: center;
		text-transform: uppercase;
	}

	.docker-flow :global(svg) {
		justify-self: center;
		color: var(--deck-cyan);
	}

	.docker-grid article {
		padding: 1rem;
	}

	.docker-grid span {
		color: var(--deck-cyan);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.docker-grid h3 {
		margin-top: 0.45rem;
		font-size: 1rem;
		font-weight: 900;
	}

	.docker-grid p,
	.docker-note p {
		margin-top: 0.55rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.58;
	}

	.docker-grid code {
		color: var(--deck-ink);
		font-size: 0.7rem;
	}

	.docker-note {
		grid-template-columns: auto 1fr;
		gap: 0.8rem;
		align-items: center;
		padding: 0.9rem 1rem;
	}

	.docker-note :global(svg) {
		color: var(--deck-cyan);
	}

	.f1r3node-track-grid article,
	.f1r3node-boundary article {
		padding: 1rem;
	}

	.f1r3node-track-grid span,
	.f1r3node-boundary span {
		color: var(--deck-emerald);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.f1r3node-boundary span {
		color: var(--deck-amber);
	}

	.f1r3node-track-grid h3,
	.f1r3node-boundary h3 {
		margin-top: 0.45rem;
		font-size: 1rem;
		font-weight: 900;
	}

	.f1r3node-track-grid p,
	.f1r3node-boundary p,
	.f1r3node-note p {
		margin-top: 0.55rem;
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.58;
	}

	.f1r3node-track-grid code,
	.f1r3node-boundary code {
		color: var(--deck-ink);
		font-size: 0.7rem;
	}

	.f1r3node-flow {
		align-items: center;
		padding: 0.85rem;
	}

	.f1r3node-flow div {
		display: grid;
		min-height: 2.8rem;
		place-items: center;
		border: 1px solid rgba(117, 223, 168, 0.22);
		border-radius: 999px;
		background: rgba(117, 223, 168, 0.08);
		color: var(--deck-ink);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.11em;
		text-align: center;
		text-transform: uppercase;
	}

	.f1r3node-flow :global(svg) {
		justify-self: center;
		color: var(--deck-emerald);
	}

	.f1r3node-note {
		grid-template-columns: auto 1fr;
		gap: 0.8rem;
		align-items: center;
		padding: 0.9rem 1rem;
	}

	.f1r3node-note :global(svg) {
		color: var(--deck-emerald);
	}

	.f1r3node-next-plan {
		padding: 1rem;
	}

	.f1r3node-next-plan-head {
		display: grid;
		gap: 0.35rem;
	}

	.f1r3node-next-plan-head span {
		color: var(--deck-cyan);
		font-size: 0.62rem;
		font-weight: 900;
		letter-spacing: 0.16em;
		text-transform: uppercase;
	}

	.f1r3node-next-plan-head h3 {
		font-size: 1rem;
		font-weight: 900;
	}

	.f1r3node-next-plan ol {
		display: grid;
		gap: 0.6rem;
		padding-left: 1.2rem;
	}

	.f1r3node-next-plan li {
		color: var(--deck-muted);
		font-size: 0.72rem;
		line-height: 1.58;
	}

	.deck-controls {
		position: absolute;
		right: 1.2rem;
		bottom: 1.2rem;
		left: 1.2rem;
		z-index: 25;
		display: flex;
		align-items: center;
		gap: 0.8rem;
		border: 1px solid var(--deck-line);
		border-radius: 999px;
		background: rgba(17, 20, 28, 0.82);
		padding: 0.45rem;
		backdrop-filter: blur(18px);
	}

	.deck-controls > button,
	.deck-icon-button {
		display: grid;
		width: 2.35rem;
		height: 2.35rem;
		place-items: center;
		border: 1px solid rgba(255, 255, 255, 0.1);
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.06);
		color: var(--deck-ink);
		transition:
			transform 180ms ease,
			background 180ms ease,
			opacity 180ms ease;
	}

	.deck-icon-button {
		border-color: color-mix(in srgb, var(--foreground) 12%, transparent);
		background: color-mix(in srgb, var(--background) 78%, transparent);
		color: var(--foreground);
	}

	.deck-controls > button:hover:not(:disabled),
	.deck-icon-button:hover {
		transform: translateY(-2px);
		background: var(--deck-cyan);
		color: #11141c;
	}

	.deck-controls > button:disabled {
		cursor: not-allowed;
		opacity: 0.28;
	}

	.deck-dots {
		display: flex;
		gap: 0.4rem;
	}

	.deck-dots button {
		width: 0.5rem;
		height: 0.5rem;
		border-radius: 999px;
		background: rgba(255, 255, 255, 0.22);
		transition:
			width 260ms ease,
			background 260ms ease;
	}

	.deck-dots button.active {
		width: 1.8rem;
		background: var(--deck-cyan);
	}

	.deck-counter {
		margin-left: auto;
		color: var(--deck-muted);
		font-size: 0.65rem;
		font-weight: 900;
		letter-spacing: 0.15em;
	}

	@media (min-width: 900px) {
		.slide-heading {
			grid-template-columns: 1.15fr 0.85fr;
		}

		.protocol-layout {
			grid-template-columns: 1.18fr 0.82fr;
		}

		.stack-layout {
			grid-template-columns: 0.95fr 1.05fr;
		}

		.architecture-layout {
			grid-template-columns: 0.9fr 1.1fr;
			grid-template-rows: auto 1fr;
			align-items: end;
		}

		.architecture-copy {
			grid-row: 1 / 3;
			align-self: center;
		}

		.predicate-grid {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.issue-slide-layout {
			grid-template-rows: auto 1fr;
			align-content: center;
		}

		.issue-card-grid {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.issue-slide-layout-wide {
			grid-template-columns: 0.9fr 1.1fr;
			grid-template-rows: auto 1fr;
			align-items: end;
		}

		.issue-slide-layout-wide .issue-copy {
			grid-row: 1 / 3;
			align-self: center;
		}

		.issue-integration-grid {
			grid-template-columns: repeat(2, minmax(0, 1fr));
		}

		.planning-map {
			grid-column: 2;
			grid-template-columns: repeat(5, minmax(0, 1fr));
		}

		.planning-step {
			justify-content: center;
			text-align: center;
		}

		.refactor-layout {
			grid-template-rows: auto auto auto auto;
			align-content: center;
		}

		.refactor-grid {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.factory-panel {
			grid-template-columns: auto auto 1fr;
		}

		.review-layout,
		.safety-layout,
		.math-layout,
		.weighted-layout,
		.weighted-detail-layout {
			grid-template-rows: auto auto 1fr;
			align-content: center;
		}

		.checkpoint-layout {
			grid-template-rows: auto auto 1fr auto;
			align-content: center;
		}

		.lmdb-layout,
		.lmdb-recovery-layout {
			grid-template-rows: auto auto 1fr auto;
			align-content: center;
		}

		.slashing-layout {
			grid-template-rows: auto 1fr auto auto;
			align-content: center;
		}

		.review-layout-split,
		.math-layout-wide {
			grid-template-columns: 0.9fr 1.1fr;
			grid-template-rows: auto 1fr;
			align-items: end;
		}

		.review-layout-split .review-copy,
		.math-layout-wide .review-copy {
			grid-row: 1 / 3;
			align-self: center;
		}

		.pipeline-board {
			grid-template-columns: 1fr auto 1fr auto 1.15fr auto 1fr auto 1.25fr;
		}

		.review-grid,
		.ladder-grid,
		.finality-summary {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.review-grid-two,
		.math-card-row {
			grid-template-columns: repeat(2, minmax(0, 1fr));
		}

		.review-layout-split .review-grid-two,
		.math-layout-wide .finality-summary {
			grid-column: 2;
		}

		.weight-contrast,
		.weighted-detail-grid {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.weight-contrast article:first-child {
			grid-column: span 1;
		}

		.weight-contrast article:last-child {
			grid-column: span 2;
		}

		.weighted-functions {
			grid-template-columns: repeat(4, minmax(0, 1fr));
		}

		.threshold-panel {
			grid-template-columns: repeat(2, minmax(0, 1fr));
		}

		.bond-flow {
			grid-template-columns: 1fr auto 1fr auto 1fr auto 1fr auto 1fr;
		}

		.checkpoint-row {
			grid-template-columns: 0.42fr 1fr auto 1.2fr auto 1.1fr;
		}

		.checkpoint-row-after {
			grid-template-columns: 0.42fr 1.15fr auto 1.1fr 1fr;
		}

		.checkpoint-grid {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.checkpoint-flow {
			grid-template-columns: 1fr auto 1.25fr auto 1.2fr auto 1.25fr;
		}

		.lmdb-flow,
		.recovery-flow {
			grid-template-columns: 1fr auto 1fr auto 1fr auto 1fr auto 1fr;
		}

		.lmdb-grid,
		.recovery-grid {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.recovery-invariants {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.slashing-map {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.slashing-flow {
			grid-template-columns: 1fr auto 1fr auto 1.15fr auto 1.15fr;
		}

		.docker-flow {
			grid-template-columns: 1fr auto 1fr auto 1fr auto 1fr auto 1.15fr;
		}

		.docker-grid {
			grid-template-columns: repeat(3, minmax(0, 1fr));
		}

		.f1r3node-track-grid,
		.f1r3node-boundary,
		.f1r3node-next-plan {
			grid-template-columns: repeat(2, minmax(0, 1fr));
		}

		.f1r3node-flow {
			grid-template-columns: 1.1fr auto 1.15fr auto 1fr auto 1.25fr;
		}
	}

	@media (max-width: 899px) {
		.deck,
		.deck-track,
		.slide {
			min-height: 48rem;
		}

		.slide {
			overflow-y: auto;
			padding: 2rem 1.2rem 6rem;
		}

		.cover-copy h1 {
			font-size: clamp(3.2rem, 15vw, 5.6rem);
		}

		.cover-orbit {
			opacity: 0.5;
		}

		.shard-sky {
			display: none;
		}

		.stack-layout {
			padding-top: 0;
		}

		.finality-rail {
			min-height: 13rem;
		}

		.issue-card {
			min-height: 11rem;
		}

		.factory-arrow {
			rotate: 90deg;
		}

		.pipeline-board {
			grid-template-columns: 1fr;
		}

		.pipeline-arrow {
			rotate: 90deg;
		}

		.bond-flow {
			grid-template-columns: 1fr;
		}

		.bond-flow :global(svg) {
			rotate: 90deg;
		}

		.checkpoint-row,
		.checkpoint-row-after,
		.checkpoint-flow {
			grid-template-columns: 1fr;
		}

		.checkpoint-row :global(svg),
		.checkpoint-flow :global(svg) {
			rotate: 90deg;
		}

		.lmdb-flow,
		.recovery-flow {
			grid-template-columns: 1fr;
		}

		.lmdb-flow :global(svg),
		.recovery-flow :global(svg) {
			rotate: 90deg;
		}

		.slashing-flow {
			grid-template-columns: 1fr;
		}

		.slashing-flow :global(svg) {
			rotate: 90deg;
		}

		.docker-flow {
			grid-template-columns: 1fr;
		}

		.docker-flow :global(svg) {
			rotate: 90deg;
		}

		.f1r3node-flow {
			grid-template-columns: 1fr;
		}

		.f1r3node-flow :global(svg) {
			rotate: 90deg;
		}

		.review-card {
			min-height: auto;
		}
	}

	@media (max-width: 600px) {
		.deck {
			border-radius: 1.4rem;
		}

		.slide-cover {
			justify-content: center;
			gap: 4rem;
		}

		.cover-footer {
			align-items: start;
			flex-direction: column;
		}

		.cover-mark {
			display: none;
		}

		.slide-heading h2,
		.stack-copy h2,
		.architecture-copy h2,
		.issue-copy h2,
		.refactor-copy h2,
		.review-copy h2 {
			font-size: 2.55rem;
		}

		.blocklace-panel {
			min-height: 21rem;
		}

		.blocklace-svg {
			min-height: 20rem;
			padding-right: 0;
			padding-left: 0;
		}

		.blocklace-note {
			display: none;
		}

		.pillar {
			grid-template-columns: auto 1fr;
		}

		.pillar :global(.pillar-icon) {
			display: none;
		}

		.support-ring {
			width: 5.8rem;
			height: 5.8rem;
		}

		.integration-strip article {
			grid-template-columns: 1fr;
		}

		.issue-card {
			min-height: 10rem;
		}

		.workspace-strip {
			border-radius: 1.1rem;
		}
	}

	@keyframes coverOrbit {
		to {
			rotate: 360deg;
		}
	}

	@keyframes markFloat {
		50% {
			transform: translateY(-8px) rotate(3deg);
		}
	}

	@keyframes pathMove {
		to {
			stroke-dashoffset: -32;
		}
	}

	@keyframes dagPulse {
		50% {
			scale: 1.18;
			filter: brightness(1.2);
		}
	}

	@keyframes equivocationPulse {
		50% {
			scale: 1.28;
			filter: drop-shadow(0 0 9px #ff7e8c);
		}
	}

	@keyframes tauPulse {
		50% {
			filter: drop-shadow(0 0 12px var(--deck-amber));
			scale: 1.08;
		}
	}

	@keyframes pillarGlow {
		0%,
		22% {
			border-color: color-mix(in srgb, var(--pillar-color) 48%, transparent);
			background: color-mix(in srgb, var(--pillar-color) 10%, transparent);
			transform: translateX(-4px);
		}
		34%,
		100% {
			border-color: var(--deck-line);
			background: rgba(255, 255, 255, 0.04);
			transform: translateX(0);
		}
	}

	@keyframes shardFloat {
		50% {
			transform: translateY(-8px);
		}
	}

	@keyframes hubFloat {
		50% {
			transform: translateY(-6px) rotate(-8deg);
		}
	}

	@keyframes stackRise {
		50% {
			transform: rotateX(0deg) translateY(-4px);
			border-color: color-mix(in srgb, var(--layer-color) 55%, transparent);
		}
	}

	@keyframes nodeLift {
		50% {
			transform: translateY(-6px);
			filter: brightness(1.12);
		}
	}

	@keyframes supportPulse {
		50% {
			box-shadow: 0 0 28px rgba(117, 223, 168, 0.26);
			scale: 1.04;
		}
	}

	@media (prefers-reduced-motion: reduce) {
		.deck-track,
		.cover-orbit,
		.cover-mark,
		.dag-paths path,
		.dag-node,
		.node-equivocation,
		.tau-node,
		.pillar,
		.shard-sky::before,
		.shard-sky::after,
		.shard-node,
		.shard-hub,
		.stack-layer,
		.finality-node,
		.support-ring {
			animation: none;
			transition-duration: 1ms;
		}
	}
</style>
