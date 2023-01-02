<template lang="pug">
div
    h1 Welcome!
    button(@click="getData()") Click Me
    section
        div {{ result }}

</template>

<script lang="ts">
const url = 'https://api.waqi.info/feed/paris/?token=107e07356961086733ad81e14fb9e6a344aa7562'
export default {
    data() {
        return {
            result: null,
        }
    },
    methods: {
        async getData() {
            fetch(url)
            .then(response => response.json())
            .then(json => {
                if (json.status === 'ok') {
                    this.result = json.data;
                }             
            })
        }
    }
}
</script>