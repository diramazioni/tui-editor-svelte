<script>
  import { run } from 'svelte/legacy';

  //import 'codemirror/lib/codemirror.css';
  import '@toast-ui/editor/dist/toastui-editor.css';
  import { editorEvents, defaultValueMap } from './tui_defaults.js';
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();
	
  
  /**
   * @typedef {Object} Props
   * @property {string} [previewStyle]
   * @property {string} [height]
   * @property {string} [initialEditType]
   * @property {string} [initialValue]
   * @property {object} [options]
   */

  /** @type {Props} */
  let {
    previewStyle = defaultValueMap.previewStyle,
    height = defaultValueMap.height,
    initialEditType = defaultValueMap.initialEditType,
    initialValue = defaultValueMap.initialValue,
    options = {},
    load = false,
    change = false,
    focus = false,
    blur = false,
  } = $props();
  /**
   *
   * @param method {string}
   * @param args {any}
   * @returns {any}
   */
  export function invoke(method, ...args) {
      let result = null;
      if (editor[method]) {
        result = editor[method](...args);
      }
      return result;
  }
  /**
   * @returns {Node}
   */
  export function getRootElement() {
    return node;
  }

  let editor = $state(), node = $state(), _previewStyle = $state(previewStyle), _height = $state(height);

  run(() => {
    if(previewStyle !== _previewStyle) {
      editor.changePreviewStyle(previewStyle);
      _previewStyle = previewStyle
    }
    if(height !== _height) {
      editor.height(height);
      _height = height
    }
  });

  async function init(node, options) {
    const Editor = (await import('@toast-ui/editor')).default;
    options = { ...options, previewStyle, height, initialEditType, initialValue };
    Object.keys(defaultValueMap).forEach(key => {
      if (!options[key]) {
        options[key] = defaultValueMap[key];
      }
    });
    // const editorEvents = ['load', 'change', 'stateChange', 'focus', 'blur'];
    options.events = Object.fromEntries(
    editorEvents.map(event => [
        event,
        (...args) => dispatch(event, args)
      ])
    );
    options.el = node;
    editor = new Editor(options);
  }

  $effect(() => {  
    return () => {
      editorEvents.forEach(event => editor.off(event));
      editor.remove();
    }
  })
</script>
<div bind:this={node} use:init={options}></div>
