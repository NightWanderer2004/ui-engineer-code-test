<template>
  <div class="generic-template">
    <!-- Toolbar -->
    <div class="toolbar">
      <button class="back-button"><ChevronLeft :size="22" :stroke-width="2.125" /> <span>Back</span></button>
      <h1 class="page-title">{{ title }}</h1>
      <button class="bookmark-button" @click="toggleBookmark">
        <Bookmark
          :size="22"
          :color="isBookmarked ? 'currentColor' : 'color-mix(in srgb, var(--color-bookmark) 85%, black 5%)'"
          :fill="isBookmarked ? 'transparent' : 'var(--color-bookmark)'"
          :style="{
            transition: 'fill 0.1s ease-in-out, color 0.1s ease-in-out',
          }"
        />
      </button>
    </div>

    <!-- Content/Blocks -->
    <div class="content-area">
      <div v-for="(block, index) in blocks" :key="index" class="block">
        <!-- Hero Block -->
        <div v-if="block.type === 'hero'" class="hero-block">
          <img :src="block.imageUrl" :alt="'Hero image'" />
        </div>

        <!-- Text Block -->
        <div v-else-if="block.type === 'text'" class="text-block" v-html="block.content"></div>

        <!-- Video Block -->
        <div v-else-if="block.type === 'video'" class="video-block">
          <iframe
            :src="block.embedUrl"
            frameborder="0"
            allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
            allowfullscreen
          ></iframe>
        </div>
      </div>
    </div>

    <!-- Actions/CTA -->
    <div class="actions-section">
      <div v-for="(action, index) in actions" :key="index" class="action-item">
        <button class="cta-button" @click="handleAction(action)">
          <span class="cta-text-wrapper">
            <span class="cta-text" :class="{ 'is-active': !isPurchased }" aria-hidden="false">
              {{ action.button_text }}
            </span>
            <span class="cta-text" :class="{ 'is-active': isPurchased }" aria-hidden="false">
              {{ action.post_join_text }}
              <Check :size="20" :stroke-width="2.75" />
            </span>
          </span>
        </button>
        <p v-if="action.pricing_text" class="pricing-text">
          {{ action.pricing_text }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import { ChevronLeft, Bookmark, Check } from 'lucide-vue'

export default {
  name: 'GenericTemplate',
  components: {
    ChevronLeft,
    Bookmark,
    Check,
  },
  props: {
    title: {
      type: String,
      required: true,
    },
    blocks: {
      type: Array,
      default: () => [],
    },
    actions: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      isBookmarked: false,
      isPurchased: false,
    }
  },
  computed: {
    storageKey() {
      // Create a unique key based on the page title
      return `page_${this.title.replace(/\s+/g, '_').toLowerCase()}`
    },
  },
  mounted() {
    this.loadState()
  },
  watch: {
    title() {
      // When switching pages, reload the state
      this.loadState()
    },
  },
  methods: {
    loadState() {
      const saved = localStorage.getItem(this.storageKey)
      if (saved) {
        const state = JSON.parse(saved)
        this.isBookmarked = state.isBookmarked || false
        this.isPurchased = state.isPurchased || false
      } else {
        this.isBookmarked = false
        this.isPurchased = false
      }
    },
    saveState() {
      localStorage.setItem(
        this.storageKey,
        JSON.stringify({
          isBookmarked: this.isBookmarked,
          isPurchased: this.isPurchased,
        })
      )
    },
    toggleBookmark() {
      this.isBookmarked = !this.isBookmarked
      this.saveState()
    },
    handleAction(action) {
      this.isPurchased = !this.isPurchased
      this.saveState()
    },
  },
}
</script>

<style scoped src="./GenericTemplate.css"></style>
