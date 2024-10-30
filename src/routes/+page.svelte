<script lang="ts" setup>
    import Editor from "$lib/Editor.svelte";
    import { content } from './dummy.js';
</script>

<main>

</main>

<Editor initialValue={content} 
load={() => console.log('Editor loaded')}
change={() => console.log('Editor changed')}
focus={() => console.log('Editor focused')}
/>

<h1>Welcome to your library project</h1>
<p>Create your package using @sveltejs/package and preview/showcase your work with SvelteKit</p>
<p>Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the documentation</p>


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