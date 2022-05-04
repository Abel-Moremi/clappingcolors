<!-- add a full sceen color background -->
<template>
<div class='basic' :style="{backgroundColor: color}">
</div>
</template>

<script>
import uniqolor from 'uniqolor';
import  '@tensorflow/tfjs';
import * as speech from '@tensorflow-models/speech-commands';
import '@tensorflow/tfjs-backend-webgl';

export default {
    name: 'BasicColor',
    data() {
        return {
            color: '#000000'
        }
    },
    mounted() {
        this.color = String(uniqolor.random().color);
        this.init();
    },
    methods: {
        changeColor() {
            this.color = String(uniqolor.random().color);
        },
        async loadModel() {
            const URL = 'https://teachablemachine.withgoogle.com/models/pq8jo4Xsj/'
            const checkpointURL = URL + 'model.json';
            const metadataURL = URL + 'metadata.json';

            const recognizer = speech.create(
                "BROWSER_FFT", // fourier transform type, not useful to change
                undefined, // speech commands vocabulary feature, not useful for your models
                checkpointURL,
                metadataURL
            );

            // check that model and metadata are loaded via HTTPS requests.
            await recognizer.ensureModelLoaded();
            console.log(recognizer.wordLabels());

            return recognizer;
        },
        async init() {
            const recognizer = await this.loadModel();
            const classLabels = recognizer.wordLabels(); // get class labels

            // listen() takes two arguments:
            // 1. A callback function that is invoked anytime a word is recognized.
            // 2. A configuration object with adjustable fields
            recognizer.listen(result => {
                const scores = result.scores; // probability of prediction for each class
                // render the probability scores per class
                for (let i = 0; i < classLabels.length; i++) {
                    const classPrediction = classLabels[i] + ": " + scores[i].toFixed(2);
                    console.log(classPrediction);

                    // if clap is detected, change the color
                    if (i == 1 && scores[i] > 0.8) {
                        this.changeColor();
                    }
                }
            }, {
                includeSpectrogram: true, // in case listen should return result.spectrogram
                probabilityThreshold: 0.75,
                invokeCallbackOnNoiseAndUnknown: true,
                overlapFactor: 0.50 // probably want between 0.5 and 0.75. More info in README
            });

            // Stop the recognition in 5 seconds.
            // setTimeout(() => recognizer.stopListening(), 5000);
        }
    }
}
</script>

<!-- add background colour -->
<style scoped>
.basic {
    height: 100vh;
}
</style>
