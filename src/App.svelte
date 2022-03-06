<svelte:options tag="json-editor" />

<script>
  import Editor from "./Editor.svelte";
  export let json;
  export let config = {};
  let editor;
  function handleUpdate({ detail }) {
    editor.dispatchEvent(
      new CustomEvent("updated", {
        detail,
        bubbles: true,
        cancelable: true,
        composed: true,
      })
    );
  }
</script>

<div class="json-editor" bind:this={editor}>
  <Editor value={json} {config} on:update={handleUpdate} />
</div>
