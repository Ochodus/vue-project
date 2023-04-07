<script setup>
import { createVNode, ref, render } from 'vue'

const textKey = ref(0)
const elString = ref("")

function getText(el) {
    elString.value = el
    textKey.value++
}

defineExpose({
    textKey,
    getText
})

</script>

<template>
    <div id = "text" :key="textKey">
        <span v-html="elString">

        </span>
    </div>
</template>

<style scoped>
#text {
    height: 100%;
}

.yellow-highlight {
    background-color: yellow;
}
</style>
