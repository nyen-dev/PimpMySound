<template>
 <div>
 </div>
</template>

<script>
import {EventBus} from '../main.js';
var midiMap = new Map();
const midiStates = [
  //Match the number with the button array indices
  { id: 48, property: 'volumeLeft' },  
  { id: 51, property: 'volumeRight' },  
  { id: 64, property: 'crossfader' },
  { id: 18, property: 'speedLeft' },
  { id: 21, property: 'speedRight' },  
  { id: 19, property: 'playLeft' },
  { id: 20, property: 'stopLeft' },
  { id: 31, property: 'playRight' },
  { id: 32, property: 'stopRight' },
  { id: 3, property: 'filechooser'},
  { id: 16, property: 'sendLeft'},
  { id: 17, property: 'sendRight'},

   { id: 6, property: 'to-filter-lowshelf-Freq-1'},
  { id: 7, property: 'to-filter-lowshelf-Gain-1'},
  { id: 8, property: 'to-filter-lowshelf-Freq-2'},
   { id: 9, property: 'to-filter-lowshelf-Gain-2'},
  { id: 10, property: 'to-filter-highshelf-Freq-1'},
  { id: 11, property: 'to-filter-highshelf-Gain-1'},
  { id: 12, property: 'to-filter-highshelf-Freq-2'},
   { id: 13, property: 'to-filter-highshelf-Gain-2'}, 

   
];

midiStates.forEach(entry => midiMap.set(entry.id,entry.property))

// window.console.log(midiMap.get(48));

export default {
    name: 'InputManager',
    created() {
    navigator.requestMIDIAccess().then(this.onMidiDevice.bind(this));
    },

    methods: {
        onMidiDevice(access) {
            const inputs = access.inputs.values();

            for (const input of inputs) {
                // window.console.log(input);
                input.onmidimessage = this.onMidiEvent.bind(this);
            }
        },

        onMidiEvent(event) {
            let cmd = event.data[0] >> 4;
            let btnID = event.data[1];
            let btnValue = event.data[2];

            // window.console.log(`cmd: ${cmd}, btnID: ${btnID}, value: ${btnValue}`);
            if(cmd === 11 )
                EventBus.$emit('midi-' + midiMap.get(btnID), {cmd: cmd, btnID: btnID, btnValue: btnValue});
            else if ( cmd === 8 && (btnValue === 0 || btnValue === 127))
                EventBus.$emit('midi-' + midiMap.get(btnID), {cmd: cmd, btnID: btnID, btnValue: btnValue});
        },
    }
}
</script>

<style scoped>

#freq {
    margin-left: 10%
}

#qual {
    margin-left: 10%;
    margin-right: 10%
}

#gain {
    margin-right: 10%
}

.sliderContainer {
   width: 100%;
   height: 50%;
   float: left;
   margin-left: 2%;
   margin-right: 2%; 
}

.sliderLabel {
    text-align: center;
}

.slider:hover {
    opacity: 1;
}

.slider {
    -webkit-appearance: none;
    margin-top: .5em;
    width: 100%;
    height: 15px;
    border-radius: 5px;  
    background: #d3d3d3;
    outline: none;
    opacity: 0.7;
    -webkit-transition: .2s;
    transition: opacity .2s;
}

.slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 25px;
    height: 25px;
    border-radius: 50%; 
    background: rgb(143, 15, 58);
    cursor: pointer;
}
</style>

