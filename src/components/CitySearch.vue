<template lang="pug">
div
    h1 Welcome! Check your Air Quality today!
    form(@submit.prevent="getData()")
        label(for="location") City:
            input#location(type="text", v-model="location" @keyup.enter="getData()" required)
        label(for="threshold") Desired AQI Threshold:
            select(v-model="threshold")
                option(v-for="[key, value] in thresholds" :value="key") {{ value }}

        button(type="submit") Search
    section
        h1 {{ message }}
        h3(v-if="status === 'ok'") Today's AQI: {{ aqi }}

</template>

<script lang="ts">
const BASE_URL = 'https://api.waqi.info/feed/{city}/?token=107e07356961086733ad81e14fb9e6a344aa7562';
const thresholdMap = new Map<number, string>([
    [50, 'Good'],
    [100, 'Moderate'],
])

export default {
    data() {
        return {
            location: '',
            thresholds: thresholdMap,
            threshold: 50,
            aqi: 0, // air quality indicator
            attributions: [],
            status: null,
        }
    },
    computed: {
        message(): string {
            if (this.status === 'ok') {
                return this.aqi < this.threshold ? 'You\'re in the clear!' : 'Air quality does not meet your threshold today!';
            } else if (this.status === 'error') {
                return 'Sorry, that is not a valid city!';
            }
            
            return '';
        }
    },
    methods: {
        async getData() {
            this.status = null;
            const url = BASE_URL.replace('{city}', this.location);
            fetch(url)
            .then(response => response.json())
            .then(json => {
                this.status = json.status;
                if (this.status === 'ok') {
                    // if more of the response were used, would create response class 
                    // would also do more robust response checking
                    this.aqi = json.data.aqi;
                } else {
                    console.log('error msg:' + json.data);
                }             
            })
            .catch((response) => {
                // would check status codes and do external logging to sentry in a real app
                console.log(response.json());
            })
        }
    }
}
</script>

<style scoped>
</style>