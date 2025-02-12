<script lang="ts">
  import { Button, Tooltip, cls } from 'svelte-ux';

  import {
    mdiArrowULeftTop,
    mdiMagnifyPlusOutline,
    mdiMagnifyMinusOutline,
    mdiImageFilterCenterFocus
  } from '@mdi/js';
  import type Zoom from '$lib/components/Zoom.svelte';

  type Placement =
    | 'top-left'
    | 'top'
    | 'top-right'
    | 'left'
    | 'center'
    | 'right'
    | 'bottom-left'
    | 'bottom'
    | 'bottom-right';

  export let zoom: Zoom;
  export let placement: Placement | undefined = 'top-right';
  export let orientation: 'horizontal' | 'vertical' = 'vertical';
</script>

<div
  class={cls(
    'bg-black/5 rounded-full m-1 backdrop-blur z-10 flex',
    orientation === 'vertical' && 'flex-col',
    {
      'top-left': 'absolute top-0 left-0',
      top: 'absolute top-0 left-1/2 -translate-x-1/2',
      'top-right': 'absolute top-0 right-0',
      left: 'absolute top-1/2 left-0 -translate-y-1/2',
      center: 'absolute top-1/2 left-1/2 -translate-x-1/2  -translate-y-1/2',
      right: 'absolute top-1/2 right-0 -translate-y-1/2',
      'bottom-left': 'absolute bottom-0 left-0',
      bottom: 'absolute bottom-0 left-1/2 -translate-x-1/2',
      'bottom-right': 'absolute bottom-0 right-0'
    }[placement]
  )}
>
  <Tooltip title="Zoom in">
    <Button
      icon={mdiMagnifyPlusOutline}
      on:click={() => zoom.increase()}
      class="text-black/50 p-2"
    />
  </Tooltip>
  <Tooltip title="Zoom out">
    <Button
      icon={mdiMagnifyMinusOutline}
      on:click={() => zoom.decrease()}
      class="text-black/50 p-2"
    />
  </Tooltip>
  <Tooltip title="Center">
    <Button
      icon={mdiImageFilterCenterFocus}
      on:click={() => zoom.translateCenter()}
      class="text-black/50 p-2"
    />
  </Tooltip>
  <Tooltip title="Reset">
    <Button icon={mdiArrowULeftTop} on:click={() => zoom.reset()} class="text-black/50 p-2" />
  </Tooltip>
</div>
