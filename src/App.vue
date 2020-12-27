<template>
    <Harp id="harp" />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Harp from './components/Harp.vue';

export default defineComponent({
    name: 'App',
    components: {
        Harp,
    },
});
</script>

<style scoped>
#harp {
    height: 1000px;
    width: 100%;
}
</style>
