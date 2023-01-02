<template lang="pug">
div
    h1 Welcome!
    form
        //- label(for="location") Location:
        //- input#location(type="text", name="")
        label(for="threshold") Desired AQI Threshold:
        select(v-model="threshold")
            option(v-for="[key, value] in thresholds" :value="key") {{ value }}

    button(@click="getData()") Click Me
    section
        h1 {{ message }}
        h3 Today's AQI: {{ aqi }}
        //- div {{ result }}

</template>

<script lang="ts">
const url = 'https://api.waqi.info/feed/paris/?token=107e07356961086733ad81e14fb9e6a344aa7562';
const thresholdMap = new Map<number, string>([
    [50, 'Good'],
    [100, 'Moderate'],
])

export default {
    data() {
        return {
            thresholds: thresholdMap,
            threshold: 50,
            aqi: 0, // air quality indicator
            attributions: [],
            message: '',
            result: null,
        }
    },
    computed: {
        message(): string {
            if(!this.result) {
                return '';
            }
            return this.aqi < this.threshold ? 'You\'re in the clear!' : 'Air quality does not meet your threshold today!';
        }
    },
    methods: {
        async getData() {
            fetch(url)
            .then(response => response.json())
            .then(json => {
                if (json.status === 'ok') {
                    // if more of the response were used, would create response class 
                    // would also do more robust response checking
                    this.aqi = json.data.aqi;
                    
                    this.result = json.data;
                }             
            })
            .catch(() => {
                // would check status codes and do logging in a real app
                alert('Sorry, please try again later!')
            })
        }
    }
}
</script>

<style scoped>
</style>