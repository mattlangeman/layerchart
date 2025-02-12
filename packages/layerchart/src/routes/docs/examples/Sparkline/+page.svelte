<script lang="ts">
  import { scaleTime } from 'd3-scale';
  import { format } from 'date-fns';

  import Chart, { Svg } from '$lib/components/Chart.svelte';
  import Highlight from '$lib/components/Highlight.svelte';

  import Spline from '$lib/components/Spline.svelte';
  import Tooltip from '$lib/components/Tooltip.svelte';
  import TooltipItem from '$lib/components/TooltipItem.svelte';

  import Preview from '$lib/docs/Preview.svelte';
  import { createDateSeries } from '$lib/utils/genData';

  const data = createDateSeries({ count: 50, min: 50, max: 100, value: 'integer' });
</script>

<h1>Examples</h1>

<h2>Basic</h2>
<Preview {data}>
  <div>
    <div class="w-[125px] h-[18px]">
      <Chart {data} x="date" xScale={scaleTime()} y="value">
        <Svg>
          <Spline class="stroke-1 stroke-accent-500" />
        </Svg>
      </Chart>
    </div>
  </div>
</Preview>

<h2>Basic within a paragraph</h2>
<Preview {data}>
  <div>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam pretium, ligula ac sollicitudin
      ullamcorper, leo justo pretium tellus, at gravida ex quam et orci.
      <span class="w-[125px] h-[18px] inline-block">
        <Chart {data} x="date" xScale={scaleTime()} y="value">
          <Svg>
            <Spline class="stroke-1 stroke-accent-500" />
          </Svg>
        </Chart>
      </span> Sed ipsum justo, facilisis id tempor hendrerit, suscipit eu ipsum. Mauris ut sapien quis
      nibh volutpat venenatis. Ut viverra justo varius sapien convallis venenatis vel faucibus urna.
    </p>
  </div>
</Preview>

<h2>Basic zero axis</h2>
<Preview {data}>
  <div class="w-[125px] h-[20px] inline-block">
    <Chart {data} x="date" xScale={scaleTime()} y="value" yDomain={[0, null]}>
      <Svg>
        <Spline class="stroke-1 stroke-accent-500" />
      </Svg>
    </Chart>
  </div>
</Preview>

<h2>With Tooltip and Highlight</h2>
<Preview {data}>
  <div class="w-[125px] h-[25px]">
    <Chart {data} x="date" xScale={scaleTime()} y="value" tooltip>
      <Svg>
        <Spline class="stroke-1 stroke-accent-500" />
        <Highlight points={{ r: 3, class: 'stroke-none' }} />
      </Svg>
      <Tooltip
        class="text-xs"
        contained={false}
        header={(data) => format(data.date, 'eee, MMM do')}
        let:data
      >
        <TooltipItem label="value" value={data.value} />
      </Tooltip>
    </Chart>
  </div>
</Preview>

<h2>With Tooltip and Highlight (fixed position)</h2>
<Preview {data}>
  <div class="w-[125px] h-[25px]">
    <Chart {data} x="date" xScale={scaleTime()} y="value" tooltip let:containerWidth>
      <Svg>
        <Spline class="stroke-1 stroke-accent-500" />
        <Highlight points={{ r: 3, class: 'stroke-none' }} />
      </Svg>

      <Tooltip
        class="text-xs"
        contained={false}
        y={-3}
        x={containerWidth + 8}
        variant="none"
        let:data
      >
        <div class="whitespace-nowrap">
          {format(data.date, 'eee, MMM do')}
        </div>
        <div class="font-semibold">
          {data.value}
        </div>
      </Tooltip>
    </Chart>
  </div>
</Preview>

<h2>Within a paragraph with Tooltip and Highlight</h2>
<Preview {data}>
  <div>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam pretium, ligula ac sollicitudin
      ullamcorper, leo justo pretium tellus, at gravida ex quam et orci.
      <span class="w-[125px] h-[18px] inline-block">
        <Chart {data} x="date" xScale={scaleTime()} y="value" tooltip let:containerHeight>
          <Svg>
            <Spline class="stroke-1 stroke-accent-500" />
            <Highlight points lines />
          </Svg>
          <Tooltip
            class="text-xs"
            contained={false}
            header={(data) => format(data.date, 'eee, MMM do')}
            y={containerHeight + 4}
            xOffset={0}
            let:data
          >
            <TooltipItem label="value" value={data.value} />
          </Tooltip>
        </Chart>
      </span> Sed ipsum justo, facilisis id tempor hendrerit, suscipit eu ipsum. Mauris ut sapien quis
      nibh volutpat venenatis. Ut viverra justo varius sapien convallis venenatis vel faucibus urna.
    </p>
  </div>
</Preview>
