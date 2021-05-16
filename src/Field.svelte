<style>
	.field{
		margin: var(--field-margin);
	}
</style>
<script>
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

	export let value;
		export let path;

	const typeMapper = {
		string: 'text',
		number:'number',
		date:'date',
		boolean: 'checkbox',
		multiline:'textarea',
		multiselect:'multiselect'
	}

	// TODO: handle array of objects
	function getType(val){
		if(val === 'true'|| val ==='false'){
			return typeMapper['boolean'];
		}
		if(/([\.|\\|\/]\d{1,4})+$/g.test(val) && !isNaN(parseInt(val)) && new Date(val).toString() !== "Invalid Date"){
		 return typeMapper['date'];
		}
		if(Array.isArray(val)){
			return typeMapper['multiselect']
		}
		const type =  typeMapper[typeof val];
		if(type === 'text' && val.length > 50){
			return typeMapper['multiline'];
		}
		return type;
	}

	function getValue(val){
		const type = getType(value);
		if(type === 'date'){
			return new Date(val);
		}
		if(type === 'boolean'){
			return val === 'true';
		}
		return val;
	}

	function getSelectedOptions(element){
		return Array.from(element.selectedOptions).map(e=> e.value)
	}

	function submit(event){
		const val = getType(value) === 'checkbox'? event.target.checked:getType(value) === 'multiselect'?getSelectedOptions(event.target):event.target.value;
		dispatch('fieldUpdated',{value:val, path})
	}

</script>

<svelte:options tag="json-editor-field"/>
<div class="field" data-path={path}>
	<label for={path}>{path}</label>
	{#if getType(value) === 'textarea'}
	<textarea on:blur={submit} {value} placeholder={path} id={path}/>
	{:else if getType(value) === 'multiselect'}
		<select on:blur={submit} multiple placeholder={path} id={path} {value}>
			{#each value as option}
				<option value={option}>{option}</option>
			{/each}
		</select>
	{:else}
		<input on:blur={submit} on:change={submit} placeholder={path} id={path} type={getType(value)} value={getValue(value)} checked={getType(value)}/>
	{/if}

</div>