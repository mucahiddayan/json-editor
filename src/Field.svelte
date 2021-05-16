<style>
	.field{
		margin: var(--field-margin);
	}
</style>
<script>
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

	export let value = {};
	export let config = {};
	export let path = '';
	export let key='';

	const INPUT_TYPES = ['email','date','password','tel','url','month','week','time','search','color','range'];

	let invalid=false;

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
		if(/([\.|\\|\/|-]\d{1,4})+$/g.test(val) && !isNaN(parseInt(val)) && new Date(val).toString() !== "Invalid Date"){
		 return typeMapper['date'];
		}
		if(Array.isArray(val)){
			return typeMapper['multiselect']
		}
		const type =  byKey() || typeMapper[typeof val];
		if(type === 'text' && val.length > 50){
			return typeMapper['multiline'];
		}
		return type;
	}

	function byKey(){
		return INPUT_TYPES.find(_=> _ === key)

	}

	function dateToDateInput(date){
		if(date.toString() === 'Invalid Date'){
			return
		}
		const parts = new Intl.DateTimeFormat('uk',{}).formatToParts(date);
		const d = new Proxy(parts, {get(o,f){
			return o.find(e=> e.type===f).value;
		}})
		return `${d.year}-${d.month}-${d.day}`
	}

	function getValue(val){
		const type = getType(value);
		if(type === 'date'){
			return dateToDateInput(new Date(val));
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
		const type = getType(value);
		const val =  type=== 'checkbox'? event.target.checked:type === 'multiselect'?getSelectedOptions(event.target):event.target.value;
		dispatch('fieldUpdated',{value:type === 'number'?parseFloat(val):val, path})
	}

	function validate({target:{value:val}}){
		const type = getType(value);
		const emailMatcher = config.emailMatcher ||
	 /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/

		console.log(config.emailMatcher)
		invalid = type === 'email'? !(emailMatcher).test(val):false;
	}

</script>

<svelte:options tag="json-editor-field"/>
<div class="field" data-path={path}>
{#if !config.hideLabel}<label for={path}>{path}</label>{/if}
	{#if getType(value) === 'textarea'}
	<textarea on:blur={submit} {value} placeholder={path} id={path}/>
	<!-- {:else if getType(value) === 'multiselect'}
		<select on:blur={submit} multiple placeholder={path} id={path} {value}>
			{#each value as option}
				<option value={option}>{option}</option>
			{/each}
		</select> -->
	{:else}
		<input on:input={validate} on:blur={submit} on:change={submit} placeholder={path} id={path} type={getType(value)} value={getValue(value)} data-value={getValue(value)} checked={getType(value)}/>
		{#if invalid}Invalid {getType(value)}{/if}
	{/if}

</div>