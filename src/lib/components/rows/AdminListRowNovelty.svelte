<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	export let doc: any;
	export let attributes: string[];
	export let actions: string[];

	const distpach = createEventDispatcher();

	const deleteEvent = () => {
		distpach('delete-doc', { id: doc._id });
	};
	const modifyEvent = () => {
		distpach('modify-doc', { id: doc._id, doc: doc });
	};
</script>

<tr
	class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600"
>
	<td class="text-center h-1 py-3 px-6 t"><span>{doc['title']}</span></td>
	<td class="text-center h-1 py-3 px-6 t"><span>{doc['summary']}</span></td>
	<td class="text-center h-1 py-3 px-6 t"><span>{doc['slug']}</span></td>
	<td class=" py-4 px-6 text-gray-900 whitespace-nowrap dark:text-white">
		<img
			class="w-16 rounded"
			src={!doc.image || doc.image == '#' ? '/img/test.png' : doc.image}
			alt="foto novedad"
		/>
	</td>
	<td class="text-center h-1 py-3 px-6 t"><span>{doc['date']}</span></td>
	<td class="h-1 py-3 px-6 text-center">
		<div class="flex items-center w-full justify-center gap-2">
			{#if actions.includes('edit')}
				<button
					href="#"
					class="font-medium text-indigo-600 dark:text-indigo-500 hover:underline"
					on:click={modifyEvent}>Editar</button
				>
			{/if}
			{#if actions.includes('delete')}
				<button
					href="#"
					class="font-medium text-red-600 dark:text-red-500 hover:underline"
					on:click={deleteEvent}>Eliminar</button
				>
			{/if}
		</div>
	</td>
</tr>
