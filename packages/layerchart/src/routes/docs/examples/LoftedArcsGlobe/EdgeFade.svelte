<script lang="ts">
  import { getContext } from 'svelte';
  import { scaleLinear } from 'd3-scale';
  import { geoDistance } from 'd3-geo';

  import { geoContext } from '$lib/components/GeoContext.svelte';

  export let link: { source: [number, number]; target: [number, number] };

  const geo = geoContext();
  const { width, height } = getContext('LayerCake');

  const fade = scaleLinear().domain([-0.1, 0]).range([0, 0.1]);
  const clamper = scaleLinear().domain([0, 1]).range([0, 1]).clamp(true);

  // $: center = $geo.invert([$width / 2, $height / 2]);
  $: center = $geo.invert($geo.translate());
  $: source = link.source;
  $: target = link.target;
  $: startDistance = 1.57 - geoDistance(source, center);
  $: endDistance = 1.57 - geoDistance(target, center);
  $: distance = startDistance < endDistance ? startDistance : endDistance;
  $: opacity = clamper(fade(distance));
</script>

<g {opacity}>
  <slot />
</g>
