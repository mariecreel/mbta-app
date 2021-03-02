<script type="text/ts">
  import { onMount } from "svelte";
  const apiKey = "9692d1a17a814d86822248b3ee1b339d";

  async function getStops(trainLine){
    let apiURLStops = `https://api-v3.mbta.com/stops?api_key=${apiKey}&filter[route]=${trainLine}`
    const response = await fetch(apiURLStops);
    let object = await response.json();
    let selectStop = document.getElementById("stop")

    if(selectStop.children.length != 0){
      // want select list to clear every time we change lines
      while (selectStop.firstChild){
        selectStop.removeChild(selectStop.lastChild)
      }
    }

    for(let i=0; i<object.data.length; i++){
      // create an option for each stop
      let option = document.createElement("option");
      option.value = object.data[i].id
      option.text = object.data[i].attributes.name
      selectStop.appendChild(option)
    }
  }

  function getLine(event){
      if(this.value != ""){ // handling empty input at top of box
        let line = this.value
        getStops(line)
      }
    }
</script>

<style>
  .selectBox{
    width: 60%;
    margin: 0 auto;
  }
</style>

<div class="selectBox">
  <label for="line">Which line?</label>
  <select name="line" id="line" form="line" on:input={getLine}>
    <option></option> <!-- before adding this, selecting blue did nothing
                           unless you selected another line, which would
                           probably be really annoying as a user-->
    <option value="Blue">Blue</option>
    <option value="Green-B">Green Line - B</option>
    <option value="Green-C">Green Line - C</option>
    <option value="Green-D">Green Line - D</option>
    <option value="Orange">Orange</option>
    <option value="Red">Red</option>
  </select>
  <label for="stop">Which stop? (select line first)</label>
  <!-- this select list populated based on which line is chosen using an API call -->
  <select name="stop" id="stop" form="stop">

    <option disabled>Please select a line first</option>
  </select>
  <label for="direction">Which direction?</label>
  <select name="direction" id="direction" form="direction">
    <!-- this select list populated based on which line is chosen using an API
         call...want to make the text more descriptive but depends on line
         chosen by user. use dummy values 0 and 1 to test API calls tho -->
    <option value="0">0</option>
    <option value="1">1</option>
  </select>
</div>
