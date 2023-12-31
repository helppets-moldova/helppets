<template>
    <MainView v-if="content" class="shelters">
        <LeftNav v-if="isPC" :links="content.shelters"/>
        <div class="shelters_shelter">
            <div class="shelters_shelter_header">
                <h2>{{ shelterInfo.title }}</h2>
                <div class="shelters_shelter_header_media">
                    <template v-for="link in shelterInfo.social">
                        <div v-if="link.link" class="shelters_shelter_header_social">
                            <router-link :to="link.link">
                                <img :src="`/help_shelters_project/icons/${ link.icon }`"/>
                            </router-link>
                        </div>
                    </template>
                </div>
            </div>
            <div class="shelters_shelter_title">
                <span class="shelters_shelter_title">{{ shelterInfo.amount }}</span>
                <span class="shelters_shelter_number">{{ shelterInfo.number }}</span>
            </div>
            <div class="shelters_shelter_title" v-html="shelterInfo.workers"/>
            <div class="shelters_shelter_title" v-html="shelterInfo.textLarge"/>
            <div class="shelters_shelter_title" v-html="shelterInfo.contacts"/>
            <div class="shelters_shelter_title" v-html="shelterInfo.bankDetails"/>

            <div class="shelters_shelter_banks">
                <ToggleOpenList class="shelters_shelter_banks" :elements="shelterInfo.bankDetailsArr" :opened-blocks-id="blocksId[0]"/>
            </div>

            <div class="shelters_shelter_gallery">
                <swiper
                    :spaceBetween="30"
                    :slidesPerView="mainStore.isPC ? 3 : 1"
                    :autoplay="{
                        delay: 3000,
                        disableOnInteraction: false,
                    }"
                    :pagination="{
                        clickable: true,
                    }"
                    :navigation="true"
                    class="shelters_shelter_gallery_swiper"
                >
                    <swiper-slide v-for="(imageName, index) in images" :key="index">
                        <img :src="`/help_shelters_project/images/${imageName}`" alt="Image" class="shelters_shelter_gallery_image"/>
                    </swiper-slide>
                </swiper>
            </div>
            <div class="shelters_shelter_title" v-html="shelterInfo.location"/>
            <div class="shelters_shelter_location">
                <div v-html="shelterInfo.map"/>
                <div class="shelters_shelter_info">
                    <div class="shelters_shelter_info_title">{{ shelterInfo.needs }}</div>
                    <ul>
                        <li v-for="elem in shelterInfo.needsArr">{{ elem }}</li>
                    </ul>
                </div>
            </div>
        </div>
    </MainView>
    <Footer/>
</template>

<script lang="ts">
    import { ref, watch } from 'vue'
    import { useMainStore } from '@/store/main'
    import { useRoute } from 'vue-router'
    import MainView from '@/views/MainView.vue'
    import LeftNav from '@/components/common/LeftNav.vue'
    import { useSheltersStore } from '@/store/shelters'
    import Footer from '@/components/Footer.vue'
    import ToggleOpenList from '@/components/common/ToggleOpenList.vue'

    export default {
        components: { ToggleOpenList, Footer, LeftNav, MainView },
        setup () {
            const sheltersStore = useSheltersStore()
            const mainStore = useMainStore()
            const route = useRoute()
            const shelterId = route.params.id ?? '0'

            const images = ['center_banner.jpg', 'left_banner.jpg', 'right_banner.jpg', 'right_banner.jpg']

            const isPC = ref(mainStore.isPC)
            let content = ref(sheltersStore.content[route.query.lang])
            let shelterInfo = ref(content.value.shelters && content.value.shelters.find(shelter => {
                return shelter.id.toString() === shelterId
            }))

            watch(() => route.query.lang, () => {
                content.value = sheltersStore.content[route.query.lang]
            })
            watch(() => route.params.id, () => {
                shelterInfo.value = content.value.shelters && content.value.shelters.find(shelter => {
                    return shelter.id.toString() === route.params.id
                })
            })

            const blocksId = shelterInfo.value.bankDetailsArr.map((elem) => elem.id)

            return {
                content,
                mainStore,
                shelterInfo,
                isPC,
                route,
                images,
                blocksId
            }
        }
    }
</script>

<style lang="scss" scoped>
.shelters {
  display: flex;

  &_shelter {
    margin: 0 50px;
    width: 90%;

    @include mobileOrTablet {
      margin-left: 0;
    }

    &_header {
      display: flex;
      justify-content: space-between;
      &_media {
        display: flex;
        justify-content: space-between;
        align-items: center;
      }

      &_social {
        width: 20px;
        height: 20px;
        margin-right: 15px;
      }
    }

    & > div {
      margin-bottom: 25px;

      &::v-deep(span:first-child) {
        font-size: 18px;
        font-weight: 500;
        color: $main-green;
      }
    }

    &_banks {
      margin-bottom: 20px;
    }

    &_gallery {
      height: 400px;

      @include mobileOrTablet {
        height: 300px;
      }

      &_swiper {
        height: 100%;
        border-radius: 4px;

        :deep(*) {
          span {
            background: $white;
          }

          color: $white;
        }
      }
      &_image {
        width: 100%;
        height: 100%;
      }

    }
    &_location {
      display: flex;
      justify-content: space-between;
      margin-right: 20%;
      @include mobile {
        display: block;
      }
    }
    &_info {
      margin-left: 30px;
      @include mobile {
        margin: 20px 0;
      }
      &_title {
        font-size: 16px;
        font-weight: 500;
        color: $main-green;
      }
    }
  }
}
</style>