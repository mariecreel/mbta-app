<script type="text/ts">
  import Mbtaapi from './mbta-api.svelte';
  const apiKey = "9692d1a17a814d86822248b3ee1b339d";

  $: line = "Green-B"; // these values are going to be used to make
  $: stop = "place-wrnst"; // predictions in a second component
  $: directionID = "1";
  $: props = {
    line: line,
    stop: stop,
    directionID: directionID
  }
  let fetchedLines = {};

  async function getStops(trainLine){
    /*
      async function getStops(trainLine: string):
      this function takes a string representing an MBTA subway line as input,
      then fetches the stops on that subway line and populates the stop select
      box based on the line selected by the user. If the stop select box has
      been populated with options already, those options are removed and
      replaced by the stop for the new line. In other words, the options of
      the select box with id = stop changes every time a new line is selected
      in the line select box.

      On the first time that a certain line is passed as input, an array of
      tuples containing the unique stop ID and stop Name is saved in an object
      called fetchedLines. The next time that line is sent as input, instead
      of making an API call, the stop selection box is populated by the
      information stored in the fetchedLines object. This is intended to avoid
      redundant API calls.
    */

    let selectStop = document.getElementById("stop");

    if(selectStop.children.length != 0){
      // want select list to clear every time we change lines
      while (selectStop.firstChild){
        selectStop.removeChild(selectStop.lastChild);
      }
    }

    let selectDirection = document.getElementById("direction");
    // grab first and last stop for direction selections
    // clear previous directions
    if(selectDirection.children.length !=0){
      while (selectDirection.firstChild){
        selectDirection.removeChild(selectDirection.lastChild);
      }
    }

    if(fetchedLines[trainLine]){ // if we saved the stops on this line already
      console.log("Seen line")
      for(let i = 0; i < fetchedLines[trainLine].length; i++){
          let option = document.createElement("option");
          option.value = fetchedLines[trainLine][i][0]; // stopID
          option.text = fetchedLines[trainLine][i][1]; // stopName
          selectStop.appendChild(option);
      }

    } else { // else we haven't fetched the stops for this line yet
      console.log("Unseen Line")
      let apiURLStops = `https://api-v3.mbta.com/stops?api_key=${apiKey}&filter[route]=${trainLine}`
      const response = await fetch(apiURLStops);
      let object = await response.json();

      fetchedLines[trainLine] = [];
      // list of tuples, first string is stopID, second is stopName

      for(let i=0; i<object.data.length; i++){
        // create an option for each stop
        let stopID: string = object.data[i].id;
        let stopName: string = object.data[i].attributes.name;
        let option = document.createElement("option");
        if(i == 0){
          let firstStop = document.createElement("option");
          firstStop.value = 0;
          firstStop.text = stopName;
          selectDirection.appendChild(firstStop);
        } else if(i == object.data.length-1){
          let lastStop = document.createElement("option");
          lastStop.value = 1;
          lastStop.text = stopName;
          selectDirection.appendChild(lastStop)
        }

        fetchedLines[trainLine].push([stopID,stopName])
        option.value = stopID
        option.text = stopName
        selectStop.appendChild(option)
      }
    }
  }

  function handleLine(event){
      if(this.value != ""){ // handling empty input at top of box
        line = this.value
        console.log(line)
        getStops(line)
      }
    }

  function handleStop(event){
    if(this.value != ""){
      stop = this.value;
      console.log(stop)
    }
  }
  function handleDirection(event){
    if (this.value!=""){
      directionID = this.value
      props = props;
      console.log(props)
    }
  }
</script>

<style>
  .select{
    width: 60%;
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
  }
  .selectBox{
    display:flex;
    flex-direction: column;
    margin: 5px;
  }
</style>

<div class="select">

  <div class="selectBox">
    <label for="line">Origin Line</label>
    <select name="line" id="line" form="line" on:input={handleLine}>
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
  </div>

  <div class="selectBox">
    <label for="stop">Origin Stop</label>
    <!-- this select list populated based on which line is chosen -->
    <select name="stop" id="stop" form="stop" on:input={handleStop}>
      <option disabled>Please select a line first</option>
    </select>
  </div>

  <div class="selectBox">
    <label for="direction">Trip Direction</label>
    <select name="direction" id="direction" form="direction" on:input={handleDirection}>
      <!-- this select list populated based on which line is chosen -->
    </select>
    </div>
</div>
<Mbtaapi {...props}/>
