<script type="text/ts">
  import { onMount } from "svelte";

  let query = "alerts"
  const apiKey = "9692d1a17a814d86822248b3ee1b339d";
  const apiURL = `https://api-v3.mbta.com/${query}?api_key=${apiKey}&sort=description&filter%5Bactivity%5D=BOARD%2CEXIT%2CRIDE&filter%5Broute_type%5D=0&filter%5Broute%5D=Orange,Blue,Green,Red,Silver`;
  let object = {};

  onMount(async function(){
    const response = await fetch(apiURL);
    object = await response.json();
    console.log(object.data)
  })

</script>

{#if typeof object.data !== 'undefined'}
<!--Need to wait until object is fetched and assigned or this will fail-->
  <div class="data-display">
    {#each object.data as alert}
      <div class="data-item">
        <h2>Alert: {alert.attributes.effect}</h2>
        <h3>Service Effect: {alert.attributes.service_effect}</h3>
        <h3>Cause: {alert.attributes.cause}</h3>
        <p>{alert.attributes.header}</p>
        <p>{alert.attributes.description}</p>
      </div>
    {/each}
  </div>
{/if}
