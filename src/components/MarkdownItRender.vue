<template>
    <div v-html="html"></div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue'
import MarkdownIt from 'markdown-it'

const props = defineProps<{
    content: string
}>()

const md = new MarkdownIt()

const html = computed(() => md.render(props.content))

</script>