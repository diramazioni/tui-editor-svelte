<script lang="ts">
  import { run } from 'svelte/legacy';
  import { onMount } from 'svelte';
  import type { Editor as ToastEditor, EditorOptions } from '@toast-ui/editor';
  import '@toast-ui/editor/dist/toastui-editor.css';
  import { defaultValueMap } from './tui_defaults.js';

  interface EditorProps {
    initialValue?: string;
    options?: Partial<EditorOptions>;
    onload?: (e: any) => void;
    onchange?: (e: any) => void;
    onfocus?: (e: any) => void;
    onblur?: (e: any) => void;
  }

  let {
    initialValue = defaultValueMap.initialValue,
    options = {},
    onload = () => {},
    onchange = () => {},
    onfocus = () => {},
    onblur = () => {},
  }: EditorProps = $props() as EditorProps;
  
  let editor: ToastEditor | null  = $state();
  let node: HTMLElement | null  = $state();

  export function invoke(method: string, ...args: any[]): any {
    console.log('invoke', method, args);
      let result = null;
      if (editor[method]) {
        result = editor[method](...args);
        return result;
      } else {
        console.error(`Method ${method} does not exist on the editor instance.`);
      }
      return null;
  }

  export function getRootElement() {
    return node;
  }
  export function getEditor() {
    return editor;
  }

  async function initEditor(): Promise<void> {
    const editorModule = await import('@toast-ui/editor');
    const Editor = editorModule.default;
    // const { tableMergedCell } = Editor.plugin;
    // Cannot destructure property 'tableMergedCell' of 'Editor.plugin' as it is undefined.

    //const plugins = [tableMergedCell];
    // Merge default options with user options
    const editorOptions = { ...defaultValueMap, ...options, initialValue }; // plugins,
    // Map events to callback props
    editorOptions.events = {
      load: (args: any) => onload(args),
      change: (args: any) => onchange(args),
      focus: (args: any) => onfocus(args),
      blur: (args: any) => onblur(args)
    };
    // Inject the editor into the DOM
    editorOptions.el = node;
    editor = new Editor(editorOptions);
  }

  onMount(async () => {
    await initEditor();

    return () => {
      if (editor) {
        const editorEvents = ['load', 'change', 'focus', 'blur'];
        editorEvents.forEach(event => editor.off(event));
        editor.remove();
      }
    };
  });
</script>

<div bind:this={node}></div>