<script lang="ts">
	import ItemImage from '$lib/components/ItemImage.svelte';
	import NpcImage from '$lib/components/NpcImage.svelte';

	import type { PageData } from './$types';
	import { url } from '$lib/helpers/addBasePath';

	export let data: PageData;
</script>

<svelte:head>
	<title>MapleStory 2 Handbook</title>
</svelte:head>

<div
	class="main-container mt-5 flex h-full flex-col items-start gap-10 rounded-xl bg-zinc-800 py-2 md:mt-10 md:flex-col lg:m-auto lg:flex-row xl:w-3/4"
>
	<div
		class="mx-5 flex flex-col items-center justify-center rounded-md border border-gray-600 bg-zinc-800 px-5 pb-5 lg:w-1/2 lg:justify-center"
	>
		<h1 class="mb-5 mt-3 text-2xl text-white">Most Viewed Items</h1>
		<div class="flex w-full flex-col lg:w-3/4">
			{#each data.props.mostViewedItems as item}
				<div class="w-full border-b border-blue-ascent py-3 last:border-none hover:bg-zinc-800 ">
					<a class="flex flex-row items-center " href={url(`/items/${item.id}`)}>
						<ItemImage icon_path={item.icon_path} name={item.name} rarity={item.rarity} />
						<p class="ml-8 w-1/2 text-white line-clamp-1 lg:w-3/4">{item.name}</p>
					</a>
				</div>
			{/each}
		</div>
	</div>

	<div
		class="mx-5 flex flex-col items-center justify-center rounded-md border border-gray-600 bg-zinc-800 px-5 pb-5 lg:w-1/2 lg:justify-center"
	>
		<h1 class="mb-5 mt-3 text-2xl text-white">Most Viewed NPCs</h1>
		<div class="flex w-full flex-col lg:w-3/4">
			{#each data.props.mostViewedNpcs as npc}
				<div class="w-full border-b border-blue-ascent py-3 last:border-none hover:bg-zinc-800">
					<a class="flex flex-row items-center " href={url(`/npcs/${npc.id}`)}>
						<NpcImage portrait={npc.portrait} name={npc.name} />
						<p class="ml-8 w-1/2 text-white line-clamp-1 lg:w-3/4">{npc.name}</p>
					</a>
				</div>
			{/each}
		</div>
	</div>
</div>
