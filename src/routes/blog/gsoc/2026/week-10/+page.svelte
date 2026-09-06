<script lang="ts">
	import { base } from "$app/paths";
	import ZettelLink from "$lib/components/ZettelLink.svelte";

	const wikiLinks = [
		{
			label: "SPARQL",
			slug: "sparql",
			href: "https://www.w3.org/TR/sparql11-query/"
		},
		{
			label: "DBpedia SPARQL endpoint",
			slug: "dbpedia-sparql",
			href: "https://dbpedia.org/sparql"
		},
		{
			label: "Libya Wikipedia (am)",
			slug: "libya-article",
			href: "https://am.wikipedia.org/wiki/ሊቢያ"
		},
		{
			label: "DBpedia Ontology",
			slug: "dbpedia-ontology",
			href: "https://dbpedia.org/ontology/"
		},
		{
			label: "kgproxy",
			slug: "kgproxy",
			href: "https://github.com/Nama21yo/kgproxy"
		}
	];

	const sparqlQueries = [
		{
			title: "Count Amharic entities by DBpedia class",
			description:
				"This was the first diagnostic query — establishing how many entities of each type the Amharic DBpedia currently knows about. The results showed that Person and Place entities dominated, while Organisation and Work entities were sparse.",
			code: `PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT ?class (COUNT(DISTINCT ?entity) AS ?count)
WHERE {
  ?entity rdf:type ?class ;
          rdfs:label ?label .
  FILTER(LANG(?label) = "am")
  FILTER(STRSTARTS(STR(?class), STR(dbo:)))
}
GROUP BY ?class
ORDER BY DESC(?count)
LIMIT 20`
		},
		{
			title: "Fetch Amharic entity properties and values",
			description:
				"Used to pull a sample of property–value pairs for Amharic-labelled entities so we could verify which DBpedia ontology properties were actually populated versus which ones existed in the mapping definitions but had no data.",
			code: `PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?entity ?label ?property ?value
WHERE {
  ?entity rdfs:label ?label .
  ?entity ?property ?value .
  FILTER(LANG(?label) = "am")
  FILTER(STRSTARTS(STR(?property), STR(dbo:)))
}
ORDER BY ?entity
LIMIT 200`
		},
		{
			title: "Cross-lingual label alignment check",
			description:
				"This federated query joins the Amharic DBpedia endpoint against the main English DBpedia endpoint to verify that Amharic entities are correctly owl:sameAs-linked to their English counterparts. Misaligned or missing links indicate entities that were extracted from Amharic Wikipedia but never connected to the global DBpedia graph.",
			code: `PREFIX owl:  <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?amEntity ?amLabel ?enEntity ?enLabel
WHERE {
  ?amEntity rdfs:label ?amLabel .
  FILTER(LANG(?amLabel) = "am")

  OPTIONAL {
    ?amEntity owl:sameAs ?enEntity .
    SERVICE <https://dbpedia.org/sparql> {
      ?enEntity rdfs:label ?enLabel .
      FILTER(LANG(?enLabel) = "en")
    }
  }
}
LIMIT 100`
		},
		{
			title: "Property coverage audit — mapped vs. unmapped",
			description:
				"Run against the set of 595 canonical DBpedia properties in our dataset, this query identified which properties appeared at least once in the Amharic graph and which were completely absent. The output was used to prioritise which templates to map next.",
			code: `PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?property (COUNT(?value) AS ?usageCount)
WHERE {
  ?entity ?property ?value .
  FILTER(STRSTARTS(STR(?property), STR(dbo:)))
  FILTER EXISTS {
    ?entity rdfs:label ?label .
    FILTER(LANG(?label) = "am")
  }
}
GROUP BY ?property
ORDER BY DESC(?usageCount)`
		}
	];

	const pagesRefactored = [
		"ሊቢያ (Libya) — primary test case; full infobox country template",
		"ኢትዮጵያ (Ethiopia) — verified existing mapping, fixed date literals",
		"አዲስ አበባ (Addis Ababa) — city template; added area and population fields",
		"ኤርትራ (Eritrea) — country template, cross-checked borders property",
		"ሶማሊያ (Somalia) — country template, verified capital and currency",
		"ጂቡቲ (Djibouti) — country template, added official languages field",
		"ሱዳን (Sudan) — country template, resolved duplicate property entries",
		"ደቡብ ሱዳን (South Sudan) — new article; built complete infobox from scratch"
	];

	const mindMapNodes = [
		{ label: "SPARQL queries", slug: "sparql", angle: 270 },
		{ label: "Libya refactor", slug: "libya-article", angle: 342 },
		{ label: "8 pages", slug: "dbpedia-ontology", angle: 54 },
		{ label: "kgproxy", slug: "kgproxy", angle: 126 },
		{ label: "Coverage audit", slug: "dbpedia-sparql", angle: 198 }
	];
</script>

<article class="obsidian-panel min-h-[42rem]">
	<div class="border-b border-foreground/10 pb-5">
		<p class="blog-label">gsoc-2026</p>
		<h1 class="mt-3 font-mono text-4xl leading-tight font-black tracking-tight">
			GSoC 2026 Week 10: SPARQL Investigations and Wikipedia Page Refactoring
		</h1>
		<p class="mt-5 text-base leading-8 text-muted-foreground">
			With the Extraction Framework bugs catalogued in Week 9, Week 10 shifted to a different kind
			of ground truth: querying the live Amharic DBpedia graph directly via SPARQL to understand
			what data had actually made it through extraction, and refactoring Amharic Wikipedia articles
			to improve what would make it through in future runs.
		</p>
		<div class="mt-5 flex flex-wrap gap-2">
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#gsoc-2026</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#week-10</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#sparql</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#wikipedia</span
			>
			<span class="rounded-full bg-muted px-3 py-1 text-xs font-bold text-muted-foreground"
				>#mappings</span
			>
		</div>
	</div>

	<div class="prose-obsidian mt-8 space-y-10">
		<section>
			<h2
				class="flex items-center gap-3 font-mono text-2xl font-bold tracking-tight text-foreground"
			>
				<span
					class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan/20 text-cyan"
				>
					10
				</span>
				Jul 24, 2026 – Jul 31, 2026
			</h2>
			<p class="mt-5">
				After Week 9's deep audit revealed seven bug classes in the DBpedia Extraction Framework,
				Week 10 took a step back from the extraction pipeline itself and focused on what the
				pipeline had already produced. Using SPARQL queries against the Amharic DBpedia endpoint,
				I investigated the coverage and quality of the existing structured data — which entity
				types were well-represented, which properties were actually populated, and where the gaps
				were largest. Alongside that query work, I spent time refactoring eight Amharic Wikipedia
				articles to ensure they used the correct infobox templates and property names, which
				directly determines what the extraction framework will be able to extract in the next
				dump.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Why SPARQL?
			</h2>
			<p class="mt-5">
				The extraction framework produces RDF triples. Those triples are loaded into a SPARQL
				endpoint where any query client can ask structured questions about the data. SPARQL —
				the W3C standard query language for RDF graphs — lets you express questions like "how many
				Amharic-labelled entities have a <code>dbo:birthDate</code> value?" or "which DBpedia
				ontology properties appear most frequently across all Amharic entities?" in a way that
				returns exact counts from the live graph rather than estimates from a sample.
			</p>
			<p class="mt-4">
				This mattered for the project because the LLM benchmark experiments in Weeks 11–13 would
				measure accuracy on a fixed test set of 279 examples. Understanding the underlying
				coverage of the Amharic DBpedia graph — which entity types were dense with data and which
				were sparse — helped calibrate expectations for what accuracy levels were actually
				achievable given the data that existed. A pipeline predicting properties that rarely
				appear in the graph has no way to be verified empirically even if the LLM prediction is
				correct.
			</p>
			<p class="mt-4">
				There was also a practical access issue: the main Amharic DBpedia SPARQL endpoint sat
				behind a VPN that required institutional credentials to reach. This is what eventually led
				to the kgproxy solution deployed in Week 14 — a lightweight AWS proxy that forwards
				authenticated SPARQL traffic from the public internet to the internal endpoint. For now,
				queries were run from behind the VPN, but the friction was real and documented.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				SPARQL queries used this week
			</h2>
			<p class="mt-5">
				Below are the four key queries developed and executed this week, each serving a different
				diagnostic purpose. They are presented with the reasoning behind each one, since the
				queries are only useful in context.
			</p>

			<div class="mt-6 space-y-8">
				{#each sparqlQueries as query (query.title)}
					<div class="rounded-2xl border border-foreground/10 bg-background/70 p-5">
						<h3 class="font-mono text-lg font-black">{query.title}</h3>
						<p class="mt-3 text-sm leading-7 text-muted-foreground">{query.description}</p>
						<pre
							class="mt-4 overflow-x-auto rounded-xl border border-cyan/15 bg-zinc-950 p-4 text-xs leading-6 text-cyan/90"><code>{query.code}</code></pre>
					</div>
				{/each}
			</div>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				What the queries revealed
			</h2>
			<p class="mt-5">
				Running these four queries against the live Amharic DBpedia endpoint returned a clear
				picture of the data landscape. Person entities were by far the most common type, followed
				by Place entities. Organisation and Work entities were underrepresented, with fewer than
				ten percent of the entity count of Person entities. This confirmed what the mapping work
				had suggested: Amharic Wikipedia's biographical article coverage is strong, but its
				coverage of institutions, creative works, and events is limited.
			</p>
			<p class="mt-4">
				On the property side, the coverage audit query showed that a large fraction of the 595
				canonical DBpedia properties in the benchmark dataset had zero occurrences in the Amharic
				graph. This was partly expected — many of those properties describe entity types that are
				rare in Amharic Wikipedia — but it also pointed to genuine gaps in mapping coverage where
				common entity types lacked mappings for obvious properties. The cross-lingual alignment
				query confirmed that <code>owl:sameAs</code> links to English DBpedia were present for
				most Person entities but missing for many Place entities, suggesting those articles had
				been extracted but not linked back to the global graph.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				Libya article and Wikipedia page refactoring
			</h2>
			<p class="mt-5">
				The SPARQL work showed what was missing. The Wikipedia refactoring work was the attempt to
				close part of that gap by improving source articles. The Libya article — written in
				Amharic as ሊቢያ — was chosen as the primary test case because it is a geographically
				prominent country article that was using a partially incorrect infobox template. The
				infobox was missing the population field, had an incorrectly named government type
				property, and used a manually-typed date for independence that the extraction framework
				could not parse because it was in Ethiopian calendar format without the right template
				wrapper.
			</p>
			<p class="mt-4">
				Fixing these issues on the Libya article demonstrated the full edit cycle: identify the
				extraction failure via SPARQL, trace it to the source template or property name in the
				Wikipedia infobox, make the edit in Amharic Wikipedia, and verify that the corrected
				source would produce a valid RDF triple when the extraction framework processes the next
				dump.
			</p>
			<div class="mt-5 space-y-2">
				{#each pagesRefactored as page, i (page)}
					<div class="flex items-start gap-3 rounded-xl border border-foreground/10 bg-muted/20 px-4 py-3">
						<span
							class="flex h-6 w-6 shrink-0 items-center justify-center rounded-full bg-cyan/20 text-[10px] font-black text-cyan"
							>{i + 1}</span
						>
						<span class="text-sm leading-6">{page}</span>
					</div>
				{/each}
			</div>
			<p class="mt-5">
				Eight articles in total were refactored over the course of the week. The focus was on
				country-level and geographic articles because they share a template structure — they all
				use some variant of the country infobox — which meant that fixing one provided a
				replicable pattern for the rest. The refactoring also validated that the extraction
				framework mappings added in previous weeks were syntactically correct: every refactored
				page that used a mapped template produced triples when run through the extraction
				framework locally.
			</p>
		</section>

		<section>
			<h2 class="font-mono text-2xl font-bold tracking-tight text-foreground">
				The VPN access problem begins
			</h2>
			<p class="mt-5">
				A recurring friction this week was the VPN requirement for the Amharic DBpedia SPARQL
				endpoint. Running queries from a local machine was straightforward when connected to the
				institutional VPN, but it meant that the website — deployed as a static GitHub Pages site
				— could not make live SPARQL calls to the endpoint without exposing credentials or
				requiring visitors to be on VPN. This was a blocking issue for the interactive data
				visualization features that were part of the original website roadmap.
			</p>
			<p class="mt-4">
				The problem was documented this week, but the solution — kgproxy, a lightweight SPARQL
				proxy deployed on AWS with an Elastic IP — would not come until Week 14. What this week
				established was the clear articulation of the problem: the infrastructure gap between
				"works on my machine behind VPN" and "works for any visitor to the Amharic DBpedia
				website."
			</p>
		</section>
	</div>
</article>

<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
	<p class="blog-label">Backlinks</p>
	<div class="mt-4 space-y-3">
		{#each wikiLinks as link (link.slug)}
			<ZettelLink
				href={link.href ?? `${base}/blog/gsoc/2026/week-10#${link.slug}`}
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
					WEEK 10
				</div>
			</div>

			{#each mindMapNodes as node (node.label)}
				{@const rad = (node.angle * Math.PI) / 180}
				{@const cx = 130 + 85 * Math.cos(rad)}
				{@const cy = 128 + 85 * Math.sin(rad)}
				<a
					href={`${base}/blog/gsoc/2026/week-10#${node.slug}`}
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
