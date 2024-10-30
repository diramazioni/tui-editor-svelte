<script lang="ts" setup>
    import Editor from "$lib/Editor.svelte";
    import Viewer from "$lib/Viewer.svelte";
    import { content } from './dummy.js';
    let viewer = false
</script>

<main>

</main>

<h1>Welcome to tui-editor-svelte for Svelte 5!</h1>
<p><button on:click={() => viewer = !viewer}>{viewer ? 'Editor mode' : 'Viewer mode'}</button></p>

{#if viewer}
    <Viewer initialValue={content} 
    onload={() => console.log('Viewer loaded')}
    onchange={() => console.log('Viewer changed')}
    onfocus={() => console.log('Viewer focused')}
    onblur={() => console.log('Viewer blur')}
    />    
{:else}
    <Editor initialValue={content} 
    onload={() => console.log('Editor loaded')}
    onchange={() => console.log('Editor changed')}
    onfocus={() => console.log('Editor focused')}
    onblur={() => console.log('Editor blur')}
    />
{/if}


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