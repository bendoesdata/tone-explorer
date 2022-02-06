<script>
	let nodeRef;
    
    export let stype;

    import * as Tone from 'tone';
    
        let key;
        let keyCode;
        let synth;
        let noteLength = 2;

        switch (stype) {
            case "poly":
                synth = new Tone.PolySynth();
                break;
            case "fm":
                synth = new Tone.FMSynth();
                break;
            case "am":
                synth = new Tone.AMSynth();
                break;
            case "mono":
                synth = new Tone.MonoSynth();
                break;
            case "duo":
                synth = new Tone.DuoSynth();
                break;
            case "metal":
                synth = new Tone.MetalSynth();
                break;
            case "membrane":
                synth = new Tone.MembraneSynth();
                break;
            case "noise":
                synth = new Tone.NoiseSynth();
                break;
            case "pluck":
                synth = new Tone.PluckSynth();
                break;
            default:
                synth = new Tone.PolySynth();
        }
    
        const keyboardToMidi = {
            90: "C4",
            88: "D4",
            67: "E4",
            86: "F4",
            66: "G4",
            78: "A4",
            77: "B4",
            188: "C5"
        }

        const orderedNotes = [
            90,
            88,
            67,
            86,
            66,
            78,
            77,
            188];

        const keyboardKeys = ["z", "x", "c", "v", "b", "n", "m", ","]
    
        const envParams = ["attack", "decay", "sustain", "release"];
    
        let waves = ["sine", "sawtooth", "triangle", "square"];

        const attackReleaseCurveOpts = [
    "linear",
    "exponential",
    "sine",
    "cosine",
    "bounce",
    "ripple",
    "step"
];

        const decayCurveOpts = ["linear", "exponential"]
    
        let synthSettings = {
        volume: 0,
        detune: 0,
        portamento: 0,
        envelope: {
            attack: 0.1,
            "attackCurve": "linear",
            decay: 0.5,
            decayCurve: "exponential",
            release: 2,
            releaseCurve: "exponential",
            sustain: 1
        },
        oscillator: {
            partialCount: 0,
            partials: [],
            phase: 0,
            type: "sine"
        }
    }
        
    
        let reverbSettings = {
            decay: 5,
            wet: 0.01,
            preDelay: 0.2
        }
        
        function loadSynth() {
            
            synth.set({
                oscillator: {
                    type: synthSettings.oscillator.type
                }
            })
    
            // beed to reduce values so they are between 0 and 1
            synth.set({
                volume: synthSettings.volume,
                detune: synthSettings.detune,
                portamento: synthSettings.portamento,
                envelope : {
                attack :  synthSettings.envelope.attack,
                decay :  synthSettings.envelope.decay,
                sustain : synthSettings.envelope.sustain,
                release : synthSettings.envelope.release,
                attackCurve : synthSettings.envelope.attackCurve,
                decayCurve : synthSettings.envelope.decayCurve,
                releaseCurve : synthSettings.envelope.releaseCurve,
            }
            })
    
            reverb.set({
                decay: reverbSettings.decay,
                wet: reverbSettings.wet,
                preDelay: reverbSettings.preDelay
            })
        }
    
        // Setup a reverb with ToneJS
        const reverb = new Tone.Reverb({
            decay: reverbSettings.decay,
            wet: reverbSettings.wet,
            preDelay: reverbSettings.preDelay
        });
    
        // var feedbackDelay = new Tone.PingPongDelay('8n',  0.85);
        synth.connect(reverb);
        // feedbackDelay.connect(reverb);
        reverb.connect(Tone.Master);
        loadSynth();
    
    
        // document.getElementById("json").textContent = JSON.stringify(data, undefined, 2);
    
        function handleKeydown(event) {
            key = event.key;
            keyCode = event.keyCode;
    
            let note = keyboardToMidi[keyCode];
    
            if (note != null) {
                synth.triggerAttackRelease(note, noteLength+"n");
            }
            console.log("note-"+keyCode)
    
            document.getElementById("note-"+keyCode).classList.add("active-note"); 
    
            setTimeout(() => {
                let els = document.querySelectorAll(".note");
                // console.log(els.classList)
                for (var i = 0; i < els.length; i++) {
                    els[i].classList.remove("active-note"); 
                }
                // document.getElementById("note-"+keyCode)
                // document.getElementById("note-"+keyCode).classList.add("white"); 
            }, 1000);
        }
    
        $: formattedJson = JSON.stringify(synthSettings, undefined, 2);
    </script>

<svelte:window on:keydown={handleKeydown}/>
<div id="keyboard">
    <span style="font-size: 12px; color: gray;">Play with keys: </span>
    {#each orderedNotes as n,i}
        <div class="note" id={"note-"+n}>
            {keyboardKeys[i]}
        </div>
    {/each}
    <div class="active-note"></div>
</div>

    <div class="row">
        <div class="col">
            <div class="header">
                <h3>CONTROL</h3>
            </div>
            <div>
            <label for="note-length">
                <span class="range-value"> {(noteLength)+"n"}</span>
                <input name="note-length" class="range" type=range step=1 bind:value={noteLength} min=1 max=8>
                <span class="range-label">note length</span>
            </label>
            <label for="volume-range" >
                <span class="range-value">{(synthSettings.volume).toFixed(1)}</span>
                <input name="volume-range" class="range" type=range step=1 bind:value={synthSettings.volume} on:change={loadSynth} min=-10 max=5>
                <span class="range-label">volume</span>
            </label>
            <label for="detune-range">
                <span class="range-value">{(synthSettings.detune).toFixed(1)}</span>
                <input name="detune-range" class="range" type=range step=1 bind:value={synthSettings.detune} on:change={loadSynth} min=0 max=100>
                <span class="range-label">detune</span>
            </label>
            <label for="portamento-range">
                <span class="range-value">{(synthSettings.portamento).toFixed(1)}</span>
                <input name="portamento-range" class="range" type=range step=0.1 bind:value={synthSettings.portamento} on:change={loadSynth} min=0 max=5>
                <span class="range-label">portamento</span>
            </label>
        </div>
        </div>

        <div class="col">
            <div class="header">
                <h3>OSCILLATOR</h3>
            <div>
                <select bind:value={synthSettings.oscillator.type} on:change={loadSynth}>
                    {#each waves as w}
                        <option value={w}>
                            {w}
                        </option>
                    {/each}
                </select>
            </div>
                <h3>CURVES</h3>
                <div>
                <select bind:value={synthSettings.envelope.attackCurve} on:change={loadSynth}>
                    {#each attackReleaseCurveOpts as op}
                        <option value={op}>
                            {op}
                        </option>
                    {/each}
                </select>
                <select bind:value={synthSettings.envelope.releaseCurve} on:change={loadSynth}>
                    {#each attackReleaseCurveOpts as op}
                        <option value={op}>
                            {op}
                        </option>
                    {/each}
                </select>
                <select bind:value={synthSettings.envelope.decayCurve} on:change={loadSynth}>
                    {#each decayCurveOpts as op}
                        <option value={op}>
                            {op}
                        </option>
                    {/each}
                </select>
            </div>
            </div>
        </div>
	
        <div class="col">
            <div class="header">
            <h3>ENVELOPE</h3>
        </div>
            {#each envParams as param}
                {#if param == "release"}
                <label>
                    <span class="range-value">{(synthSettings.envelope[param]).toFixed(1)}</span>
                    <input class="range" type=range step=0.5 bind:value={synthSettings.envelope[param]} on:change={loadSynth} min=1 max=5>
                    <span class="range-label">{param}</span>
                </label>
                {:else if param == "sustain"}
                <label>
                    <span class="range-value">{(synthSettings.envelope[param]).toFixed(1)}</span>
                    <input class="range" type=range step=0.1 bind:value={synthSettings.envelope[param]} on:change={loadSynth} min=0.1 max=2>
                    <span class="range-label">{param}</span>
                </label>
                {:else}
                <label>
                    <span class="range-value">{(synthSettings.envelope[param]).toFixed(2)}</span>
                    <input class="range"type=range step=0.01 bind:value={synthSettings.envelope[param]} on:change={loadSynth} min=0.01 max=2>
                    <span class="range-label">{param}</span>
                </label>
                {/if}
            {/each}
        </div>
	
        <div class="col">
            <div class="header">
                <h3 style="margin-bottom: 4px">JSON OUTPUT</h3>
                <span style="font-style: italic; margin-top: 0px; font-size: 10px">After creating new Tone, pass this into synth.set() method</span>
                <br>
            </div>
            <pre id="json-output">{formattedJson}</pre>
        </div>
    </div>

    <div class="row">
        <div class="col">
            <div class="header">
                <h3>REVERB</h3>
            </div>
            <div>
                <label for="reverb-range" >
                    <span class="range-value">{(reverbSettings.wet).toFixed(2)}</span>
                    <input name="reverb-range" class="range" type=range step=0.01 bind:value={reverbSettings.wet} on:change={loadSynth} min=0 max=1>
                    <span class="range-label">dry/wet</span>
                </label>
                <label for="reverb-range" >
                    <span class="range-value">{(reverbSettings.decay).toFixed(1)}</span>
                    <input name="reverb-range" class="range" type=range step=0.1 bind:value={reverbSettings.decay} on:change={loadSynth} min=0 max=15>
                    <span class="range-label">decay</span>
                </label>
                <label for="reverb-range" >
                    <span class="range-value">{(reverbSettings.preDelay).toFixed(2)}</span>
                    <input name="reverb-range" class="range" type=range step=0.01 bind:value={reverbSettings.preDelay} on:change={loadSynth} min=0 max=5>
                    <span class="range-label">predelay</span>
                </label>
            </div>
        </div>
        <div class="col">
            <div class="header">
                <h3>DELAY</h3>
            </div>
            <div>
                coming soon...
            </div>
        </div>
    </div>
<style>
    #keyboard {
        max-width: 600px;
        float: right;
        margin-right: 20px;
    }
    .header {
        height: 50px;
    }
    pre {
		text-align: left;
		max-width: 500px;
		margin: 0 auto;
		background-color: rgb(36, 36, 36);
		padding: 20px
	}

	.note {
		display: inline-block;
		border: 1px solid gray;
		height: 40px;
		width: 20px;
		padding: 2px;
        margin-left: 20px;
        text-align: center;
        padding-top: 5px;
		color: black;
		background-color: white;
		-webkit-transition: background-color 0.2s linear;
		-moz-transition: background-color 0.2s linear;
		-ms-transition: background-color 0.2s linear;
		-o-transition: background-color 0.2s linear;
		transition: background-color 0.2s linear;
		border-radius: 20px
	}

	.white {
		background-color: red;
		transition: background-color 1.5s ease;
	}

    select {
        display: block;
        margin-top: 20px;
        width: 100%;
        background-color: gray;
    }

    .range-value {
    position: relative;
    display: block;
    text-align: center;
    font-size: 1.5em;
    color: rgb(177, 177, 177);
    font-weight: 400;
    }
    .range-label {
    position: relative;
    display: block;
    text-align: center;
    font-size: 14px;
    color: rgb(160, 160, 160);
    font-weight: 400;
    margin-bottom: 15px
    }
    .range {
    width: 100%;
    height: 10px;
    -webkit-appearance: none;
    background: #111;
    outline: none;
    border-radius: 15px;
    border: solid 1px rgb(75, 75, 75);
    overflow: hidden;
    box-shadow: inset 0 0 5px rgba(0, 0, 0, 1);
    
    }
    .range::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 15px;
    height: 15px;
    border-radius: 50%;
    background: #ffffff;
    cursor: pointer;
    border: 4px solid #333;
    box-shadow: -407px 0 0 400px #ffffff;
    }

	.active-note {
		background-color: gray;
		/* transition: background-color 0.5s ease; */
		/* color: red; */
	}

    @media (max-width: 900px) {
	#keyboard {
		float: none
	}

    .note {
        height: 30px;
		width: 10px;
		padding: 2px;
        margin-left: 5px;
        font-size: 12px;
    }
}
</style>