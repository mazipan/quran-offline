<template>
  <section class="container">
    <div class="detail">
      <SurahHeader
        :surah-number="Number(currentSurah.number)"
        :surah-name="currentSurah.name"
        :surah-latin="currentSurah.name_latin"
        :surah-translation="currentSurah.translations.id.name" />

      <div class="detail__content">
        <SingleVerse
          :verse="currentSurah.text[String(verseId)]"
          :verse-index="String(verseId)"
          :surah-id="surahId"
          :translations="currentSurah.translations"
          :tafsir="currentSurah.tafsir" />
      </div>

      <VerseNavigation
        :surah-id="surahId"
        :verse-id="verseId"
        :verse-count="Number(currentSurah.number_of_ayah)"
        :on-change-verse="onChangeVerse" />
    </div>
  </section>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'
import { State, Mutation } from 'vuex-class'

import SingleVerse from '../../../components/SingleVerse.vue'
import SurahHeader from '../../../components/SurahHeader.vue'
import VerseNavigation from '../../../components/VerseNavigation.vue'

import { __isNotNull } from '../../../utils/index'

@Component({
  components: {
    SingleVerse,
    SurahHeader,
    VerseNavigation
  },
  async asyncData ({ params }) {
    const respDetail = await import(`~/static/data/surah/${params.surahid}.json`)
    return {
      currentSurah: respDetail[params.surahid]
    }
  }
})

export default class VerseDetailPage extends Vue {
  loading = true

  @State settingActiveTheme
  @Mutation setHeaderTitle
  @Mutation setPage

  get metaHead () {
    // @ts-ignore: Unreachable code error
    const title = `Baca Qur'an Ayat ke-${this.verseId} Surat ${this.currentSurah.name_latin} | Qur'an Web`
    // @ts-ignore: Unreachable code error
    const description = `Baca Qur'an, Terjemahan Bahasa Indonesia dan Tafsir Ayat ke-${this.verseId} Surat ${this.currentSurah.name_latin} Berdasarkan Data dari Kemenag`

    return {
      title,
      meta: [
        { hid: 'description', name: 'description', content: description },

        { hid: 'og:title', property: 'og:title', content: title },
        { hid: 'twitter:title', name: 'twitter:title', content: title },
        {
          hid: 'theme-color',
          name: 'theme-color',
          content: this.settingActiveTheme.bgColor
        }
      ]
    }
  }

  get surahId () {
    let id = 1
    if (__isNotNull(this.$route.params && this.$route.params.surahid)) {
      id = Number(this.$route.params.surahid)
    }
    return id
  }

  get verseId () {
    let id = 1
    if (__isNotNull(this.$route.params && this.$route.params.verseid)) {
      id = Number(this.$route.params.verseid)
    }
    return id
  }

  get isValidSurah () {
    return this.surahId > 0 && this.surahId <= 114
  }

  onChangeVerse (e: any) {
    const val = e.target.value
    this.$router.push(`/${this.surahId}/${val}`)
  }

  head () {
    return this.metaHead
  }

  mounted () {
    // @ts-ignore: Unreachable code error
    this.setHeaderTitle(`${this.surahId}:${this.verseId} ${this.currentSurah.name_latin}`)
    this.setPage('verse-detail')
  }

  activated () {
    // @ts-ignore: Unreachable code error
    this.setHeaderTitle(`${this.surahId}:${this.verseId} ${this.currentSurah.name_latin}`)
    this.setPage('verse-detail')
  }
}
</script>

<style lang="scss" scoped>
@import "@/assets/_variables.scss";

.detail {
  &__content {
    width: 90%;
    margin: 0 auto;
    padding-bottom: 2em;
  }
}
</style>