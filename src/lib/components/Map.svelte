<script>
  import { geoMercator, geoPath } from 'd3'
  import { draw } from 'svelte/transition'

  import Tooltip from './Tooltip.svelte'
  import geoJson from '../../lib/data/europe.json'

  let width = 500
  let height = 500

  let hoveredCountry = null
  let positionX = 0
  let positionY = 0
  let isDirectionLeft = false

  const margin = { top: 10, right: 10, bottom: 10, left: 10 }

  $: innerHeight = height - margin.top - margin.bottom
  $: innerWidth = width - margin.left - margin.right

  $: projection = geoMercator().fitSize([innerWidth, innerHeight], geoJson)
  $: pathGenerator = geoPath(projection)

  $: countries = geoJson.features.map(feature => {
    return {
      ...feature,
      path: pathGenerator(feature),
      id: feature.properties['ISO2'],
    }
  })

  /**
   * Position the tooltip and determine its direction
   * @param {MouseEvent} event - The hover event
   */
  function positionTooltip(event) {
    positionX = event.layerX
    positionY = event.layerY

    if (positionX + 200 > width) {
      isDirectionLeft = true
    } else {
      isDirectionLeft = false
    }
  }

  /**
   * Set the active country when hovering over it
   * @param {MouseEvent} event - The hover event
   * @param {Object} properties - The properties of the hovered country
   */
  function setActiveCountry(event, properties) {
    if (!properties) {
      hoveredCountry = null
    } else {
      positionTooltip(event)
      hoveredCountry = properties
    }
  }
</script>

<div
  class="chart-container"
  bind:clientHeight={height}
  bind:clientWidth={width}
>
  {#if hoveredCountry}
    <Tooltip
      name={hoveredCountry.NAME}
      abbreviation={hoveredCountry.ISO3}
      {positionX}
      {positionY}
      {isDirectionLeft}
    />
  {/if}
  <h1>Ceci n'est pas l'Europe</h1>
  <svg {width} {height}>
    <!-- MAP -->
    <g transform="translate({margin.left}, {margin.top})">
      {#each countries as { path, id, properties }, i}
        <path
          in:draw={{ delay: i * 20 }}
          d={path}
          on:mouseenter={event => setActiveCountry(event, properties)}
          on:mouseleave={event => setActiveCountry(event, null)}
          on:mousemove={positionTooltip}
        />
      {/each}
    </g>
  </svg>
</div>

<style>
  .chart-container {
    --base-color: rgb(41, 41, 41); --chart-color: rgb(172, 186, 228);

    height: 100vh;
    width: 100vw;

    & h1 {
      position: absolute;
      top: 5%;
      left: 15%;
      color: var(--base-color);
    }

    & path {
      cursor: help;
      stroke: var(--base-color);
      stroke-width: 0.5;
      opacity: 0.6;
      fill: var(--chart-color);

      &:hover {
        opacity: 1;
      }
    }
  }
</style>
