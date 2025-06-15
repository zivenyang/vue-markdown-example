<template>
    <div v-html="html"></div>
</template>
<script setup lang="ts">
import { computed, nextTick, onMounted, ref, watch } from 'vue'
import MarkdownIt from 'markdown-it'
import { tasklist } from "@mdit/plugin-tasklist"
import { full as emoji } from 'markdown-it-emoji'
import { ins } from "@mdit/plugin-ins";
import { sub } from "@mdit/plugin-sub";
import { sup } from "@mdit/plugin-sup";
import { mark } from "@mdit/plugin-mark";
import { footnote } from "@mdit/plugin-footnote";
import { dl } from "@mdit/plugin-dl";
import { abbr } from "@mdit/plugin-abbr";
import { container } from "@mdit/plugin-container";
import { plantuml } from "@mdit/plugin-plantuml";
import { alert } from "@mdit/plugin-alert";
import "@mdit/plugin-alert/style"
import { ruby } from "@mdit/plugin-ruby";
import { spoiler } from "@mdit/plugin-spoiler";
import "@mdit/plugin-spoiler/style"
import { tab } from "@mdit/plugin-tab";
import mathjax3 from "markdown-it-mathjax3";
import mermaid from 'mermaid';




const props = defineProps<{
    content: string
}>()

const mdIt = new MarkdownIt({
    html: true,
    linkify: true,
    typographer: true
})

mdIt.use(tasklist)
    .use(emoji)
    .use(sub)
    .use(sup)
    .use(ins)
    .use(mark)
    .use(footnote)
    .use(dl)
    .use(abbr)
    .use(container, { name: "warning" })
    .use(plantuml)
    .use(alert)
    .use(ruby)
    .use(spoiler)
    .use(tab, { name: "tabs" })
    .use(mathjax3)

const defaultFenceRenderer = mdIt.renderer.rules.fence!;
mdIt.renderer.rules.fence = (tokens, idx, options, env, self) => {
    const token = tokens[idx];
    const info = token.info ? token.info.trim() : '';

    // 如果代码块是 mermaid
    if (info === 'mermaid') {
        const content = token.content.trim();
        // 返回一个带有 "mermaid" class 的 <pre> 标签
        // Mermaid.js 将会找到这个标签并渲染它
        return `<pre class="mermaid">${content}</pre>`;
    }

    // 如果不是 mermaid，则使用默认的渲染器
    return defaultFenceRenderer(tokens, idx, options, env, self);
};


const html = computed(() => mdIt.render(props.content))

onMounted(() => {
    mermaid.initialize({
        // startOnLoad: false 是关键！我们希望手动控制渲染时机
        startOnLoad: false,
        // 可以自定义主题等
        theme: 'default',
    });
});

watch(
    html,
    async () => {
        // Vue 的 DOM 更新是异步的。
        // 我们必须等待 nextTick，确保 v-html 的内容已经实际插入到 DOM 中。
        await nextTick();

        // 手动调用 Mermaid API 来查找并渲染所有 .mermaid 元素
        // 这样做性能更好，因为它只在需要时运行
        try {
            await mermaid.run();
        } catch (e) {
            console.error("Mermaid rendering error:", e);
        }
    },
    {
        // 立即执行一次 watcher，以便在组件首次加载时也能渲染 Mermaid
        immediate: true,
    }
);

</script>