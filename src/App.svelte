<script>
	import SelectStops from './select-stops.svelte'
</script>

<main>
	<h1>MBTA Arrival Predictions</h1>
</main>
<SelectStops/>

<style lang="scss">

	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
		font-family:	Helvetica;
	}

	h1 {
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
