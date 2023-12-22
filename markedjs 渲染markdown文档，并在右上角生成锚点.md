## 背景
因为是markdown，所以用户可以随便写，像图一这种规规矩矩的标题 做成锚点很简单。
但是markdown里面 用户可以随便写，就会出现图二这种情况，这种情况下 antdv的anchor的带children嵌套的那种数组就不好实现了
<img width="221" alt="image" src="https://github.com/xjsdlla/study/assets/54897491/716a5d6c-b4c7-4661-bcfe-9183a5e17ec4">

<img width="217" alt="image" src="https://github.com/xjsdlla/study/assets/54897491/c0d0067a-fd4d-4d86-90d9-827192907692">

### 方案
因此我们选择 把所有的项放到同一级，然后给不同的标题加class，通过css来做成这种有间距的效果

<script setup>
import { marked } from 'marked'
import { onMounted, computed, ref } from 'vue'

const anchorLinkList = ref([])
const anchorIndex = ref(0)
const sidebarMainRef = ref(null);

// 文章内容
const content = computed(() => {
    const renderer = {
        heading(text, level) {
            const idName = level + '_' + anchorIndex.value++
            const className = 'level' + level
            anchorLinkList.value.push({
                level: level,
                title: text,
                key: className,
                href: '#' + idName
            })

            return `
                <h${level} id=${idName} class=${className}>
                    ${text}
                </h${level}>
            `
        }
    }
    marked.use({ renderer })

    marked.parse(detail.value.content)

})

const getContainer = () => {
    return this.$refs.sidebarMain;
}

onMounted(() => {
    sidebarMainRef.value = document.querySelector('.sidebar-main');
})
</script>


<template>
    <div class="sidebar-main" ref="sidebarMainRef">
        <div class="content" v-html="content"></div>
    </div>
    <a-anchor :getContainer="getContainer">
        <a-anchor-link
            v-for="link in anchorLinkList"
            :key="link.key"
            :href="link.href"
            :class="link.key"
        />
    </a-anchor>
    <mtd-anchor class="title-wrap" :offset-top="20" container="#marked-content">
      <mtd-anchor-link
        v-for="link in anchorLinkList"
        :href="link.href"
        :title="link.title"
        :key="link.key"
        :class="link.key"
      />
    </mtd-anchor>
</template>

<style scoped>
.level2 {
    text-indent: 10px;
}

.level3 {
    text-indent: 20px;
}

.level4 {
    text-indent: 30px;
}

.level5 {
    text-indent: 40px;
}

.level6 {
    text-indent: 50px;
}

.title-wrap {
    position: sticky;
    top: 80px;
    padding-right: 10px;
    height: 400px;
    overflow-y: auto;
}

#marked-content {
    height: 100vh;
    overflow-y: auto;
}
</style>

### 效果
规规矩矩的标题
<img width="1693" alt="image" src="https://github.com/xjsdlla/study/assets/54897491/1b75449d-f168-4ea5-a556-68a298dad0a2">

<img width="1690" alt="image" src="https://github.com/xjsdlla/study/assets/54897491/94d0975a-5c64-43a3-8437-6a266242f6f6">

