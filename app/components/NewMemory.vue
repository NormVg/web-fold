<template>
  <div class="new-memory-container">
    <div class="noise-overlay"></div>

    <!-- Header -->
    <header class="header entrance-anim" style="--delay: 0.1s">
      <button class="close-btn" @click="handleClose">
        <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
          <path d="M1 1L13 13M1 13L13 1" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
        </svg>
      </button>
      <h1 class="header-title">New Memory</h1>
      <div class="header-spacer"></div>
    </header>

    <!-- Main Content -->
    <main class="main-content">
      <!-- Hero Section -->
      <section class="hero-section entrance-anim" style="--delay: 0.2s">
        <h2 class="tagline">Unfold your mind.</h2>
        <p class="subtitle">Capture your thoughts, feelings, and moments that matter</p>
      </section>

      <!-- Two Column Layout -->
      <div class="content-grid">
        <!-- Left Column: Recording -->
        <div class="recording-column entrance-anim" style="--delay: 0.3s">
          <div class="recorder-card hover-lift">
            <div class="timer" :class="{ 'pulsing': isRecording }">{{ formattedTime }}</div>

            <!-- Waveform Visualization -->
            <div class="waveform">
              <div
                v-for="(bar, index) in waveformBars"
                :key="index"
                class="waveform-bar"
                :style="{
                  height: bar + 'px',
                  animationDelay: (index * 0.05) + 's',
                  transform: isRecording ? 'scaleY(1.5)' : 'scaleY(1)'
                }"
                :class="{ 'animating': isRecording }"
              ></div>
            </div>

            <!-- Recording Controls -->
            <div class="controls">
              <button class="control-btn secondary" @click="resetRecording" title="Reset">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
                  <path d="M3 12a9 9 0 1 0 9-9 9.75 9.75 0 0 0-6.74 2.74L3 8" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M3 3v5h5" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </button>

              <button
                class="control-btn primary"
                @click="toggleRecording"
                :class="{ 'recording': isRecording }"
              >
                <div class="record-ring" v-if="isRecording"></div>
                <div class="record-icon" :class="{ 'recording': isRecording }"></div>
              </button>

              <button class="control-btn secondary" @click="confirmRecording" title="Confirm">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
                  <path d="M20 6L9 17l-5-5" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </button>
            </div>

            <p class="recording-hint">
              <span v-if="!isRecording">Click the button to start recording</span>
              <span v-else class="recording-text">Recording...</span>
            </p>
          </div>
        </div>

        <!-- Right Column: Mood & Caption -->
        <div class="details-column">
          <!-- Mood Section -->
          <div class="mood-card hover-lift entrance-anim" style="--delay: 0.4s">
            <h3 class="card-title">How you feeling right now?</h3>
            <div class="mood-options">
              <button
                v-for="mood in moods"
                :key="mood.id"
                class="mood-btn"
                :class="{ 'selected': selectedMood === mood.id }"
                @click="selectedMood = mood.id"
              >
                <span class="mood-emoji">{{ mood.emoji }}</span>
                <span class="mood-label">{{ mood.label }}</span>
              </button>
            </div>
          </div>

          <!-- Caption Section -->
          <div class="caption-card hover-lift entrance-anim" style="--delay: 0.5s">
            <h3 class="card-title">Caption this?</h3>
            <div class="caption-input-wrapper">
              <textarea
                v-model="caption"
                class="caption-input"
                placeholder="Add a title or description for your memory..."
                rows="3"
              ></textarea>
              <button class="add-btn" title="Add more details">
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                  <path d="M12 5v14M5 12h14" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
                </svg>
              </button>
            </div>
            <p class="caption-hint">thinking....</p>
          </div>

          <!-- Tags Section -->
          <div class="tags-card hover-lift entrance-anim" style="--delay: 0.6s">
            <h3 class="card-title">Add tags</h3>
            <div class="tags-wrapper">
              <span
                v-for="(tag, index) in suggestedTags"
                :key="tag"
                class="tag"
                @click="toggleTag(tag)"
                :class="{ 'selected': selectedTags.includes(tag) }"
                :style="{ animationDelay: (0.7 + index * 0.05) + 's' }"
              >
                {{ tag }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- Submit Button -->
      <div class="submit-section entrance-anim" style="--delay: 0.8s">
        <button
          class="fold-btn"
          @click="handleSubmit"
          :class="{ 'submitting': isSubmitting, 'success': isSuccess }"
        >
          <span v-if="!isSubmitting && !isSuccess">Fold it</span>
          <span v-if="isSubmitting">Folding...</span>
          <span v-if="isSuccess">Folded!</span>

          <svg v-if="!isSubmitting && !isSuccess" width="20" height="20" viewBox="0 0 24 24" fill="none" class="arrow-icon">
            <path d="M5 12h14M12 5l7 7-7 7" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>

          <svg v-if="isSuccess" width="20" height="20" viewBox="0 0 24 24" fill="none" class="check-icon">
            <path d="M20 6L9 17l-5-5" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
        </button>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed, onUnmounted } from 'vue'

// Recording state
const isRecording = ref(false)
const elapsedTime = ref(0)
const startTime = ref(0)
let recordingInterval = null

// Submission state
const isSubmitting = ref(false)
const isSuccess = ref(false)

// Mood state
const selectedMood = ref('v-happy')
const moods = [
  { id: 'v-sad', emoji: '😢', label: 'V. Sad' },
  { id: 'sad', emoji: '😔', label: 'Sad' },
  { id: 'normal', emoji: '😐', label: 'Normal' },
  { id: 'happy', emoji: '🙂', label: 'Happy' },
  { id: 'v-happy', emoji: '😄', label: 'V. Happy' }
]

// Caption state
const caption = ref('')

// Tags state
const suggestedTags = ['#reflection', '#gratitude', '#goals', '#memories', '#daily', '#ideas']
const selectedTags = ref([])

// Waveform visualization
const waveformBars = ref([12, 24, 18, 32, 28, 40, 35, 28, 40, 32, 24, 36, 28])

const formattedTime = computed(() => {
  // Calculate seconds from milliseconds
  const totalSeconds = Math.floor(elapsedTime.value / 1000)
  const minutes = Math.floor(totalSeconds / 60)
  const seconds = totalSeconds % 60

  // Optional: Add milliseconds for "high-tech" feel if desired,
  // but standard 00:00 is usually best for voice memos.
  // Sticking to 00:00 as per design.
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

function toggleRecording() {
  if (isRecording.value) {
    // Pause/Stop
    isRecording.value = false
    clearInterval(recordingInterval)
  } else {
    // Start/Resume
    isRecording.value = true
    startTime.value = Date.now() - elapsedTime.value

    recordingInterval = setInterval(() => {
      elapsedTime.value = Date.now() - startTime.value

      // More dynamic waveform
      waveformBars.value = waveformBars.value.map(val => {
        const change = Math.floor(Math.random() * 20) - 10
        return Math.max(10, Math.min(60, val + change))
      })
    }, 50) // Faster updates for smoother waveform/timer
  }
}

function resetRecording() {
  isRecording.value = false
  elapsedTime.value = 0
  clearInterval(recordingInterval)
  waveformBars.value = [12, 24, 18, 32, 28, 40, 35, 28, 40, 32, 24, 36, 28]
}

function confirmRecording() {
  isRecording.value = false
  clearInterval(recordingInterval)
}

function toggleTag(tag) {
  const index = selectedTags.value.indexOf(tag)
  if (index > -1) {
    selectedTags.value.splice(index, 1)
  } else {
    selectedTags.value.push(tag)
  }
}

function handleClose() {
  console.log('Close clicked')
}

function handleSubmit() {
  if (isSubmitting.value || isSuccess.value) return

  isSubmitting.value = true

  // Simulate API call
  setTimeout(() => {
    isSubmitting.value = false
    isSuccess.value = true

    // Reset success state after delay
    setTimeout(() => {
      isSuccess.value = false
    }, 2000)
  }, 1500)
}

onUnmounted(() => {
  clearInterval(recordingInterval)
})
</script>

<style scoped>
/* Animations */
@keyframes slideUpFade {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes pulse {
  0% { transform: scale(1); box-shadow: 0 0 0 0 rgba(139, 46, 46, 0.4); }
  70% { transform: scale(1.05); box-shadow: 0 0 0 10px rgba(139, 46, 46, 0); }
  100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(139, 46, 46, 0); }
}

@keyframes float {
  0% { transform: translateY(0px); }
  50% { transform: translateY(-5px); }
  100% { transform: translateY(0px); }
}

@keyframes pop {
  0% { transform: scale(0.95); }
  50% { transform: scale(1.1); }
  100% { transform: scale(1); }
}

.entrance-anim {
  opacity: 0;
  animation: slideUpFade 0.6s cubic-bezier(0.2, 0.8, 0.2, 1) forwards;
  animation-delay: var(--delay);
}

.hover-lift {
  transition: transform 0.3s cubic-bezier(0.2, 0.8, 0.2, 1), box-shadow 0.3s ease;
}

.hover-lift:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.08);
}

.new-memory-container {
  min-height: 100vh;
  background: #f5f3ef;
  position: relative;
  overflow-x: hidden;
}

/* Noise Texture */
.noise-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0.03;
  pointer-events: none;
  z-index: 0;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
}

/* Header */
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px 48px;
  background: rgba(245, 243, 239, 0.85);
  backdrop-filter: blur(10px);
  position: sticky;
  top: 0;
  z-index: 100;
  border-bottom: 1px solid rgba(0, 0, 0, 0.05);
}

.close-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: #8b2e2e;
  border: none;
  color: white;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.close-btn:hover {
  transform: rotate(90deg) scale(1.1);
  background: #6b2323;
}

.header-title {
  font-size: 18px;
  font-weight: 600;
  color: #1a1a1a;
  opacity: 0.9;
}

.header-spacer { width: 40px; }

/* Main Content */
.main-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: 40px 48px 80px;
  position: relative;
  z-index: 1;
}

/* Hero Section */
.hero-section {
  text-align: center;
  margin-bottom: 48px;
}

.tagline {
  font-size: 48px;
  font-weight: 800;
  color: #1a1a1a;
  margin-bottom: 16px;
  letter-spacing: -0.03em;
  background: linear-gradient(135deg, #1a1a1a 0%, #4a4a4a 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.subtitle {
  font-size: 18px;
  color: #666;
  font-weight: 400;
}

/* Content Grid */
.content-grid {
  display: grid;
  grid-template-columns: 1.2fr 1fr;
  gap: 32px;
  margin-bottom: 48px;
}

/* Cards Base Style */
.recorder-card,
.mood-card,
.caption-card,
.tags-card {
  background: white;
  border-radius: 24px;
  padding: 32px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -1px rgba(0, 0, 0, 0.03);
  border: 1px solid rgba(0,0,0,0.02);
}

/* Recording Column */
.recording-column {
  display: flex;
  flex-direction: column;
}

.recorder-card {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 420px;
  background: linear-gradient(180deg, #e8e4dc 0%, #e0dbce 100%);
}

.timer {
  font-size: 80px;
  font-weight: 700;
  color: #1a1a1a;
  letter-spacing: -0.04em;
  margin-bottom: 32px;
  font-variant-numeric: tabular-nums;
  transition: color 0.3s ease;
}

.timer.pulsing {
  color: #8b2e2e;
}

/* Waveform */
.waveform {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  height: 80px;
  margin-bottom: 40px;
}

.waveform-bar {
  width: 6px;
  background: #1a1a1a;
  border-radius: 3px;
  transition: all 0.1s ease;
  min-height: 4px;
}

.waveform-bar.animating {
  background: #8b2e2e;
}

/* Controls */
.controls {
  display: flex;
  align-items: center;
  gap: 24px;
  margin-bottom: 24px;
}

.control-btn {
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  position: relative;
}

.control-btn:active {
  transform: scale(0.95);
}

.control-btn.secondary {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.9);
  color: #1a1a1a;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  backdrop-filter: blur(4px);
}

.control-btn.secondary:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  color: #8b2e2e;
}

.control-btn.primary {
  width: 88px;
  height: 88px;
  border-radius: 50%;
  background: #1a1a1a;
  position: relative;
}

.control-btn.primary.recording {
  animation: pulse 1.5s infinite;
}

.record-icon {
  width: 32px;
  height: 32px;
  background: #f5f3ef;
  border-radius: 50%;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  z-index: 2;
}

.record-icon.recording {
  width: 24px;
  height: 24px;
  border-radius: 4px;
  background: #ff4747;
}

.recording-hint {
  font-size: 14px;
  color: #888;
  height: 20px;
}

.recording-text {
  color: #8b2e2e;
  font-weight: 600;
  animation: float 2s ease-in-out infinite;
  display: inline-block;
}

/* Details Column */
.details-column {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

/* Mood Options */
.mood-options {
  display: flex;
  gap: 12px;
}

.mood-btn {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  padding: 16px 8px;
  background: #f9f9f9;
  border: 2px solid transparent;
  border-radius: 20px;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.mood-btn:hover {
  transform: translateY(-5px) scale(1.05);
  background: white;
  box-shadow: 0 8px 16px rgba(0,0,0,0.08);
}

.mood-btn.selected {
  background: #e8f5e8;
  border-color: #4a9c4a;
  transform: scale(1.05);
}

.mood-btn.selected:first-child,
.mood-btn.selected:nth-child(2) {
  background: #fce8e8;
  border-color: #c44a4a;
}

.mood-emoji {
  font-size: 32px;
  filter: drop-shadow(0 2px 4px rgba(0,0,0,0.1));
  transition: transform 0.3s ease;
}

.mood-btn:hover .mood-emoji {
  transform: scale(1.2) rotate(5deg);
}

.mood-label {
  font-size: 12px;
  font-weight: 600;
  color: #666;
}

/* Caption Card */
.caption-input-wrapper {
  position: relative;
}

.caption-input {
  width: 100%;
  padding: 20px;
  padding-right: 60px;
  background: #f9f9f9;
  border: 2px solid transparent;
  border-radius: 20px;
  font-size: 16px;
  font-family: inherit;
  resize: none;
  color: #1a1a1a;
  transition: all 0.3s ease;
  line-height: 1.5;
}

.caption-input:focus {
  outline: none;
  background: white;
  border-color: #8b2e2e;
  box-shadow: 0 0 0 4px rgba(139, 46, 46, 0.1);
}

.add-btn {
  position: absolute;
  right: 16px;
  bottom: 16px;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: #8b2e2e;
  border: none;
  color: white;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.add-btn:hover {
  transform: scale(1.1) rotate(90deg);
  background: #6b2323;
}

/* Tags Card */
.tags-wrapper {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.tag {
  padding: 10px 20px;
  background: #f5f5f5;
  border-radius: 100px;
  font-size: 14px;
  font-weight: 500;
  color: #666;
  cursor: pointer;
  transition: all 0.2s cubic-bezier(0.34, 1.56, 0.64, 1);
  border: 1px solid transparent;
}

.tag:hover {
  background: white;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.05);
  border-color: #eee;
  color: #1a1a1a;
}

.tag.selected {
  background: #8b2e2e;
  color: white;
  transform: scale(1.05);
  box-shadow: 0 4px 12px rgba(139, 46, 46, 0.2);
}

/* Submit Section */
.submit-section {
  display: flex;
  justify-content: center;
  perspective: 1000px;
}

.fold-btn {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 22px 72px;
  background: #8b2e2e;
  color: white;
  border: none;
  border-radius: 100px;
  font-size: 18px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  position: relative;
  overflow: hidden;
}

.fold-btn:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 32px rgba(139, 46, 46, 0.3);
  background: #7a2626;
}

.fold-btn:active {
  transform: translateY(-1px);
}

.fold-btn.submitting {
  background: #666;
  pointer-events: none;
}

.fold-btn.success {
  background: #4a9c4a;
  pointer-events: none;
}

.arrow-icon {
  transition: transform 0.3s ease;
}

.fold-btn:hover .arrow-icon {
  transform: translateX(4px);
}

.check-icon {
  animation: pop 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

/* Responsive */
@media (max-width: 1024px) {
  .content-grid { grid-template-columns: 1fr; }
  .recorder-card { min-height: 360px; }
  .tagline { font-size: 40px; }
}

@media (max-width: 768px) {
  .header { padding: 16px 24px; }
  .main-content { padding: 24px 24px 40px; }
  .tagline { font-size: 32px; }
  .timer { font-size: 60px; }
  .mood-options { flex-wrap: wrap; }
  .mood-btn { flex: 0 0 calc(33.33% - 8px); }
}
</style>
