<script lang="ts">
  import { spring } from 'svelte/motion';
  import { geoOrthographic, geoCentroid } from 'd3-geo';
  import { feature } from 'topojson-client';
  import { index } from 'd3-array';

  import { mdiPlay, mdiStop } from '@mdi/js';

  import { Button, scrollIntoView, cls, sortFunc, ButtonGroup, timerStore } from 'svelte-ux';

  import Preview from '$lib/docs/Preview.svelte';
  import Chart, { Canvas, Svg } from '$lib/components/Chart.svelte';
  import GeoPath from '$lib/components/GeoPath.svelte';
  import Graticule from '$lib/components/Graticule.svelte';
  import Tooltip from '$lib/components/Tooltip.svelte';
  import { timings } from './timings.js';

  export let data;

  const countries = feature(data.geojson, data.geojson.objects.countries);

  const springOptions = { stiffness: 0.04 };
  const yaw = spring(0, springOptions);
  const pitch = spring(0, springOptions);
  const roll = spring(0, springOptions);

  let selectedFeature;
  $: if (selectedFeature) {
    const centroid = geoCentroid(selectedFeature);
    $yaw = -centroid[0];
    $pitch = -centroid[1];
  }

  // Animate to Yakko's song
  // https://animaniacs.fandom.com/wiki/Yakko%27s_World_(song)#New_Updated_Verse
  // https://www.youtube.com/watch?v=BoaLSUKeGWw
  // https://www.youtube.com/watch?v=5pOFKmk7ytU

  const countryFeaturesByName = index(countries.features, (f) => f.properties.name);

  const countryTimings = Object.entries(timings).map(([timing, country], index) => {
    const [hours, minutes, seconds, milli] = timing.split(':');

    return {
      country,
      audioTime: +hours * 60 * 60 + +minutes * 60 + +seconds + +milli / 1000,
    };
  });

  // Set to jump to a country
  let currentIndex = -1;
  let isPlaying = false;

  $: if (isPlaying && $audioCurrentTime >= countryTimings[currentIndex + 1]?.audioTime) {
    const countryName = countryTimings[currentIndex + 1].country;
    selectedFeature = countryFeaturesByName.get(countryName);
    currentIndex += 1;
  }

  const audioFile = new Audio('/audio/yakko_world.mp3');
  audioFile.addEventListener('ended', () => stop());

  const audioCurrentTime = timerStore({
    initial: 0,
    delay: 100,
    onTick: () => audioFile.currentTime,
  });

  async function play() {
    isPlaying = true;
    audioFile.currentTime = currentIndex !== -1 ? countryTimings[currentIndex].audioTime : 0;
    audioFile.play();
  }

  function stop() {
    isPlaying = false;
    audioFile.pause();
    audioFile.currentTime = 0;
    currentIndex = -1;
    selectedFeature = null;
  }
</script>

<h1>Examples</h1>

<h2>SVG</h2>

<Preview data={countries}>
  <div class="h-[600px] grid grid-cols-[224px,1fr] relative">
    <div class="absolute top-0 right-0 z-10 flex items-center gap-3">
      {#if isPlaying && selectedFeature}
        <span class="text-sm px-2 py-1 font-semibold text-blue-500 bg-blue-50 rounded-full">
          {selectedFeature?.properties.name ?? ''}
        </span>
      {/if}
      <ButtonGroup variant="fill-light" color="blue" size="sm">
        <Button icon={mdiPlay} on:click={play} disabled={isPlaying} />
        <Button icon={mdiStop} on:click={stop} disabled={!isPlaying} />
      </ButtonGroup>
    </div>

    <div class="overflow-auto scrollbar-none">
      {#each countries.features.sort(sortFunc('properties.name')) as country}
        {@const isSelected = selectedFeature === country}
        <div use:scrollIntoView={{ condition: isSelected }}>
          <Button
            variant={isSelected ? 'fill-light' : 'default'}
            color={isSelected ? 'red' : 'default'}
            fullWidth
            on:click={() => (selectedFeature = country)}
          >
            {country.properties.name}
          </Button>
        </div>
      {/each}
    </div>
    <Chart
      geo={{
        projection: geoOrthographic,
        fitGeojson: countries,
        rotate: {
          yaw: $yaw,
          pitch: $pitch,
          roll: $roll,
        },
      }}
      tooltip={{ mode: 'manual' }}
      let:tooltip
    >
      <Svg>
        <GeoPath geojson={{ type: 'Sphere' }} class="fill-blue-300" />
        <Graticule class="stroke-black/20" />
        {#each countries.features as country}
          <GeoPath
            geojson={country}
            class={cls(
              'fill-white cursor-pointer',
              selectedFeature === country ? 'fill-red-400' : 'hover:fill-gray-200'
            )}
            on:click={(e) => (selectedFeature = country)}
            {tooltip}
          />
        {/each}
      </Svg>

      <Tooltip>
        <div slot="header" let:data>{data.properties.name}</div>
      </Tooltip>
    </Chart>
  </div>
</Preview>

<h2>Canvas</h2>

<Preview data={countries}>
  <div class="h-[600px] grid grid-cols-[224px,1fr] relative">
    <div class="absolute top-0 right-0 z-10 flex items-center gap-3">
      {#if isPlaying && selectedFeature}
        <span class="text-sm px-2 py-1 font-semibold text-blue-500 bg-blue-50 rounded-full">
          {selectedFeature?.properties.name ?? ''}
        </span>
      {/if}
      <ButtonGroup variant="fill-light" color="blue" size="sm">
        <Button icon={mdiPlay} on:click={play} disabled={isPlaying} />
        <Button icon={mdiStop} on:click={stop} disabled={!isPlaying} />
      </ButtonGroup>
    </div>

    <div class="overflow-auto scrollbar-none">
      {#each countries.features.sort(sortFunc('properties.name')) as country}
        {@const isSelected = selectedFeature === country}
        <div use:scrollIntoView={{ condition: isSelected }}>
          <Button
            variant={isSelected ? 'fill-light' : 'default'}
            color={isSelected ? 'red' : 'default'}
            fullWidth
            on:click={() => (selectedFeature = country)}
          >
            {country.properties.name}
          </Button>
        </div>
      {/each}
    </div>
    <Chart
      geo={{
        projection: geoOrthographic,
        fitGeojson: countries,
        rotate: {
          yaw: $yaw,
          pitch: $pitch,
          roll: $roll,
        },
      }}
    >
      <Canvas>
        <GeoPath geojson={{ type: 'Sphere' }} fill="#93c5fd" />
      </Canvas>
      <Canvas>
        <Graticule stroke="rgba(0,0,0,.20)" />
      </Canvas>
      <Canvas>
        <GeoPath geojson={countries} fill="white" />
      </Canvas>
      <Canvas>
        <GeoPath geojson={selectedFeature} fill="#f87171" />
      </Canvas>
    </Chart>
  </div>
</Preview>
