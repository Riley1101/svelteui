<script lang="ts">
	import { randomID } from '$lib/styles';
	import { createEventForwarder, useActions } from '$lib/internal';
	import { get_current_component } from 'svelte/internal';
	import Input from '../Input/Input.svelte';
	import InputWrapper from '../InputWrapper/InputWrapper.svelte';
	import ChevronUpDown from './ChevronUpDown.svelte';
	import type { NativeSelectProps as $$NativeSelectProps } from './NativeSelect';

	interface $$Props extends $$NativeSelectProps {}

	export let use: $$Props['use'] = [],
		element: $$Props['element'] = undefined,
		className: $$Props['className'] = '',
		override: $$Props['override'] = {},
		id: $$Props['id'] = 'NativeSelect',
		placeholder: $$Props['placeholder'] = '',
		data: $$Props['data'] = [],
		inputStyle: $$Props['inputStyle'] = {},
		wrapperProps: $$Props['wrapperProps'] = {},
		size: $$Props['size'] = 'sm',
		icon: $$Props['icon'] = null,
		iconWidth: $$Props['iconWidth'] = 36,
		iconProps: $$Props['iconProps'] = { size: 20, color: 'currentColor' },
		rightSectionWidth: $$Props['rightSectionWidth'] = 36,
		rightSectionProps: $$Props['rightSectionProps'] = {},
		required: $$Props['required'] = false,
		radius: $$Props['radius'] = 'sm',
		variant: $$Props['variant'] = 'default',
		disabled: $$Props['disabled'] = false,
		value: $$Props['value'] = '',
		label: $$Props['label'] = '',
		description: $$Props['description'] = '',
		error: $$Props['error'] = '',
		labelProps: $$Props['labelProps'] = {},
		descriptionProps: $$Props['descriptionProps'] = {},
		errorProps: $$Props['errorProps'] = {};
	export { className as class };

	/** Generate random id for label and select elements*/
	const uuid = randomID(id);

	/** Map through the data and format it*/
	let formattedData = [];
	$: data &&
		(formattedData = data.map((item) =>
			typeof item === 'string' ? { label: item, value: item } : item
		));

	/** An action that forwards inner dom node events from parent component */
	const forwardEvents = createEventForwarder(get_current_component());

	/** When no icon is present give the left section 12px of padding*/
	const base = { '& .input': { paddingLeft: 12 } };
</script>

<!--
@component

Capture user feedback limited to large set of options

@see https://svelteui.org/core/native-select
@example
    ```svelte
    <NativeSelect
      data={['React', 'Vue', 'Angular', 'Svelte']}
      placeholder="Pick one"
      label="Select your favorite framework/library"
      description="This is anonymous"
      required
    />
    ```
-->
<InputWrapper
	id={uuid}
	class="{className} svelteui-NativeSelect-root"
	{size}
	{label}
	{error}
	{override}
	{required}
	{labelProps}
	{errorProps}
	{description}
	{descriptionProps}
	{...wrapperProps}
>
	<Input
		bind:element
		use={[forwardEvents, [useActions, use]]}
		bind:value
		root="select"
		id={uuid}
		autocomplete="off"
		invalid={Boolean(error)}
		override={{ ...base, ...inputStyle }}
		{size}
		{icon}
		{radius}
		{variant}
		{required}
		{disabled}
		{iconWidth}
		{iconProps}
		{placeholder}
		{rightSectionWidth}
		{rightSectionProps}
		{...$$restProps}
	>
		{#if placeholder}
			<option value="" disabled hidden selected={!value}>
				{placeholder}
			</option>
		{/if}
		<slot slot="icon" name="icon" />
		{#each formattedData as item}
			<option value={item.value} disabled={item.disabled} selected={item.value === value}>
				{item.label ?? item.value}
			</option>
		{:else}
			<option value="" disabled hidden>Add Some Options</option>
		{/each}
		<svelte:fragment slot="rightSection">
			<slot name="rightSection" size={iconProps.size} color={iconProps.color}>
				<svelte:component this={ChevronUpDown} size={iconProps.size} color={iconProps.color} />
			</slot>
		</svelte:fragment>
	</Input>
</InputWrapper>
