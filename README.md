# tui-editor-svelte for Svelte 5!

This libraray is a wrapper for [tui-editor](https://github.com/nhn/tui.editor) for Svelte 5.

## Installation

`npm install @diramazioni/tui-editor-svelte`

## Usage
Example `+page.svelte`:
```svelte
<script lang="ts" setup>
  import Editor from "$lib/Editor.svelte";
  import Viewer from "$lib/Viewer.svelte";
  import { content } from "./dummy.js";
  
  let viewerMode = $state(false);
  let editorRef = $state(); // Reference to store the component instance
</script>

<main>
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

{/if}
</main>
```

## Plugins

All the plugins from [tui-editor](https://github.com/nhn/tui.editor) are supported, to use them you need to specify an array of the plugin you want to use on the pluginsOn prop. 
```svelte
  <Viewer 
    initialValue={content} 
    pluginsOn={['tableMergedCell','codeSyntaxHighlight','chart', 'uml']} 
    />
  <Editor
    bind:this={editorRef}
    initialValue={content}
    pluginsOn={['colorSyntax', 'tableMergedCell','codeSyntaxHighlight', 'chart', 'uml']} />
```
---

## Developing this library

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

Everything inside `src/lib` is part of your library, everything inside `src/routes` can be used as a showcase or preview app.

## Building

To build your library:

```bash
npm run package
```

To create a production version of your showcase app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://svelte.dev/docs/kit/adapters) for your target environment.

## Publishing

Go into the `package.json` and give your package the desired name through the `"name"` option. Also consider adding a `"license"` field and point it to a `LICENSE` file which you can create from a template (one popular option is the [MIT license](https://opensource.org/license/mit/)).

To publish your library to [npm](https://www.npmjs.com):

```bash
npm publish
```
