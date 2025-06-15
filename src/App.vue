<script setup lang="ts">
import MarkdownItRender from './components/MarkdownItRender.vue'
import { ref } from 'vue'

// 由于直接导入 .md 文件会报错（找不到模块），这里我们使用 fetch 异步加载 markdown 文件内容
const content = ref('加载中...')

const baseUrl = import.meta.env.VITE_BASE_URL || '/'
fetch(`${baseUrl}markdown-it-example.md`)
  .then(res => {
    if (!res.ok) {
        throw new Error(`HTTP error! status: ${res.status}`)
    }
    return res.text()
  })
  .then(text => {
    content.value = text
  })
  .catch(err => {
    content.value = '加载 Markdown 文件失败: ' + err.message
  })
</script>

<template>
  <main>
    <MarkdownItRender :content="content" />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
