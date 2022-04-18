<!-- add a full sceen color background -->
<template>
<div class='basic' :style="{backgroundColor: color}">
</div>
</template>

<script>
import uniqolor from 'uniqolor';

export default {
    name: 'BasicColor',
    data() {
        return {
            color: '#000000'
        }
    },
    mounted() {
        this.color = String(uniqolor.random().color);
        console.log(this.color)
    },
    methods: {
        changeColor() {
            this.color = String(uniqolor.random().color);
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
