<template>
    <div v-html="html"></div>
</template>
<script setup lang="ts">
import { ref } from 'vue'
import MarkdownIt from 'markdown-it'

const props = defineProps<{
    content: string
}>()

const md = new MarkdownIt()

const html = ref(md.render(props.content))
</script>