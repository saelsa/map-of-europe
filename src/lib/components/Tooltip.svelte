<script>
  import { fade } from 'svelte/transition'

  export let name
  export let abbreviation
  export let positionX = 0
  export let positionY = 0
  export let isDirectionLeft = false
</script>

<div
  class="card"
  style="top:{positionY}px;left:{positionX}px;"
  transition:fade={{ delay: 100, duration: 200 }}
  class:left={isDirectionLeft}
>
  <p>{name} ({abbreviation})</p>
</div>

<style>
  .card {
    position: absolute;
    background-color: white;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    padding: 5px;
    margin: 0;
    display: flex;
    z-index: 1;
    transform: translate(10%, 0%);

    &.left {
      transform: translateX(-105%);
    }
  }
</style>
