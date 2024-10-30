<script>
  import { run } from 'svelte/legacy';

  import { onMount, onDestroy, createEventDispatcher } from 'svelte'
  // import 'codemirror/lib/codemirror.css';
  import '@toast-ui/editor/dist/toastui-editor.css';
  import { editorEvents, defaultValueMap } from './tui_defaults.js';

	
	
  
  /**
   * @typedef {Object} Props
   * @property {string} [height]
   * @property {string} [initialValue]
   * @property {object} [options]
   */

  /** @type {Props} */
  let { height = defaultValueMap.height, initialValue = defaultValueMap.initialValue, options = {} } = $props();

  let editor = $state(), node = $state(), _height = $state(height);
  const emit = createEventDispatcher();

  /**
   * @returns {Node}
   */
  export function getRootElement() {
    return node;
  }

  run(() => {
    if(height !== _height) {
      editor.height(height);
      _height = height
    }
  });

  async function init(node, options) {
    const Viewer = (await import('@toast-ui/editor/dist/toastui-editor-viewer')).default;
    options = { ...options, height, initialValue };
    Object.keys(defaultValueMap).forEach(key => {
      if (!options[key]) {
        options[key] = defaultValueMap[key];
      }
    });
    options.events = editorEvents.reduce((events, event) => (events[event] = (...args) => emit(event, args), events), {});
    options.el = node;
    editor = new Viewer(options);

    return {
      destroy() {
        editorEvents.forEach(event => editor.off(event));
        editor.remove();
      }
    }

  }
</script>
<div bind:this={node} use:init={options}></div>
