<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import { IComponent } from '$lib/types/Components';

	import AdminFormInputText from './inputs/AdminFormInputText.svelte';
	import AdminFormInputNumber from './inputs/AdminFormInputNumber.svelte';
	import AdminFormInputPassword from './inputs/AdminFormInputPassword.svelte';
	import AdminFormInputDate from './inputs/AdminFormInputDate.svelte';
	import AdminFormSelect from './inputs/AdminFormSelect.svelte';
	import AdminFormFileInput from './inputs/AdminFormFileInput.svelte';
	import AdminFormFileImageInput from "./inputs/AdminFormFileImageInput.svelte"
	import AdminFormEditor from './inputs/AdminFormEditor.svelte';
	import Spinner from '$lib/components/Spinner.svelte';

	const dispatch = createEventDispatcher();

	export let components: IComponent[];
	export let title: string;
	export let submitMessage: string;
	export let loading: boolean;

	const handlerSubmit = async (e: Event) => {
		dispatch('custom-submit', {
			data: components.map((component) => {
				return { input: component.name, value: component.value };
			})
		});
		console.log("Ey tamo Devuelta")
	};
</script>

<form
	class="flex flex-col gap-3 bg-gray-200 dark:bg-gray-800 p-10"
	on:submit|preventDefault={handlerSubmit}
>
	<!-- Dejamos un slot para ampliar la funcionalidad del formulario dependiendo la necesidad -->
	<slot name="top-slot" />
	<legend class="block mb-2 text-sm font-medium text-gray-900 dark:text-gray-300">{title}</legend>
	{#each components as component}
		{#if component.type == 'text'}
			<AdminFormInputText
				label={component.label}
				bind:value={component.value}
				required={component.required}
			/>
		{:else if component.type == 'number'}
			<AdminFormInputNumber
				label={component.label}
				bind:value={component.value}
				required={component.required}
			/>
		{:else if component.type == 'date'}
			<AdminFormInputDate
				label={component.label}
				bind:value={component.value}
				required={component.required}
			/>
		{:else if component.type == 'password'}
			<AdminFormInputPassword
				label={component.label}
				bind:value={component.value}
				required={component.required}
			/>
		{:else if component.type == 'select'}
			<AdminFormSelect
				label={component.label}
				options={component.options}
				bind:value={component.value}
				required={component.required}
			/>
		{:else if component.type == 'file'}
			<AdminFormFileInput label={component.label} bind:files={component.value} />
		{:else if component.type == 'image'}
			<AdminFormFileImageInput label={component.label} bind:files={component.value} />
		{:else if component.type == 'editor'}
			<AdminFormEditor label={component.label} bind:value={component.value} />
		{/if}
	{/each}
	<!-- Dejamos un slot para ampliar la funcionalidad del formulario dependiendo la necesidad -->
	<slot name="bottom-slot" />
	<button
		class="w-full m-0 focus:outline-none text-white bg-indigo-700 hover:bg-indigo-800 focus:ring-4 focus:ring-indigo-300 font-medium rounded-lg text-sm px-5 py-2.5  dark:bg-indigo-600 dark:hover:bg-indigo-700 dark:focus:ring-indigo-900 flex justify-center"
	>
		{#if loading}
			<Spinner />
		{:else}
			{submitMessage}
		{/if}
	</button>
</form>
