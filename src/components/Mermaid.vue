<script setup lang="ts">
import { ref, onMounted, watch, nextTick } from 'vue';
import mermaid from 'mermaid';

const diagram = ref(`
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
`);

onMounted(() => {
    mermaid.initialize({ startOnLoad: false });
});

// copilot create: debounce only run after 2s of no changes
const debounce = (fn, delay) => {
    let timeoutID = null;
    return (...args) => {
        clearTimeout(timeoutID);
        timeoutID = setTimeout(() => {
            fn(...args);
        }, delay);
    };
};

watch(diagram, async (newVal) => {
    await nextTick();
    debounce(drawDiagram, 1000)();
});

// @see https://mermaid.js.org/config/usage.html
const drawDiagram = async function () {
    const element = document.querySelector('.mermaid');
    const { svg } = await mermaid.render('graphDiv', diagram.value);
    element.innerHTML = svg;
};
</script>

<template>
    <textarea name="mermaid-textarea" id="mermaid-textarea" v-model="diagram" rows="10" cols="30">
    </textarea>
    <div>
        <div class="mermaid" v-html="diagram"></div>
    </div>
</template>
