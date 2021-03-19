<script type="text/ts">
  import { afterUpdate } from "svelte";
  let query = "predictions"
  const apiKey = "9692d1a17a814d86822248b3ee1b339d";
  export let stop: string;
  export let line: string;
  export let directionID: string;
  let headsign: string;
  $: apiURL = `https://api-v3.mbta.com/${query}?api_key=${apiKey}&filter[route]=${line}&filter[stop]=${stop}&filter[direction_id]=${directionID}&include=trip,stop`;
  // at some point, options for this query will be decided by user selections
  let object = {}; // want to avoid "data.data" later bc it's confusing
  let count = 0

  async function fetchPrediction(){
    const response = await fetch(apiURL);
    object = await response.json();
    count += 1
    directionID = "" // if we don't do this, first user change won't trigger fetchPrediction()

    // need to get headsign from trip included

    if(object.included){
      for(let i = 0; i<object.included.length; i++){
        if(object.included[i].type == "trip"){
          headsign = object.included[i].attributes.headsign;
          break;
          }
        }
      }
      console.log(object) // debug
      console.log(count)
    }

  $: if (directionID!=""){
    // we want to make an API call each time the direction ID is changed
    fetchPrediction()
  }
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
<!--TODO: Need to handle the case that where there are multple vehicles
          because right now, when there are multiple, stop becomes undefined
          note: data contains vehicle, but not headsign or st-->
  <div class="data-display">
    <h2>When will the next train arrive at my stop?</h2>
    <h3>Results: {object.data.length}</h3>
    {#each object.data as prediction}
      <div class="data-item">
        <h3>Line</h3>
        <p>{prediction.relationships.route.data.id}</p>
        {#each object.included as included}
          {#if included.type == "stop"}
            <h3>Stop</h3>
            <p>{included.attributes.name} </p>
          {/if}
        {/each}
          <h3>Direction</h3>
          <p>{headsign}</p>
          <!-- headsign = last stop on current route, depening on direction -->
        <h3>Arrival Time</h3>
        <p>{new Date(prediction.attributes.arrival_time).toTimeString()}</p>
        <!-- make time human readable -->
      </div>
    {/each}
  </div>

{/if}
