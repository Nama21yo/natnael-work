<script lang="ts">
	import { page } from "$app/state";
	import { base } from "$app/paths";

	let { children } = $props();

	// Single source of truth for the GSoC weekly nav -- add a week here once
	// and every page (including old ones) picks it up, instead of every page
	// carrying its own frozen copy of the list.
	const weeks = [
		{ id: "pre-coding", label: "Pre-Coding" },
		{ id: "week-1", label: "Week 1" },
		{ id: "week-2", label: "Week 2" },
		{ id: "week-3", label: "Week 3" },
		{ id: "week-4", label: "Week 4" },
		{ id: "week-5", label: "Week 5" },
		{ id: "week-6", label: "Week 6" },
		{ id: "week-7", label: "Week 7" },
		{ id: "week-8", label: "Week 8" },
		{ id: "week-9", label: "Week 9" },
		{ id: "week-10", label: "Week 10" },
		{ id: "week-11", label: "Week 11" },
		{ id: "week-12", label: "Week 12" },
		{ id: "week-13", label: "Week 13" },
		{ id: "week-14", label: "Week 14" },
		{ id: "final-week", label: "Final Week" }
	];

	const overviewHref = `${base}/blog/gsoc/2026`;

	const pathname = $derived(page.url.pathname.replace(/\/$/, ""));
	const isOverview = $derived(pathname === overviewHref);
	const activeId = $derived(pathname.split("/").pop());

	function navClass(active: boolean) {
		return active
			? "block rounded-xl bg-cyan/15 px-3 py-2 text-sm font-bold text-cyan"
			: "block rounded-xl px-3 py-2 text-sm font-bold text-muted-foreground transition hover:bg-muted hover:text-foreground";
	}
</script>

{#if isOverview}
	{@render children()}
{:else}
	<div class="grid gap-5 lg:grid-cols-[17rem_minmax(0,1fr)_18rem]">
		<aside class="obsidian-panel h-fit lg:sticky lg:top-28">
			<p class="blog-label">GSoC 2026</p>
			<div class="mt-4 space-y-1">
				<a href={overviewHref} class={navClass(false)}>Overview</a>
				{#each weeks as w (w.id)}
					<a href={`${overviewHref}/${w.id}`} class={navClass(activeId === w.id)}>{w.label}</a>
				{/each}
			</div>
		</aside>
		{@render children()}
	</div>
{/if}
