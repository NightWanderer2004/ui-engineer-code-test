<template>
  <div class="generic-template">
    <!-- Toolbar -->
    <div class="toolbar">
      <button class="back-button">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M19 12H5M12 19l-7-7 7-7"/>
        </svg>
      </button>
      <h1 class="page-title">{{ title }}</h1>
      <button class="bookmark-button" @click="toggleBookmark">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" :fill="isBookmarked ? 'currentColor' : 'none'" stroke="currentColor" stroke-width="2">
          <path d="M19 21l-7-5-7 5V5a2 2 0 0 1 2-2h10a2 2 0 0 1 2 2z"/>
        </svg>
      </button>
    </div>

    <!-- Content/Blocks -->
    <div class="content-area">
      <div
        v-for="(block, index) in blocks"
        :key="index"
        class="block"
      >
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
      <div
        v-for="(action, index) in actions"
        :key="index"
        class="action-item"
      >
        <p v-if="action.pricing_text && !isPurchased" class="pricing-text">{{ action.pricing_text }}</p>
        <button class="cta-button" @click="handleAction(action)">
          {{ isPurchased ? action.post_join_text : action.button_text }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GenericTemplate',
  props: {
    title: {
      type: String,
      required: true
    },
    blocks: {
      type: Array,
      default: () => []
    },
    actions: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      isBookmarked: false,
      isPurchased: false
    }
  },
  computed: {
    storageKey() {
      // Create a unique key based on the page title
      return `page_${this.title.replace(/\s+/g, '_').toLowerCase()}`
    }
  },
  mounted() {
    this.loadState()
  },
  watch: {
    title() {
      // When switching pages, reload the state
      this.loadState()
    }
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
      localStorage.setItem(this.storageKey, JSON.stringify({
        isBookmarked: this.isBookmarked,
        isPurchased: this.isPurchased
      }))
    },
    toggleBookmark() {
      this.isBookmarked = !this.isBookmarked
      this.saveState()
    },
    handleAction(action) {
      this.isPurchased = !this.isPurchased
      this.saveState()
    }
  }
}
</script>

<style scoped>
.generic-template {
  display: flex;
  flex-direction: column;
  height: 100%;
  background-color: var(--color-bg);
}

/* Toolbar */
.toolbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 60px;
  padding: 0 16px;
  background-color: var(--color-bg);
  border-bottom: 1px solid var(--color-border);
}

.back-button,
.bookmark-button {
  background: none;
  border: none;
  cursor: pointer;
  padding: 8px;
  color: var(--color-text);
  transition: transform 0.2s ease;
}

.bookmark-button:active {
  transform: scale(0.9);
}

.page-title {
  font-size: 18px;
  font-weight: 600;
  color: var(--color-text);
}

/* Content Area */
.content-area {
  flex: 1;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}

.block {
  margin-bottom: 16px;
}

.hero-block img {
  width: 100%;
  height: auto;
  display: block;
}

.text-block {
  padding: 0 16px;
  line-height: 1.6;
}

.text-block h2 {
  font-size: 24px;
  font-weight: 700;
  margin-bottom: 12px;
  color: var(--color-text);
}

.text-block h3 {
  font-size: 20px;
  font-weight: 600;
  margin-bottom: 10px;
  color: var(--color-text);
}

.text-block p {
  margin-bottom: 12px;
  color: #4b5563;
}

.text-block ul {
  margin-left: 20px;
  margin-bottom: 12px;
}

.text-block li {
  margin-bottom: 8px;
  color: #4b5563;
}

.video-block {
  padding: 0 16px;
}

.video-block iframe {
  width: 100%;
  height: 200px;
  border-radius: 8px;
}

/* Actions Section */
.actions-section {
  height: 80px;
  padding: 12px 16px;
  background-color: var(--color-bg);
  border-top: 1px solid var(--color-border);
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.action-item {
  text-align: center;
}

.pricing-text {
  font-size: 14px;
  color: #6b7280;
  margin-bottom: 8px;
}

.cta-button {
  width: 100%;
  padding: 14px;
  background-color: var(--color-primary);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.2s ease, opacity 0.2s ease;
}

.cta-button:active {
  transform: scale(0.98);
}
</style>
