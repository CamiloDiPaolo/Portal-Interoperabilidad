<script lang="ts">
	import type { PageData } from './$types';
	import type { IComponent } from '$lib/types/Components';
	import { Plus, Table } from 'svelte-hero-icons';
	import AdminForm from '$lib/components/AdminForm.svelte';
	import AdminList from '$lib/components/AdminList.svelte';
	import AdminButton from '$lib/components/AdminButton.svelte';
	import { validateInputFile, validateEmptyInput } from '$lib/components/inputs/validators';
	import AdminModalConfirm from '$lib/components/modals/AdminModalConfirm.svelte';

	export let data: PageData;
	let list = true;
	let loading = false;
	let modalConfirm = false;
	let messageSubmit = { status: false, message: '' };

	const components: IComponent[] = [
		{
			type: 'file',
			label: 'Input de archivos',
			name: 'manual',
			value: '',
			files: []
		},
		{
			type: 'select',
			label: 'Tipo de archivo',
			name: 'file',
			value: '',
			required: true,
			options: [
				{ value: 'files', name: 'Archivo' },
				{ value: 'videos', name: 'Video' }
			]
		},
		{
			type: 'text',
			label: 'Descripcion del archivo',
			name: 'description',
			value: '',
			required: true
		}
	];

	const validateInputs = (data: any) => {
		if (validateInputFile(data[0].value).status && validateEmptyInput(data[1].value).status) {
			return { status: true, message: 'Se subio correctamente' };
		} else {
			return { status: false, message: 'Alguno de los datos ingresados es incorrecto' };
		}
	};

	const fileSubmit = async (e: CustomEvent) => {
		if (loading) return;
		const { data } = e.detail;
		const files = data[0].value;

		loading = true;

		messageSubmit = validateInputs(data);
		if (!messageSubmit.status) {
			loading = false;
			return (modalConfirm = true);
		}

		const reader = new FileReader();
		reader.readAsDataURL(files[0]);
		reader.onload = async (e) => {
			// uploadImage(e.target.result);
			const target = e.target as FileReader;
			const fileReaderResult = target.result as string;
			const file = fileReaderResult.split(',');
			const body = {
				file: file[1],
				type: data[1].value,
				name: files[0].name,
				description: data[2].value
			};
			try {
				await fetch(`/api/files`, {
					method: 'POST',
					headers: {
						'Content-Type': 'application/json',
						Accept: 'application/json'
					},
					body: JSON.stringify(body)
				});
				modalConfirm = true;
			} catch (err) {
				console.log(err);
			} finally {
				loading = false;
			}
		};
	};

	const deleteFile = async (e: CustomEvent) => {
		try {
			await fetch(`/api/files/${e.detail.id}`, {
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
</script>

<AdminButton
	icon={list ? Plus : Table}
	on:click={() => {
		list = !list;
	}}
/>
<main class="ml-56 dark:bg-gray-900 bg-neutral-50 h-screen relative overflow-y-scroll">
	<div class="w-3/4 h-3/4 absolute bottom-1/2 right-1/2 transform translate-x-1/2 translate-y-1/2">
		{#if list}
			<AdminList
				headers={['Nombre', 'Descripcion', 'Extension', 'Orden']}
				attributes={['name', 'description', 'extension', 'order']}
				data={JSON.parse(data.files).map((file) => {
					return {
						_id: file._id,
						name: file.name.split('.').at(0),
						extension: file.name.split('.').at(-1),
						order: file.order,
						description: file.description
					};
				})}
				on:delete-doc={deleteFile}
				caption="Archivos"
				actions={['delete']}
			/>
		{:else}
			<AdminForm
				title="Formulario de archivos"
				{components}
				submitMessage="Subir archivo"
				{loading}
				on:custom-submit={fileSubmit}
			/>
		{/if}
	</div>
	<div>
		{#if modalConfirm}
			<AdminModalConfirm
				status={messageSubmit.status}
				message={messageSubmit.message}
				on:close={() => {
					modalConfirm = false;
					messageSubmit.status ? location.reload() : (list = false);
					// if (messageSubmit.status) {
					// 	location.reload();
					// } else {
					// 	list = false;
					// }
				}}
			/>
		{/if}
	</div>
</main>
