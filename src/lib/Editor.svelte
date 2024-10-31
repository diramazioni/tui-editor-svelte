<script lang="ts">
  import { run } from 'svelte/legacy';
  import { onMount } from 'svelte';
  import type { Editor as ToastEditor, EditorOptions } from '@toast-ui/editor';
  import '@toast-ui/editor/dist/toastui-editor.css';
  // color piker
  import 'tui-color-picker/dist/tui-color-picker.css';
  import '@toast-ui/editor-plugin-color-syntax/dist/toastui-editor-plugin-color-syntax.css';
  // table merged cell
  import '@toast-ui/editor-plugin-table-merged-cell/dist/toastui-editor-plugin-table-merged-cell.css';
  // code highlight 
  import 'prismjs/themes/prism.css';
  import '@toast-ui/editor-plugin-code-syntax-highlight/dist/toastui-editor-plugin-code-syntax-highlight.css';
  import Prism from 'prismjs';
  // chart
  import '@toast-ui/chart/dist/toastui-chart.css';

  import { defaultValueMap } from './tui_defaults.js';
  import { defaultChartOptions } from './chart_defaults.js';

  interface EditorProps {
    initialValue?: string;
    options?: Partial<EditorOptions>;
    pluginsOn?: string[];
    chartOptions?: ChartOptions;
    onload?: (e: any) => void;
    onchange?: (e: any) => void;
    onfocus?: (e: any) => void;
    onblur?: (e: any) => void;
  }
  interface ChartOptions {
    width?: number | string;
    height?: number | string;
    minWidth?: number;
    minHeight?: number;
    maxWidth?: number;
    maxHeight?: number;
  }

  let {
    initialValue = defaultValueMap.initialValue,
    options = {},
    pluginsOn = [],
    chartOptions = {},
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
    const Editor = (await import('@toast-ui/editor')).default;
    // Plugins setup
    let plugins:any[] = [];
    // @toast-ui/editor-plugin-color-syntax
    if (pluginsOn.includes('colorSyntax')) {
      const colorSyntax = (await import('@toast-ui/editor-plugin-color-syntax')).default;
      plugins.push(colorSyntax);
    }
    // @toast-ui/editor-plugin-table-merged-cell
    if (pluginsOn.includes('tableMergedCell')) {
      const tableMergedCell = (await import('@toast-ui/editor-plugin-table-merged-cell')).default;
      plugins.push(tableMergedCell);
    } 
    // @toast-ui/editor-plugin-code-syntax-highlight
    if (pluginsOn.includes('codeSyntaxHighlight')) {
      const codeSyntaxHighlight = (await import('@toast-ui/editor-plugin-code-syntax-highlight')).default;
      plugins.push([codeSyntaxHighlight, { highlighter: Prism }] );
    }
    // @toast-ui/editor-plugin-chart
    if (pluginsOn.includes('chart')) {
      const chart = (await import('@toast-ui/editor-plugin-chart')).default;
      const mergedChartOptions = { ...defaultChartOptions, ...chartOptions };
      plugins.push([chart, mergedChartOptions]);
    }
    // @toast-ui/editor-plugin-uml
    if (pluginsOn.includes('uml')) {
      const uml = (await import('@toast-ui/editor-plugin-uml')).default;
      plugins.push(uml);
    }
    // Merge default options with user options
    const editorOptions = { ...defaultValueMap, ...options, plugins, initialValue }; 
    console.log('editorOptions', editorOptions);
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