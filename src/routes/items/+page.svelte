<script lang="ts">
	import { onMount } from 'svelte';
	import type Item from 'src/types/Item';
	import ItemImage from '$lib/ItemImage.svelte';
	import { unescape } from '$lib/htmlParser';

	let page = 0;
	let searchTerm = '';

	type SearchItem = Pick<
		Item,
		| 'id'
		| 'name'
		| 'rarity'
		| 'icon_path'
		| 'main_description'
		| 'guide_description'
		| 'tooltip_description'
	>;

	let data: SearchItem[] = [];

	async function fetchData() {
		const response = await fetch(`/api/items?search=${searchTerm}&page=${page}&limit=20`);

		const items = await response.json();

		if (items.length === 0) {
			data = [];
			return;
		}

		// check if items content is the same as the data
		if (data.length > 0 && data[data.length - 1].id === items[items.length - 1].id) {
			return;
		}

		data = items;
	}

	onMount(() => {
		// load first batch onMount
		fetchData();
	});

	function getDescription(item: SearchItem) {
		if (item.main_description.length > 0) {
			return unescape(item.main_description);
		} else if (item.guide_description.length > 0) {
			return unescape(item.guide_description);
		} else if (item.tooltip_description.length > 0) {
			return unescape(item.tooltip_description);
		} else {
			return 'No description';
		}
	}
</script>

<div>
	<h1 class="mb-4 text-4xl font-bold">Items</h1>
	<input
		type="text"
		placeholder="Search 🔎"
		class="mb-4 rounded-lg border p-2"
		bind:value={searchTerm}
		on:keyup={async (e) => {
			if (e.key === 'Enter') {
				await fetchData();
			}

			if (e.key === 'Escape') {
				searchTerm = '';
			}

			if (searchTerm.length === 0) {
				await fetchData();
			}

			if (searchTerm.length > 2) {
				await fetchData();
			}
		}}
	/>

	<table class="min-w-full">
		<thead class="border-b">
			<tr>
				<th scope="col" class="py-4 pr-6 text-left">Icon</th>
				<th scope="col" class="hidden py-4 pr-6 text-left lg:table-caption">Id</th>
				<th scope="col" class="py-4 pr-6 text-left">Name</th>
				<th scope="col" class="hidden py-4 pr-6 text-left lg:table-caption">Description</th>
			</tr>
		</thead>
		<tbody>
			{#each data as item}
				<tr
					class="cursor-pointer border-b last:border-none hover:bg-zinc-800"
					on:click={() => {
						// shit code, why can't we use anchor tag around the whole row?
						window.location.href = `/items/${item.id}`;
					}}
				>
					<td class="flex flex-col items-center justify-center py-4 lg:table-cell lg:flex-row">
						<ItemImage
							icon_path={item.icon_path.toLocaleLowerCase()}
							name={item.name}
							rarity={item.rarity}
						/>
						<button
							on:click={(e) => {
								navigator.clipboard.writeText(item.id.toString());
								e.stopPropagation();
							}}
							title="Copy ID"
							class="mt-4 flex items-center justify-center gap-2 rounded-lg border p-1 lg:hidden"
						>
							{item.id}
							<img src="/icons/copy-content.svg" width={20} height={20} alt="Copy" />
						</button>
					</td>
					<td class="hidden lg:table-cell">
						<button
							on:click={(e) => {
								navigator.clipboard.writeText(item.id.toString());
								e.stopPropagation();
							}}
							title="Copy ID"
							class="flex items-center justify-start gap-2 rounded-lg border p-1"
						>
							{item.id}
							<img src="/icons/copy-content.svg" width={20} height={20} alt="Copy" />
						</button>
					</td>
					<td class="align-middle">{item.name}</td>
					<td class="hidden h-full w-96 align-middle lg:table-cell">{getDescription(item)}</td>
				</tr>
			{/each}
		</tbody>
	</table>
</div>