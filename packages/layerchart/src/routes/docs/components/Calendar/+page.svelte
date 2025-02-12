<script lang="ts">
  import { PeriodType, format } from 'svelte-ux';
  import { startOfYear, endOfYear } from 'date-fns';
  import { scaleThreshold } from 'd3-scale';
  import { range } from 'd3-array';
  import { schemeGreens } from 'd3-scale-chromatic';

  import Preview from '$lib/docs/Preview.svelte';

  import Chart, { Svg } from '$lib/components/Chart.svelte';
  import Calendar from '$lib/components/Calendar.svelte';
  import Group from '$lib/components/Group.svelte';
  import Text from '$lib/components/Text.svelte';
  import Tooltip from '$lib/components/Tooltip.svelte';
  import TooltipItem from '$lib/components/TooltipItem.svelte';

  import { createDateSeries } from '$lib/utils/genData';

  const now = new Date();
  const firstDayOfYear = startOfYear(now);
  const lastDayOfYear = endOfYear(now);

  const data = createDateSeries({ count: 365 * 4, min: 10, max: 100, value: 'integer' }).map(
    (d) => {
      return {
        ...d,
        value: Math.random() > 0.2 ? d.value : null, // set null for some values
      };
    }
  );
</script>

<h1>Examples</h1>

<h2>Responsive cell size (default)</h2>

<Preview {data}>
  <div class="h-[200px] p-4 border rounded">
    <Chart
      {data}
      x={(d) => d.date}
      r={(d) => d.value}
      rScale={scaleThreshold().unknown('transparent')}
      rDomain={[25, 50, 75]}
      rRange={schemeGreens[4]}
      tooltip={{ mode: 'manual' }}
      let:tooltip
    >
      <Svg>
        <Calendar start={firstDayOfYear} end={lastDayOfYear} {tooltip} monthPath />
      </Svg>

      <Tooltip header={(d) => format(d.date, PeriodType.Day)} />
    </Chart>
  </div>
</Preview>

<h2>Fixed cell size</h2>

<Preview {data}>
  <div class="h-[200px] p-4 border rounded overflow-hidden">
    <Chart
      {data}
      x={(d) => d.date}
      r={(d) => d.value}
      rScale={scaleThreshold().unknown('transparent')}
      rDomain={[25, 50, 75]}
      rRange={schemeGreens[4]}
      tooltip={{ mode: 'manual' }}
      let:tooltip
    >
      <Svg>
        <Calendar start={firstDayOfYear} end={lastDayOfYear} {tooltip} cellSize={16} monthPath />
      </Svg>

      <Tooltip header={(d) => format(d.date, PeriodType.Day)} />
    </Chart>
  </div>
</Preview>

<h2>Multiple Years</h2>

<Preview {data}>
  <div class="h-[716px] p-4 border rounded overflow-hidden">
    <Chart
      {data}
      x={(d) => d.date}
      r={(d) => d.value}
      rScale={scaleThreshold().unknown('transparent')}
      rDomain={[25, 50, 75]}
      rRange={schemeGreens[4]}
      padding={{ left: 20 }}
      tooltip={{ mode: 'manual' }}
      let:tooltip
    >
      <Svg>
        {#each range(2019, 2024) as year, i}
          {@const start = new Date(year, 0, 1)}
          {@const end = endOfYear(start)}
          <Group y={140 * i}>
            <Text
              value={year}
              class="text-xs"
              rotate={270}
              x={-20}
              y={(16 * 7) / 2}
              textAnchor="middle"
              verticalAnchor="start"
            />
            <Calendar {start} {end} {tooltip} cellSize={16} monthPath />
          </Group>
        {/each}
      </Svg>

      <Tooltip header={(d) => format(d.date, PeriodType.Day)} let:data>
        {#if data.value != null}
          <TooltipItem label="value" value={data.value} format="integer" valueAlign="right" />
        {/if}
      </Tooltip>
    </Chart>
  </div>
</Preview>
