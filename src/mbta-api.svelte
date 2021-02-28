<script type="text/ts">
  import { onMount } from "svelte";

  let query = "predictions" // in the future, prob change to routes / predictions
  let route="Green-B";
  let stop="place-wrnst"; // Warren st
  let direction="1" //towards Park Street
  const apiKey = "9692d1a17a814d86822248b3ee1b339d";
  const apiURL = `https://api-v3.mbta.com/${query}?api_key=${apiKey}&filter[route]=${route}&filter[stop]=${stop}&filter[direction_id]=${direction}&include=vehicle,trip,stop`;
  // at some point, options for this query will be decided by user selections
  let object = {}; // want to avoid "data.data" later bc it's confusing

  onMount(async function(){
    const response = await fetch(apiURL);
    object = await response.json();
    console.log(object)
  })
</script>

<style type="text/css">
  .data-display{
    max-width: 60%;
    margin: 0 auto;
  }
  .data-item{
    border-style: solid;
    border-width: medium;
    border-radius: 5px 5px 5px 5px;
    margin: 5px;
    padding: 5px;
    max-width: 50%;
  }
</style>

{#if typeof object.data !== 'undefined'}
<!--
  Need to wait until object is fetched and assigned or this will fail...
  I think this is true because of the onMount function, which runs
  *after* the component is first rendered, not before. so svelte will try
  to render this component before the information is received if we don't
  have a condition here for the undefined (i.e. not fetched yet) case.
-->
  <div class="data-display">
    <h2>When will the next train arrive at my stop?</h2>
    <h3>Results: {object.data.length}</h3>
    {#each object.data as prediction}
      <div class="data-item">
        <h3>Line: {prediction.relationships.route.data.id}</h3>
        <h3>Stop: {object.included[0].attributes.description} </h3>
        <h3>Direction: {object.included[1].attributes.headsign}</h3>
        <!-- headsign = last stop on current route, depening on direction -->
        <h3>Arrival Time: {new Date(
                          prediction.attributes.arrival_time
                          ).toTimeString()}</h3>
                          <!-- make time human readable -->
      </div>
    {/each}
  </div>

{/if}
