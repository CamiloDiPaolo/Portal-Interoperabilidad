<script lang="ts">
	import type { PageData } from './$types';
	// import { Plus, Table } from 'svelte-hero-icons';
	import type { IComponent } from '$lib/types/Components';
	// import AdminForm from '$lib/components/AdminForm.svelte';
	import AdminList from '$lib/components/AdminList.svelte';
	// import AdminButton from '$lib/components/AdminButton.svelte';

	export let data: PageData;
	// let list = true;
	let loading = false;
	let modalConfirm = false;
	let messageSubmit = { status: false, message: '' };

	// const components: IComponent[] = [
	// 	{
	// 		type: 'text',
	// 		label: 'Titulo del grafico',
	// 		name: 'title',
	// 		value: '',
	// 		required: true
	// 	},
	// 	{
	// 		type: 'select',
	// 		label: 'Tipo de grafico',
	// 		name: 'type',
	// 		value: '',
	// 		required: true,
	// 		options: [
	// 			{ value: 'pie', name: 'torta' },
	// 			{ value: 'bar', name: 'barras' }
	// 		]
	// 	}
	// ];

	// const graphSubmit = async (e: CustomEvent) => {
	// 	if (loading) return;

	// 	loading = true;
	// 	const { data } = e.detail;
	// 	const body = {
	// 		title: data[0].value,
	// 		type: data[1].value
	// 	};

	// 	try {
	// 		await fetch(`/api/graphs`, {
	// 			method: 'POST',
	// 			headers: {
	// 				'Content-Type': 'application/json',
	// 				Accept: 'application/json'
	// 			},
	// 			body: JSON.stringify(body)
	// 		});
	// 	} catch (err) {
	// 		console.log(err);
	// 	} finally {
	// 		loading = false;
	// 	}
	// };

	const deleteGraph = async (e: CustomEvent) => {
		try {
			await fetch(`/api/graphs/${e.detail.id}`, {
				method: 'DELETE',
				headers: {
					'Content-Type': 'application/json',
					Accept: 'application/json'
				}
			});
		} catch (err) {
			console.log(err);
		} finally {
			location.href = location.href;
		}
	};

	const modifyGraph = (e: CustomEvent) => {
		location.href = `/admin/graphs/${e.detail.id}`;
	};
</script>

<!-- <AdminButton
	icon={list ? Plus : Table}
	on:click={() => {
		list = !list;
	}}
/> -->
<main class="ml-56 dark:bg-gray-900 bg-neutral-50 h-screen relative overflow-y-scroll">
	<div class="w-3/4 h-3/4 absolute bottom-1/2 right-1/2 transform translate-x-1/2 translate-y-1/2">
		<!-- {#if list} -->
		<AdminList
			headers={['titulo', 'tipo']}
			attributes={['title', 'type']}
			data={JSON.parse(data.graphs)}
			on:delete-doc={deleteGraph}
			on:modify-doc={modifyGraph}
			caption="Graficos"
			actions={['edit']}
		/>
		<!-- {:else}
			<AdminForm
				title="Formulario de graficos"
				{components}
				submitMessage="Subir grafico"
				{loading}
				on:custom-submit={graphSubmit}
			/>
		{/if} -->
	</div>
</main>
