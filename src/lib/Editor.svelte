<script>
  import { run } from 'svelte/legacy';
  import { onMount } from 'svelte';
  import '@toast-ui/editor/dist/toastui-editor.css';
  import { defaultValueMap } from './tui_defaults.js';
  
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
    onload = () => {},
    onchange = () => {},
    onfocus = () => {},
    onblur = () => {},
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

  let editor = $state();
  let node = $state();
  let _previewStyle = $state(previewStyle);
  let _height = $state(height);

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

  async function initEditor() {
    if (!node) return;
    
    const Editor = (await import('@toast-ui/editor')).default;
    const editorOptions = { 
      ...options, 
      previewStyle, 
      height, 
      initialEditType, 
      initialValue 
    };

    Object.keys(defaultValueMap).forEach(key => {
      if (!editorOptions[key]) {
        editorOptions[key] = defaultValueMap[key];
      }
    });

    // Map events to callback props
    editorOptions.events = {
      load: (...args) => onload(...args),
      change: (...args) => onchange(...args),
      focus: (...args) => onfocus(...args),
      blur: (...args) => onblur(...args)
    };

    editorOptions.el = node;
    editor = new Editor(editorOptions);
  }

  onMount(async () => {
    await initEditor();

    return () => {
      if (editor) {
        //editorEvents.forEach(event => editor.off(event));
        editor.remove();
      }
    };
  });
</script>

<div bind:this={node}></div>