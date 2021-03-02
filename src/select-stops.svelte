<script type="text/ts">
  import { onMount } from "svelte";
  import apiKey from './mbta-api.svelte';
  // export let route: string;
  // export let stop: string;
  // export let direction_id: string;
  // need a list of each stop id (not ideal, prob better in a server?)
  // TODO: implement hash table of arrays containing stops as strings
  let stopsByLine = {};

  async function getStops(line: string){
    const response = await fetch(`https://api-v3.mbta.com/stops/?filter[route]=${line}&api_key=${apiKey}`);
    const stops = await response.json();
    console.log(stops);
    return stops;
  }

  function submitForm(value: string){
    let line = value;
    console.log("in submitForm", line);
  }

</script>

<form id="line" on:submit|preventDefault={submitForm(this.value)}>
  <label for="line">Which line?</label>
  <select name="line" id="line" form="line">
    <option value="Blue">Blue</option>
    <option value="Green-A">Green Line - A</option>
    <option value="Green-B">Green Line - B</option>
    <option value="Green-C">Green Line - C</option>
    <option value="Green-D">Green Line - D</option>
    <option value="Orange">Orange</option>
    <option value="Red">Red</option>
  </select>
  <input type="submit">
</form>
