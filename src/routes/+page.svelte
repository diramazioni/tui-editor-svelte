<script lang="ts" setup>
  import Editor from "$lib/Editor.svelte";
  import Viewer from "$lib/Viewer.svelte";
  import { content } from "./dummy.js";
  

  let viewerMode = $state(false);
  let editorRef = $state(); // Reference to store the component instance

  function invokeTest() {
    const editorInstance = editorRef.getEditor();
    // editorInstance.changePreviewStyle('tab');
    editorInstance.exec('bold');
    editorInstance.insertText('[example test]("http://example.com")')
  }

</script>

<main></main>

<h1>Welcome to tui-editor-svelte for Svelte 5!</h1>
<p>
  <button onclick={() => (viewerMode = !viewerMode)}>
    {viewerMode ? "Editor mode" : "Viewer mode"}
  </button>
</p>

{#if viewerMode}
  <Viewer initialValue={content} onload={() => console.log("Viewer loaded")} />
{:else}
  <Editor
    bind:this={editorRef}
    initialValue={content}
    onload={() => console.log("Editor loaded")}
  />
  <!-- onchange={() => console.log('Editor changed')}
    onfocus={() => console.log('Editor focused')}
    onblur={() => console.log('Editor blur')} -->
{/if}
<button onclick={invokeTest}>Invoke Method</button>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
