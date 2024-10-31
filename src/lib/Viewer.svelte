<script lang="ts">
  import { run } from 'svelte/legacy';
  import { onMount, createEventDispatcher } from 'svelte';
  import type { Viewer as ToastEditor, EditorOptions } from '@toast-ui/editor';
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

  import { defaultTuiOptions } from './tui_defaults.js';
  import { defaultChartOptions } from './chart_defaults.js';
  
  interface ViewerProps {
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
    initialValue = defaultTuiOptions.initialValue, 
    options = {}, 
    pluginsOn = [],
    chartOptions = {},    
    onload = () => {},
 
  }: ViewerProps = $props() as ViewerProps;

  let editor = $state();
  let node = $state();

  /**
   * @returns {Node}
   */
  export function getRootElement() {
    return node;
  }
  export function getEditor() {
    return editor;
  }


  async function initViewer() {

    const Viewer = (await import('@toast-ui/editor/dist/toastui-editor-viewer')).default;
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
    const viewerOptions = { ...defaultTuiOptions, ...options, plugins, initialValue };

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