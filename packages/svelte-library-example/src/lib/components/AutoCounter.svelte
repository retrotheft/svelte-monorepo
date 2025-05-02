<script lang="ts">
   import { JoinLoop } from "svelte-mainloop";

   let count = $state(0)

   let isRunning = $state(true)

   const update = (delta: number) => count += delta
</script>

<p>The count is {count.toFixed(1)}</p>

{#if isRunning}
   <JoinLoop {update} />
{/if}

<button onclick={() => isRunning = !isRunning}>{ isRunning ? 'Pause' : 'Play' }</button>
