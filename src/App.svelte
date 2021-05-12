<style>
	.invalid{
		box-shadow:0px 0px 0px 3px red;
	}

	.preivew{
		width: 400px;
		height: 300px;
		overflow: auto;
	}

</style>
<script>
	import {onMount} from 'svelte';
	import set from 'lodash/set';
	import Editor from './Editor.svelte';

	let json = '{"date":"11.2.2021",\"associations\":[{\"group\":{\"id\":\"BMW\",\"name\":\"BMW\"},\"roles\":[{\"application\":{\"name\":\"dsBOARD\"},\"name\":\"user\"}]},{"asses2":{"name":"ass 2"}}],\"authentications\":[{\"type\":\"password\",\"value\":\"mackiemesser\"}],\"email\":\"theknife@dieplatte.org\",\"otherNames\":null,\"salutation\":\"Herr\",\"status\":\"Pending\",\"surname\":\"MacHeath\",\"title\":\"Prof.\",\"username\":\"mackiemesser\",\"userType\":\"Notary Assessor\","array":[1,2,3,4,5]}';
	let parsed={};
	let jsonError=false;

	let error;

	function validate(event){
		try{
			JSON.parse(event.target.value);
			jsonError=false;
		}catch(er){
			jsonError = true;
		}
	}
	onMount(()=>{

		try{
		parsed = JSON.parse(json);
					console.log(parsed)
		}catch(er){
			error = `${er.message} => "${json}"`;
		}
	});

	let preview = '';

	function updatePreview({detail}){
		// parsed = detail;
		console.log(detail.path,detail.value);
		set(parsed,detail.path,detail.value);
		preview = JSON.stringify(parsed);
	}

	function inputJson(event){
		try{
			parsed = JSON.parse(event.target.value)
		}catch(er){
			console.warn(er)
		}
	}

</script>
<Editor value={parsed} on:update={updatePreview}/>
{#if error}
	{error}
{/if}

<textarea class="{jsonError?'invalid':''}" on:input={validate} on:blur={inputJson}></textarea>
{#if jsonError}
	<span>Json parse error</span>
{/if}

<div class="preview">
Preivew: {preview}
</div>