<template>
    <MainView v-if="content" class="footer">
        <p class="footer_text">{{ content.footer.line_one }}</p>
        <p class="footer_text">{{ content.footer.line_two }}</p>
    </MainView>
</template>

<script lang="ts">
    import { ref, watch } from 'vue'
    import { useMainStore } from '@/store/main'
    import { useMainPageStore } from '@/store/mainPage'
    import { useRoute } from 'vue-router'
    import MainView from '@/views/MainView.vue'

    export default {
        components: { MainView },
        setup () {
            const mainPageStore = useMainPageStore()
            const mainStore = useMainStore()
            const route = useRoute()
            let content = ref(mainPageStore.content[route.query.lang])
            watch(() => route.query.lang, () => {
                content.value = mainPageStore.content[route.query.lang]
            })
            return {
                content,
                mainStore

            }
        }
    }
</script>

<style lang="scss" scoped>
.footer {
  border-top: 4px solid $main-green;
  padding-top: 10px;
  &_text {
    font-size: 10px;
    margin: 0;
    padding: 3px 0;
  }
}
</style>