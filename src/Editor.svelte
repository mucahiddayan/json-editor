<script>
	import Field from './Field.svelte';
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

	export let value = {};
	export let path = '';
	export let config={};
	let entries = [];


	function isObject(val){
		if(Array.isArray(val)){
			return false;
		}
		if(['string','number','boolean'].some(type => typeof val === type)){
			return false;
		}
		return true;
	}

$:entries = Object.entries(value||{});

	function getPath(parent,...keys){
		return `${parent?`${parent}.`:''}${keys.join('.')}`;
	}

	function update({detail}){
		dispatch('update',detail)
	}


</script>
<svelte:options tag="json-editor"/>
{#each entries as [key,val]}
	{#if isObject(val)}
		<svelte:self value={val} on:update={update} path={getPath(path,key)} {config}/>
	{:else if Array.isArray(val)}
		{#each val.filter(isObject) as entry,index}
			<svelte:self value={entry}  on:update={update} path={getPath(path,key,index)} {config}/>
		{/each}
		<svelte:self value={val.filter(e=>!isObject(e))}  on:update={update} path={getPath(path,key)} {config}/>
	{:else}
	<Field on:fieldUpdated={update} path={getPath(path,key)} value={val} {config}/>
	{/if}
{/each}

