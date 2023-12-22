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