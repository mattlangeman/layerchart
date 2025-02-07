<script lang="ts">
  import { NavItem } from 'svelte-ux';
  import { mdiFormatListBulleted, mdiHome, mdiPlayCircle } from '@mdi/js';

  import { page } from '$app/stores';

  const examples = {
    'Cartesian & Polar': [
      'Arc',
      'Area',
      'AreaStack',
      { label: 'Bar Chart (Vertical)', value: 'Columns' },
      { label: 'Bar Chart (Horizontal)', value: 'Bars' },
      'Candlestick',
      'DotPlot',
      'Histogram',
      'Line',
      'Oscilloscope',
      'PunchCard',
      'Scatter',
      'Sparkbar',
      'Sparkline',
      'Threshold',
    ],
    'Hierarchy & Graph': ['Pack', 'Partition', 'Sankey', 'Sunburst', 'Tree', 'Treemap'],
    Geo: [
      'GeoPath',
      'GeoPoint',
      'GeoTile',
      { label: 'Projections', value: 'GeoProjection' },
      'StateMap',
      'CountryMap',
      'Choropleth',
      'BubbleMap',
      'SpikeMap',
      'ZoomableMap',
      'ZoomableTileMap',
      'AnimatedGlobe',
      'LoftedArcsGlobe',
      'GeojsonPreview',
      'ShapefilePreview',
    ],
  };

  const components = {
    Common: ['Chart', 'Axis', 'Frame', 'Legend', 'Rule'],
    Primatives: ['Arc', 'Circle', 'Group', 'Line', 'Point', 'Rect', 'Text'],
    'Data-driven': [
      'Area',
      'AreaStack',
      'Bars',
      'Calendar',
      'Labels',
      'Link',
      'Pie',
      'Points',
      'Spline',
      'Threshold',
    ],
    Interactions: ['Tooltip', 'TooltipContext', 'Highlight', 'Zoom'],
    Geo: ['GeoContext', 'GeoPath', 'GeoPoint', 'GeoTile', 'Graticule', 'TileImage'],
    Hierarchy: ['Pack', 'Partition', 'Sankey'],
    Clipping: ['ClipPath', 'ChartClipPath', 'CircleClipPath', 'RectClipPath'],
    Other: ['Blur', 'Bounds', 'ColorRamp', 'LinearGradient', 'MotionPath', 'Pattern'],
  };

  const utils = ['pivot'];
</script>

<NavItem text="Home" icon={mdiHome} currentUrl={$page.url} path="/" />
<NavItem
  text="Getting Started"
  icon={mdiPlayCircle}
  currentUrl={$page.url}
  path="/getting-started"
/>
<NavItem text="Changelog" icon={mdiFormatListBulleted} currentUrl={$page.url} path="/changelog" />

<h1>Examples</h1>
{#each Object.entries(examples) as [header, items]}
  <h2>{header}</h2>
  {#each items as item}
    {#if typeof item === 'object'}
      <NavItem
        text={item.label}
        currentUrl={$page.url}
        path="/docs/examples/{item.value}"
        class="pl-6 py-2"
      />
    {:else}
      <NavItem
        text={item.replace(/([a-z])([A-Z])/g, '$1 $2')}
        currentUrl={$page.url}
        path="/docs/examples/{item}"
        class="pl-6 py-2"
      />
    {/if}
  {/each}
{/each}

<h1>Components</h1>
{#each Object.entries(components) as [header, items]}
  <h2>{header}</h2>
  {#each items as item}
    {#if typeof item === 'object'}
      <NavItem
        text={item.label}
        currentUrl={$page.url}
        path="/docs/components/{item.value}"
        class="pl-6 py-2"
      />
    {:else}
      <NavItem
        text={item}
        currentUrl={$page.url}
        path="/docs/components/{item}"
        class="pl-6 py-2"
      />
    {/if}
  {/each}
{/each}

<h1>Utils</h1>
{#each utils as item}
  <NavItem text={item} currentUrl={$page.url} path="/docs/utils/{item}" class="pl-6 py-2" />
{/each}
