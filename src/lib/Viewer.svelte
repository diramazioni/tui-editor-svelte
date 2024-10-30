<script>
  import { run } from 'svelte/legacy';
  import { onMount, createEventDispatcher } from 'svelte';
  import '@toast-ui/editor/dist/toastui-editor.css';
  import { editorEvents, defaultValueMap } from './tui_defaults.js';
  
  const dispatch = createEventDispatcher();

  /**
   * @typedef {Object} Props
   * @property {string} [height]
   * @property {string} [initialValue]
   * @property {object} [options]
   */

  /** @type {Props} */
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

  run(() => {
    if(height !== _height) {
      editor.height(height);
      _height = height;
    }
  });

  async function initViewer() {
    if (!node) return;

    const Viewer = (await import('@toast-ui/editor/dist/toastui-editor-viewer')).default;
    const viewerOptions = { 
      ...options, 
      height, 
      initialValue 
    };

    Object.keys(defaultValueMap).forEach(key => {
      if (!viewerOptions[key]) {
        viewerOptions[key] = defaultValueMap[key];
      }
    });

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