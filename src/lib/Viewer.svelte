<script>
  import { run } from 'svelte/legacy';
  import { onMount, createEventDispatcher } from 'svelte';
  import '@toast-ui/editor/dist/toastui-editor.css';
  import { defaultValueMap } from './tui_defaults.js';
  
  let { 
    height = defaultValueMap.height, 
    initialValue = defaultValueMap.initialValue, 
    options = {}, 
    onload = () => {},
 
  } = $props();

  let editor = $state();
  let node = $state();
  let _height = $state(height);

  /**
   * @returns {Node}
   */
  export function getRootElement() {
    return node;
  }
  export function getEditor() {
    return editor;
  }
  run(() => {
    if(height !== _height) {
      editor.height(height);
      _height = height;
    }
  });

  async function initViewer() {

    const Viewer = (await import('@toast-ui/editor/dist/toastui-editor-viewer')).default;
    const viewerOptions = { ...defaultValueMap, ...options, initialValue };

    // Map events to callback props
    viewerOptions.events = {
      load: (...args) => onload(...args)
    };

    viewerOptions.el = node;
    editor = new Viewer(viewerOptions);
  }

  onMount(async () => {
    await initViewer();

    return () => {
      if (editor) {
        //editorEvents.forEach(event => editor.off(event));
        editor.remove();
      }
    };
  });
</script>

<div bind:this={node}></div>