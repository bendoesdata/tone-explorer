<script>
	import Synth from "./Synth.svelte"

	let synthType = "poly";
	
</script>



<main>
	
	<div class="intro">
	<h1>Tone.js Explorer</h1><span>Explore the instruments in the Tone.js API and easily copy or save the settings as a JSON object.</span>
	</div>

	<span style="padding-left: 10px">Choose an instrument: </span>
	<select bind:value={synthType} style="margin: 10px">
			<option value="poly">
				PolySynth
			</option>
			<option value="fm">
				FMSynth
			</option>
			<option value="am">
				AMSynth
			</option>
			<option value="mono">
				MonoSynth
			</option>
			<option value="duo">
				DuoSynth
			</option>
			<option value="metal">
				MetalSynth
			</option>
			<option value="membrane">
				MembraneSynth
			</option>
			<option value="noise">
				NoiseSynth
			</option>
			<option value="pluck">
				PluckSynth
			</option>
	</select>
	
	{#if synthType == "poly"}
		<Synth stype={synthType} />
	{:else if synthType == "fm"}
		<Synth stype={synthType} />
	{:else if synthType == "am"}
		<Synth stype={synthType} />
	{:else if synthType == "mono"}
		<Synth stype={synthType} />
	{:else if synthType == "duo"}
		<Synth stype={synthType} />
	{:else if synthType == "membrane"}
		<Synth stype={synthType} />
	{:else if synthType == "metal"}
		<Synth stype={synthType} />
	{:else if synthType == "pluck"}
		<Synth stype={synthType} />
	{:else}
		<Synth stype={"poly"} />
	{/if}

	<!-- <Synth stype={"fm"} /> -->
	
	
		<!-- <input type=number bind:value={reverbSettings.decay} min=0 max=10> -->
		
</main>

<style>
	.intro h1 {
		display: inline-block;
		padding-left: 10px
	}

	.intro span {
		font-size: 14px;
		margin-left: 10px;
		color: gray;
	}
</style>