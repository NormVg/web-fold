<template>
  <div class="timeline-page">
    <!-- Header -->
    <header class="timeline-header">
      <div class="date-display">
        <span class="day-name">{{ currentDayName }}</span>
        <h1 class="date-text">{{ currentDateText }}</h1>
      </div>
      <button class="filter-btn" @click="toggleFilter">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
          <path d="M10 18h4v-2h-4v2zM3 6v2h18V6H3zm3 7h12v-2H6v2z" fill="currentColor" />
        </svg>
      </button>
    </header>

    <!-- Timeline Content -->
    <main class="timeline-content">
      <div class="timeline-track">
        <!-- Vertical line -->
        <div class="timeline-line"></div>

        <!-- Memory entries -->
        <div v-for="(memory, index) in memories" :key="memory.id" class="memory-entry"
          :style="{ animationDelay: `${index * 0.1}s` }">
          <!-- Timeline dot -->
          <div class="timeline-dot" :class="{ 'active': memory.isPlaying }"></div>

          <!-- Memory Card -->
          <div class="memory-card hover-lift" :class="memory.type">
            <!-- Card Header -->
            <div class="card-header">
              <div class="card-icon" :class="memory.type">
                <!-- Microphone icon for audio -->
                <svg v-if="memory.type === 'audio'" width="16" height="16" viewBox="0 0 24 24" fill="none">
                  <path d="M12 14c1.66 0 3-1.34 3-3V5c0-1.66-1.34-3-3-3S9 3.34 9 5v6c0 1.66 1.34 3 3 3z"
                    fill="#810100" />
                  <path
                    d="M17 11c0 2.76-2.24 5-5 5s-5-2.24-5-5H5c0 3.53 2.61 6.43 6 6.92V21h2v-3.08c3.39-.49 6-3.39 6-6.92h-2z"
                    fill="#810100" />
                </svg>
                <!-- Camera icon for image -->
                <svg v-else width="16" height="16" viewBox="0 0 24 24" fill="none">
                  <path
                    d="M21 19V5c0-1.1-.9-2-2-2H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2zM8.5 13.5l2.5 3.01L14.5 12l4.5 6H5l3.5-4.5z"
                    fill="#810100" />
                </svg>
              </div>
              <h3 class="card-title">{{ memory.title }}</h3>
              <span class="card-time">{{ memory.time }}</span>
            </div>

            <!-- Audio Player -->
            <div v-if="memory.type === 'audio'" class="audio-player">
              <button class="play-btn" @click="togglePlay(memory)" :class="{ 'playing': memory.isPlaying }">
                <svg v-if="!memory.isPlaying" width="16" height="16" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M8 5v14l11-7z" />
                </svg>
                <svg v-else width="16" height="16" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M6 4h4v16H6V4zm8 0h4v16h-4V4z" />
                </svg>
              </button>

              <div class="waveform-container">
                <div v-for="(bar, i) in memory.waveform" :key="i" class="waveform-bar"
                  :class="{ 'active': memory.isPlaying && i <= memory.progressBars }" :style="{ height: bar + 'px' }">
                </div>
              </div>

              <span class="duration">{{ formatTime(memory.currentTime) }}</span>
            </div>

            <!-- Image Content -->
            <div v-if="memory.type === 'image'" class="image-container">
              <img :src="memory.imageUrl" :alt="memory.title" class="memory-image" />
            </div>

            <!-- Tags -->
            <div class="card-footer">
              <div class="tags">
                <span v-for="tag in memory.tags" :key="tag" class="tag">
                  <!-- Mood icon -->
                  <svg v-if="tag === 'SAD'" width="12" height="12" viewBox="0 0 24 24" fill="none">
                    <circle cx="12" cy="12" r="10" stroke="#810100" stroke-width="2" fill="none" />
                    <circle cx="8" cy="10" r="1.5" fill="#810100" />
                    <circle cx="16" cy="10" r="1.5" fill="#810100" />
                    <path d="M8 16s1.5-2 4-2 4 2 4 2" stroke="#810100" stroke-width="2" stroke-linecap="round" />
                  </svg>
                  <!-- Location icon -->
                  <svg v-if="tag === 'LOCATION'" width="12" height="12" viewBox="0 0 24 24" fill="none">
                    <path
                      d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z"
                      fill="#810100" />
                  </svg>
                  {{ tag }}
                </span>
              </div>
              <button class="share-btn">
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                  <path d="M22 2L11 13M22 2l-7 20-4-9-9-4 20-7z" stroke="#810100" stroke-width="2"
                    stroke-linecap="round" stroke-linejoin="round" />
                </svg>
              </button>
            </div>
          </div>
        </div>

        <!-- Year marker -->
        <div class="year-marker">
          <span>2023</span>
        </div>
      </div>
    </main>

    <!-- Bottom Navigation -->
    <nav class="bottom-nav">
      <button class="nav-btn" :class="{ active: currentView === 'grid' }" @click="currentView = 'grid'">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
          <path
            d="M4 8h4V4H4v4zm6 12h4v-4h-4v4zm-6 0h4v-4H4v4zm0-6h4v-4H4v4zm6 0h4v-4h-4v4zm6-10v4h4V4h-4zm-6 4h4V4h-4v4zm6 6h4v-4h-4v4zm0 6h4v-4h-4v4z" />
        </svg>
      </button>
      <button class="nav-btn add-btn" @click="$emit('navigate', 'new')">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
          <path d="M12 5v14M5 12h14" stroke-linecap="round" />
        </svg>
      </button>
      <button class="nav-btn" :class="{ active: currentView === 'profile' }" @click="currentView = 'profile'">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
          <path
            d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 3c1.66 0 3 1.34 3 3s-1.34 3-3 3-3-1.34-3-3 1.34-3 3-3zm0 14.2c-2.5 0-4.71-1.28-6-3.22.03-1.99 4-3.08 6-3.08 1.99 0 5.97 1.09 6 3.08-1.29 1.94-3.5 3.22-6 3.22z" />
        </svg>
      </button>
    </nav>
  </div>
</template>

<script setup>
import { ref, computed, onUnmounted } from 'vue'

const emit = defineEmits(['navigate'])

// Current date
const currentDayName = computed(() => {
  const days = ['SUNDAY', 'MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY']
  return days[new Date().getDay()]
})

const currentDateText = computed(() => {
  const date = new Date()
  const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
  return `${months[date.getMonth()]} ${date.getDate()}`
})

// View state
const currentView = ref('grid')
const filterOpen = ref(false)

// Sample memories data
const memories = ref([
  {
    id: 1,
    type: 'audio',
    title: 'Rant about math',
    time: '03:34 PM',
    duration: 214,
    currentTime: 0,
    startTime: 0,
    isPlaying: false,
    progressBars: 0,
    waveform: [8, 16, 12, 24, 20, 32, 28, 24, 32, 28, 20, 28, 24, 32, 28, 24, 20, 16],
    tags: ['SAD', 'LOCATION']
  },
  {
    id: 2,
    type: 'image',
    title: 'Hello NYC',
    time: '03:34 PM',
    imageUrl: 'https://images.unsplash.com/photo-1534430480872-3498386e7856?w=400&h=300&fit=crop',
    tags: ['SAD', 'LOCATION']
  }
])

// Audio playback intervals
let playbackIntervals = {}

function toggleFilter() {
  filterOpen.value = !filterOpen.value
}

function togglePlay(memory) {
  if (memory.isPlaying) {
    // Pause
    memory.isPlaying = false
    clearInterval(playbackIntervals[memory.id])
  } else {
    // Stop other playing memories
    memories.value.forEach(m => {
      if (m.isPlaying) {
        m.isPlaying = false
        clearInterval(playbackIntervals[m.id])
      }
    })

    // Play this one
    memory.isPlaying = true
    memory.startTime = Date.now() - (memory.currentTime * 1000)

    playbackIntervals[memory.id] = setInterval(() => {
      const elapsed = (Date.now() - memory.startTime) / 1000
      memory.currentTime = Math.min(elapsed, memory.duration)
      memory.progressBars = Math.floor((memory.currentTime / memory.duration) * memory.waveform.length)

      // Animate waveform
      memory.waveform = memory.waveform.map(val => {
        const change = Math.floor(Math.random() * 8) - 4
        return Math.max(8, Math.min(40, val + change))
      })

      if (memory.currentTime >= memory.duration) {
        memory.isPlaying = false
        memory.currentTime = 0
        memory.progressBars = 0
        clearInterval(playbackIntervals[memory.id])
      }
    }, 50)
  }
}

function formatTime(seconds) {
  const mins = Math.floor(seconds / 60)
  const secs = Math.floor(seconds % 60)
  return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`
}

onUnmounted(() => {
  Object.values(playbackIntervals).forEach(id => clearInterval(id))
})
</script>

<style scoped>
.timeline-page {
  min-height: 100vh;
  background: #EDEADC;
  position: relative;
  padding-bottom: 40px;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
}

/* Header */
.timeline-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding: 40px 60px;
  max-width: 1400px;
  margin: 0 auto;
  animation: slideUpFade 0.6s ease-out;
}

.day-name {
  font-size: 14px;
  font-weight: 600;
  color: #810100;
  letter-spacing: 1.5px;
  text-transform: uppercase;
  display: block;
  margin-bottom: 6px;
}

.date-text {
  font-size: 48px;
  font-weight: 700;
  color: #181717;
  margin: 0;
}

.filter-btn {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: #C48888;
  border: none;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: 0 2px 12px rgba(129, 1, 0, 0.2);
}

.filter-btn:hover {
  transform: scale(1.1) rotate(5deg);
  box-shadow: 0 4px 16px rgba(129, 1, 0, 0.3);
}

/* Timeline Content */
.timeline-content {
  padding: 0 60px;
  max-width: 1400px;
  margin: 0 auto;
}

.timeline-track {
  position: relative;
  padding-left: 60px;
}

.timeline-line {
  position: absolute;
  left: 20px;
  top: 0;
  bottom: 80px;
  width: 4px;
  background: #810100;
  border-radius: 2px;
}

/* Memory Entry */
.memory-entry {
  position: relative;
  margin-bottom: 32px;
  animation: slideUpFade 0.6s ease-out backwards;
}

.timeline-dot {
  position: absolute;
  left: -48px;
  top: 32px;
  width: 16px;
  height: 16px;
  background: #810100;
  border-radius: 50%;
  border: 4px solid #EDEADC;
  box-shadow: 0 2px 10px rgba(129, 1, 0, 0.3);
  transition: all 0.3s ease;
  z-index: 1;
}

.timeline-dot.active {
  background: #C0392B;
  animation: pulse 1.5s ease-in-out infinite;
}

/* Memory Card */
.memory-card {
  background: #FDFBF7;
  border-radius: 24px;
  padding: 24px;
  box-shadow:
    0 4px 20px rgba(0, 0, 0, 0.08),
    0 1px 3px rgba(0, 0, 0, 0.05);
  transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
  max-width: 800px;
}

.memory-card.hover-lift:hover {
  transform: translateY(-4px);
  box-shadow:
    0 12px 40px rgba(0, 0, 0, 0.12),
    0 4px 12px rgba(0, 0, 0, 0.08);
}

.card-header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 16px;
}

.card-icon {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.card-icon.audio {
  background: #FFEAEA;
}

.card-icon.image {
  background: #E8F4EA;
}

.card-icon svg {
  width: 20px;
  height: 20px;
}

.card-title {
  flex: 1;
  font-size: 20px;
  font-weight: 600;
  color: #181717;
  margin: 0;
}

.card-time {
  font-size: 14px;
  color: rgba(24, 23, 23, 0.4);
}

/* Audio Player */
.audio-player {
  display: flex;
  align-items: center;
  gap: 16px;
  background: #EDEADC;
  border-radius: 40px;
  padding: 12px 20px 12px 12px;
  margin-bottom: 16px;
}

.play-btn {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: #181717;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  color: white;
  padding: 0;
  flex-shrink: 0;
}

.play-btn svg {
  width: 20px;
  height: 20px;
}

.play-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 4px 12px rgba(24, 23, 23, 0.3);
}

.play-btn.playing {
  background: #810100;
}

.waveform-container {
  display: flex;
  align-items: center;
  gap: 3px;
  flex: 1;
  height: 48px;
}

.waveform-bar {
  width: 4px;
  background: #D0D0D0;
  border-radius: 2px;
  transition: all 0.15s ease;
}

.waveform-bar.active {
  background: #810100;
}

.duration {
  font-size: 14px;
  font-weight: 600;
  color: rgba(24, 23, 23, 0.7);
  min-width: 50px;
  text-align: right;
}

/* Image Container */
.image-container {
  border-radius: 16px;
  overflow: hidden;
  margin-bottom: 16px;
}

.memory-image {
  width: 100%;
  height: 300px;
  object-fit: cover;
  display: block;
  transition: transform 0.4s ease;
}

.memory-card:hover .memory-image {
  transform: scale(1.02);
}

/* Card Footer */
.card-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.tags {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.tag {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 12px;
  font-weight: 600;
  padding: 8px 14px;
  border-radius: 24px;
  background: #EDEADC;
  color: #181717;
  transition: all 0.3s ease;
}

.tag svg {
  width: 14px;
  height: 14px;
}

.tag:hover {
  transform: translateY(-2px);
  background: #E0DDD0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.share-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: #EDEADC;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  padding: 0;
}

.share-btn svg {
  width: 18px;
  height: 18px;
}

.share-btn:hover {
  background: #E0DDD0;
  transform: scale(1.1);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

/* Year Marker */
.year-marker {
  text-align: center;
  padding: 32px 0;
  font-size: 16px;
  font-style: italic;
  color: rgba(24, 23, 23, 0.5);
  position: relative;
  margin-left: -60px;
}

/* Bottom Navigation - Desktop style (sidebar-like) */
.bottom-nav {
  position: fixed;
  top: 50%;
  right: 40px;
  transform: translateY(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  background: #F4F2E8;
  padding: 12px;
  border-radius: 48px;
  border: 1px solid #810100;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.12);
  z-index: 100;
  animation: slideInRight 0.6s ease-out 0.3s backwards;
}

.nav-btn {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: transparent;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: #810100;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  padding: 0;
}

.nav-btn svg {
  width: 26px;
  height: 26px;
}

.nav-btn:hover {
  transform: scale(1.15);
}

.nav-btn.active {
  background: rgba(129, 1, 0, 0.1);
}

.nav-btn.add-btn {
  background: #FDFBF7;
  border: 2px solid #810100;
}

.nav-btn.add-btn:hover {
  transform: scale(1.2) rotate(90deg);
  background: white;
  box-shadow: 0 4px 16px rgba(129, 1, 0, 0.2);
}

/* Animations */
@keyframes slideUpFade {
  from {
    opacity: 0;
    transform: translateY(30px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes slideInRight {
  from {
    opacity: 0;
    transform: translateX(100px) translateY(-50%);
  }

  to {
    opacity: 1;
    transform: translateX(0) translateY(-50%);
  }
}

@keyframes pulse {

  0%,
  100% {
    box-shadow: 0 0 0 0 rgba(129, 1, 0, 0.5);
  }

  50% {
    box-shadow: 0 0 0 12px rgba(129, 1, 0, 0);
  }
}

/* Mobile fallback */
@media (max-width: 768px) {
  .timeline-header {
    padding: 24px 20px;
  }

  .date-text {
    font-size: 32px;
  }

  .timeline-content {
    padding: 0 20px;
  }

  .timeline-track {
    padding-left: 40px;
  }

  .timeline-line {
    left: 12px;
    width: 3px;
  }

  .timeline-dot {
    left: -32px;
    width: 12px;
    height: 12px;
    border: 3px solid #EDEADC;
  }

  .memory-card {
    padding: 16px;
    border-radius: 20px;
  }

  .card-icon {
    width: 32px;
    height: 32px;
  }

  .card-icon svg {
    width: 16px;
    height: 16px;
  }

  .card-title {
    font-size: 16px;
  }

  .audio-player {
    padding: 8px 16px 8px 8px;
  }

  .play-btn {
    width: 40px;
    height: 40px;
  }

  .play-btn svg {
    width: 16px;
    height: 16px;
  }

  .waveform-container {
    height: 40px;
    gap: 2px;
  }

  .waveform-bar {
    width: 3px;
  }

  .image-container {
    border-radius: 12px;
  }

  .memory-image {
    height: 200px;
  }

  .year-marker {
    margin-left: -40px;
  }

  /* Mobile bottom nav */
  .bottom-nav {
    position: fixed;
    bottom: 20px;
    right: auto;
    left: 50%;
    top: auto;
    transform: translateX(-50%);
    flex-direction: row;
    border-radius: 40px;
    padding: 8px;
  }

  .nav-btn {
    width: 48px;
    height: 48px;
  }

  .nav-btn svg {
    width: 24px;
    height: 24px;
  }
}
</style>
