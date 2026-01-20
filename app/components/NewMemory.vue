<template>
  <div class="new-memory-page">
    <!-- Header -->
    <header class="header">
      <button class="close-btn" @click="$emit('navigate', 'timeline')">
        <svg width="24" height="24" viewBox="0 0 49 49" fill="none">
          <circle cx="24.27" cy="24.27" r="24.27" fill="#810100" fill-opacity="0.2" />
          <path
            d="M24 11.8125C21.5895 11.8125 19.2332 12.5273 17.229 13.8665C15.2248 15.2056 13.6627 17.1091 12.7402 19.336C11.8178 21.563 11.5764 24.0135 12.0467 26.3777C12.5169 28.7418 13.6777 30.9134 15.3821 32.6179C17.0866 34.3223 19.2582 35.4831 21.6223 35.9533C23.9865 36.4236 26.437 36.1822 28.664 35.2598C30.8909 34.3373 32.7944 32.7752 34.1335 30.771C35.4727 28.7668 36.1875 26.4105 36.1875 24C36.1841 20.7687 34.899 17.6708 32.6141 15.3859C30.3292 13.101 27.2313 11.8159 24 11.8125ZM28.4133 27.0867C28.5004 27.1738 28.5695 27.2772 28.6166 27.391C28.6638 27.5048 28.688 27.6268 28.688 27.75C28.688 27.8732 28.6638 27.9952 28.6166 28.109C28.5695 28.2228 28.5004 28.3262 28.4133 28.4133C28.3262 28.5004 28.2228 28.5695 28.109 28.6166C27.9952 28.6638 27.8732 28.688 27.75 28.688C27.6268 28.688 27.5048 28.6638 27.391 28.6166C27.2772 28.5695 27.1738 28.5004 27.0867 28.4133L24 25.3254L20.9133 28.4133C20.8262 28.5004 20.7228 28.5695 20.609 28.6166C20.4952 28.6638 20.3732 28.688 20.25 28.688C20.1268 28.688 20.0048 28.6638 19.891 28.6166C19.7772 28.5695 19.6738 28.5004 19.5867 28.4133C19.4996 28.3262 19.4305 28.2228 19.3834 28.109C19.3362 27.9952 19.312 27.8732 19.312 27.75C19.312 27.6268 19.3362 27.5048 19.3834 27.391C19.4305 27.2772 19.4996 27.1738 19.5867 27.0867L22.6746 24L19.5867 20.9133C19.4108 20.7374 19.312 20.4988 19.312 20.25C19.312 20.0012 19.4108 19.7626 19.5867 19.5867C19.7626 19.4108 20.0012 19.312 20.25 19.312C20.4988 19.312 20.7374 19.4108 20.9133 19.5867L24 22.6746L27.0867 19.5867C27.1738 19.4996 27.2772 19.4305 27.391 19.3834C27.5048 19.3362 27.6268 19.312 27.75 19.312C27.8732 19.312 27.9952 19.3362 28.109 19.3834C28.2228 19.4305 28.3262 19.4996 28.4133 19.5867C28.5004 19.6738 28.5695 19.7772 28.6166 19.891C28.6638 20.0048 28.688 20.1268 28.688 20.25C28.688 20.3732 28.6638 20.4952 28.6166 20.609C28.5695 20.7228 28.5004 20.8262 28.4133 20.9133L25.3254 24L28.4133 27.0867Z"
            fill="#810100" />
        </svg>
      </button>
      <h1 class="page-title">New Memory</h1>
    </header>

    <!-- Content -->
    <main class="content">
      <!-- Left Column: Input Card -->
      <div class="input-section">
        <div class="input-card">
          <!-- State: Text Input -->
          <textarea v-if="!isRecording && !audioUrl" class="memory-input"
            placeholder="How are you feeling right now?&#10;Write it down" v-model="memoryText"></textarea>

          <!-- Audio Recorder UI -->
          <div v-if="isRecording || audioUrl" class="recorder-container">
            <!-- Timer -->
            <div class="timer">{{ formattedTime }}</div>

            <!-- Waveform -->
            <div class="waveform">
              <div v-for="(bar, i) in waveform" :key="i" class="bar"
                :style="{ height: bar + 'px', opacity: isRecording ? 1 : 0.5 }"></div>
            </div>

            <!-- Controls -->
            <div class="recorder-controls">
              <!-- Reset (Left) -->
              <button class="control-btn small" @click="discardRecording" title="Reset">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" class="icon-dark">
                  <path d="M4 12V9a9 9 0 0113.5-7.3M20 12v3a9 9 0 01-13.5 7.3M4 12H1M20 12h3" stroke="currentColor"
                    stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" />
                </svg>
              </button>

              <!-- Stop/Interaction (Center) -->
              <button class="control-btn large" @click="toggleRecordingState">
                <!-- Stop Icon (if recording) -->
                <div v-if="isRecording" class="inner-stop"></div>
                <!-- Play Icon (if stopped/preview) -->
                <svg v-else width="32" height="32" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M8 5v14l11-7z" />
                </svg>
              </button>

              <!-- Confirm (Right) -->
              <button class="control-btn small" @click="confirmAudio" title="Save Audio">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" class="icon-dark">
                  <path d="M20 6L9 17l-5-5" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"
                    stroke-linejoin="round" />
                </svg>
              </button>
            </div>
          </div>

          <div class="action-bar">
            <div class="action-group-left">
              <button class="action-btn">
                <!-- Image Icon -->
                <svg width="24" height="24" viewBox="0 0 40 40" fill="none">
                  <circle cx="20" cy="20" r="18.5" fill="#810100" fill-opacity="0.2" />
                  <path
                    d="M26.297 12.574H10.5739C10.1948 12.574 9.8312 12.725 9.5632 12.993C9.2951 13.261 9.1445 13.624 9.1445 14.004V26.868C9.1445 27.247 9.2951 27.611 9.5632 27.879C9.8312 28.147 10.1948 28.297 10.5739 28.297H26.297C26.6761 28.297 27.0397 28.147 27.3078 27.879C27.5758 27.611 27.7264 27.247 27.7264 26.868V14.004C27.7264 13.624 27.5758 13.261 27.3078 12.993C27.0397 12.725 26.6761 12.574 26.297 12.574ZM20.9369 16.862C21.1489 16.862 21.3562 16.925 21.5325 17.043C21.7088 17.161 21.8462 17.328 21.9273 17.524C22.0084 17.72 22.0297 17.936 21.9883 18.144C21.9469 18.351 21.8448 18.542 21.6949 18.692C21.545 18.842 21.354 18.944 21.146 18.986C20.9381 19.027 20.7225 19.006 20.5266 18.925C20.3307 18.844 20.1633 18.706 20.0455 18.53C19.9277 18.354 19.8648 18.146 19.8648 17.934C19.8648 17.65 19.9778 17.377 20.1788 17.176C20.3799 16.975 20.6526 16.862 20.9369 16.862ZM26.297 26.868H10.5739V23.356L14.7137 19.215C14.7801 19.149 14.8589 19.096 14.9457 19.06C15.0325 19.024 15.1255 19.006 15.2194 19.006C15.3133 19.006 15.4063 19.024 15.4931 19.06C15.5798 19.096 15.6586 19.149 15.725 19.215L21.7409 25.23C21.875 25.364 22.0569 25.439 22.2465 25.439C22.4362 25.439 22.6181 25.364 22.7522 25.23C22.8863 25.095 22.9616 24.914 22.9616 24.724C22.9616 24.534 22.8863 24.352 22.7522 24.218L21.1745 22.641L22.4556 21.36C22.5896 21.226 22.7713 21.15 22.9608 21.15C23.1502 21.15 23.332 21.226 23.466 21.36L26.297 24.194V26.868Z"
                    fill="#810100" />
                </svg>
              </button>
              <button class="action-btn">
                <!-- Video Icon -->
                <svg width="24" height="24" viewBox="0 0 40 40" fill="none">
                  <circle cx="20" cy="20" r="18.5" fill="#810100" fill-opacity="0.2" />
                  <g clip-path="url(#clip0_videocam)">
                    <path
                      d="M25.653 14.933V24.939C25.653 25.318 25.502 25.682 25.234 25.95C24.966 26.218 24.603 26.368 24.223 26.368H11.3591C10.98 26.368 10.6164 26.218 10.3483 25.95C10.0803 25.682 9.9297 25.318 9.9297 24.939V14.933C9.9297 14.554 10.0803 14.191 10.3483 13.923C10.6164 13.655 10.98 13.504 11.3591 13.504H24.223C24.603 13.504 24.966 13.655 25.234 13.923C25.502 14.191 25.653 14.554 25.653 14.933ZM30.834 14.956C30.733 14.931 30.629 14.928 30.526 14.947C30.424 14.965 30.327 15.006 30.242 15.065L27.241 17.065C27.192 17.097 27.152 17.142 27.124 17.194C27.097 17.246 27.082 17.303 27.082 17.362V22.51C27.082 22.569 27.097 22.627 27.124 22.679C27.152 22.73 27.192 22.775 27.241 22.807L30.259 24.819C30.372 24.895 30.504 24.936 30.64 24.939C30.776 24.942 30.909 24.906 31.025 24.836C31.133 24.768 31.221 24.673 31.281 24.561C31.342 24.449 31.372 24.323 31.37 24.196V15.648C31.37 15.489 31.318 15.335 31.221 15.21C31.124 15.085 30.988 14.995 30.834 14.956Z"
                      fill="#810100" />
                  </g>
                  <defs>
                    <clipPath id="clip0_videocam">
                      <rect width="21.44" height="21.44" fill="white" transform="translate(9.93 12.5)" />
                    </clipPath>
                  </defs>
                </svg>
              </button>
              <button class="action-btn" @click="startRecording" :disabled="isRecording || audioUrl">
                <!-- Mic Icon -->
                <svg width="24" height="24" viewBox="0 0 40 40" fill="none">
                  <circle cx="20" cy="20" r="18.5" fill="#810100" :fill-opacity="isRecording ? '0.8' : '0.2'" />
                  <g clip-path="url(#clip1_mic)">
                    <path
                      d="M15.648 19.935V14.218C15.648 13.081 16.1 11.99 16.904 11.186C17.708 10.381 18.799 9.93 19.936 9.93C21.073 9.93 22.164 10.381 22.968 11.186C23.772 11.99 24.224 13.081 24.224 14.218V19.935C24.224 21.073 23.772 22.163 22.968 22.967C22.164 23.772 21.073 24.223 19.936 24.223C18.799 24.223 17.708 23.772 16.904 22.967C16.1 22.163 15.648 21.073 15.648 19.935ZM27.083 19.935C27.083 19.746 27.008 19.564 26.873 19.43C26.739 19.296 26.558 19.221 26.368 19.221C26.179 19.221 25.997 19.296 25.863 19.43C25.729 19.564 25.653 19.746 25.653 19.935C25.653 21.452 25.051 22.906 23.979 23.978C22.907 25.05 21.452 25.653 19.936 25.653C18.42 25.653 16.965 25.05 15.893 23.978C14.821 22.906 14.218 21.452 14.218 19.935C14.218 19.746 14.143 19.564 14.009 19.43C13.875 19.296 13.693 19.221 13.504 19.221C13.314 19.221 13.132 19.296 12.998 19.43C12.864 19.564 12.789 19.746 12.789 19.935C12.791 21.706 13.45 23.414 14.638 24.727C15.826 26.041 17.459 26.867 19.221 27.046V29.941C19.221 30.13 19.297 30.312 19.431 30.446C19.565 30.58 19.746 30.656 19.936 30.656C20.125 30.656 20.307 30.58 20.441 30.446C20.575 30.312 20.651 30.13 20.651 29.941V27.046C22.413 26.867 24.046 26.041 25.234 24.727C26.422 23.414 27.081 21.706 27.083 19.935Z"
                      fill="#810100" />
                  </g>
                  <defs>
                    <clipPath id="clip1_mic">
                      <rect width="21.44" height="21.44" fill="white" transform="translate(9.28 9.28)" />
                    </clipPath>
                  </defs>
                </svg>
              </button>
            </div>
            <div class="action-group-right">
              <button class="action-btn">
                <!-- Location Icon -->
                <svg width="24" height="24" viewBox="0 0 40 40" fill="none">
                  <circle cx="20" cy="20" r="18.5" fill="#810100" fill-opacity="0.2" />
                  <path
                    d="M19.933 9.93C17.849 9.932 15.851 10.761 14.377 12.235C12.904 13.708 12.075 15.706 12.072 17.79C12.072 20.595 13.368 23.568 15.824 26.388C16.927 27.663 18.169 28.81 19.527 29.809C19.647 29.894 19.79 29.939 19.937 29.939C20.083 29.939 20.226 29.894 20.347 29.809C21.702 28.81 22.941 27.662 24.042 26.388C26.494 23.568 27.794 20.595 27.794 17.79C27.791 15.706 26.963 13.708 25.489 12.235C24.015 10.761 22.017 9.932 19.933 9.93ZM22.792 18.505H20.648V20.649C20.648 20.838 20.572 21.02 20.438 21.154C20.304 21.288 20.123 21.364 19.933 21.364C19.744 21.364 19.562 21.288 19.428 21.154C19.294 21.02 19.218 20.838 19.218 20.649V18.505H17.075C16.885 18.505 16.703 18.43 16.569 18.296C16.435 18.162 16.36 17.98 16.36 17.79C16.36 17.601 16.435 17.419 16.569 17.285C16.703 17.151 16.885 17.076 17.075 17.076H19.218V14.932C19.218 14.742 19.294 14.561 19.428 14.427C19.562 14.293 19.744 14.217 19.933 14.217C20.123 14.217 20.304 14.293 20.438 14.427C20.572 14.561 20.648 14.742 20.648 14.932V17.076H22.792C22.981 17.076 23.163 17.151 23.297 17.285C23.431 17.419 23.506 17.601 23.506 17.79C23.506 17.98 23.431 18.162 23.297 18.296C23.163 18.43 22.981 18.505 22.792 18.505Z"
                    fill="#810100" />
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- Right Column: Mood Selector & Footer -->
      <div class="context-section">
        <!-- Mood Selector -->
        <div class="mood-selector">
          <h2 class="mood-title">How you feeling right now?</h2>
          <div class="mood-options">
            <button v-for="(mood, index) in moods" :key="index" class="mood-btn"
              :class="{ active: selectedMood === mood.name }" :style="{ backgroundColor: mood.color }"
              @click="selectedMood = mood.name">
              <div class="mood-icon" v-html="mood.icon"></div>
              <span class="mood-label">{{ mood.name }}</span>
            </button>
          </div>
        </div>

        <!-- Footer (Fold it) -->
        <footer class="footer">
          <button class="fold-btn" @click="saveMemory">
            Fold it
          </button>
        </footer>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['navigate'])
const memoryText = ref('')
const selectedMood = ref('Normal')

const moods = [
  {
    name: 'V. Sad',
    color: '#DCBCBC',
    icon: `<svg width="24" height="24" viewBox="0 0 24 24" fill="none"><path d="M12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22Z" stroke="#181717" stroke-width="1.5"/><path d="M8 12C8 12 9 13 10 13C11 13 12 12 12 12M12 12C12 12 13 13 14 13C15 13 16 12 16 12" stroke="#181717" stroke-width="1.5" stroke-linecap="round"/><path d="M8 16C8 16 9.5 17 12 17C14.5 17 16 16 16 16" stroke="#181717" stroke-width="1.5" stroke-linecap="round"/><path d="M9 9H9.01M15 9H15.01" stroke="#181717" stroke-width="2" stroke-linecap="round"/></svg>`
  },
  {
    name: 'Sad',
    color: '#E5D4D4',
    icon: `<svg width="24" height="24" viewBox="0 0 24 24" fill="none"><path d="M12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22Z" stroke="#181717" stroke-width="1.5"/><path d="M8 16C8 16 9.5 15 12 15C14.5 15 16 16 16 16" stroke="#181717" stroke-width="1.5" stroke-linecap="round"/><path d="M9 9H9.01M15 9H15.01" stroke="#181717" stroke-width="2" stroke-linecap="round"/></svg>`
  },
  {
    name: 'Normal',
    color: '#E5E3D4',
    icon: `<svg width="24" height="24" viewBox="0 0 24 24" fill="none"><path d="M12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22Z" stroke="#181717" stroke-width="1.5"/><path d="M8 16H16" stroke="#181717" stroke-width="1.5" stroke-linecap="round"/><path d="M9 9H9.01M15 9H15.01" stroke="#181717" stroke-width="2" stroke-linecap="round"/></svg>`
  },
  {
    name: 'Happy',
    color: '#D4E5D5',
    icon: `<svg width="24" height="24" viewBox="0 0 24 24" fill="none"><path d="M12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22Z" stroke="#181717" stroke-width="1.5"/><path d="M8 15C8 15 9.5 17 12 17C14.5 17 16 15 16 15" stroke="#181717" stroke-width="1.5" stroke-linecap="round"/><path d="M9 9H9.01M15 9H15.01" stroke="#181717" stroke-width="2" stroke-linecap="round"/></svg>`
  },
  {
    name: 'V. Happy',
    color: '#BCDCBE',
    icon: `<svg width="24" height="24" viewBox="0 0 24 24" fill="none"><path d="M12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22Z" stroke="#181717" stroke-width="1.5"/><path d="M8 15C8 15 9.5 17 12 17C14.5 17 16 15 16 15" stroke="#181717" stroke-width="1.5" stroke-linecap="round"/><path d="M7 9C7 9 8 10 9 10C10 10 11 9 11 9M13 9C13 9 14 10 15 10C16 10 17 9 17 9" stroke="#181717" stroke-width="1.5" stroke-linecap="round"/></svg>`
  }
]

// Audio Recording State
const isRecording = ref(false)
const recordingTime = ref(0)
const audioUrl = ref(null)
const audioBlob = ref(null)
const waveform = ref(new Array(30).fill(10)) // Visualizer data
let mediaRecorder = null
let audioChunks = []
let timerInterval = null
let animationInterval = null

const formattedTime = computed(() => {
  const mins = Math.floor(recordingTime.value / 60)
  const secs = recordingTime.value % 60
  return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`
})

async function startRecording() {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ audio: true })
    mediaRecorder = new MediaRecorder(stream)
    audioChunks = []

    mediaRecorder.ondataavailable = (event) => {
      audioChunks.push(event.data)
    }

    mediaRecorder.onstop = () => {
      audioBlob.value = new Blob(audioChunks, { type: 'audio/wav' })
      audioUrl.value = URL.createObjectURL(audioBlob.value)
      stopTimer()
      stopWaveformAnimation()
    }

    mediaRecorder.start()
    isRecording.value = true
    startTimer()
    startWaveformAnimation()
  } catch (error) {
    console.error('Error accessing microphone:', error)
    alert('Could not access microphone. Please ensure permissions are granted.')
  }
}

function stopRecording() {
  if (mediaRecorder && mediaRecorder.state !== 'inactive') {
    mediaRecorder.stop()
    isRecording.value = false
    // Stop tracks to release microphone
    mediaRecorder.stream.getTracks().forEach(track => track.stop())
  }
}

function discardRecording() {
  audioUrl.value = null
  audioBlob.value = null
  recordingTime.value = 0
  if (isRecording.value) {
    stopRecording()
  }
  isRecording.value = false
}

function startTimer() {
  recordingTime.value = 0
  timerInterval = setInterval(() => {
    recordingTime.value++
  }, 1000)
}

function stopTimer() {
  clearInterval(timerInterval)
}

function startWaveformAnimation() {
  // Simple random visualization matching the Timeline aesthetic
  animationInterval = setInterval(() => {
    waveform.value = waveform.value.map(() => Math.max(10, Math.floor(Math.random() * 40)))
  }, 100)
}

function stopWaveformAnimation() {
  clearInterval(animationInterval)
  waveform.value = new Array(30).fill(10) // Reset
}

function toggleRecordingState() {
  if (isRecording.value) {
    stopRecording()
  } else {
    // If already recorded and stopped, clicking center might mean Play?
    // For now, let's play the audio
    const audio = new Audio(audioUrl.value)
    audio.play()
  }
}

function confirmAudio() {
  // If we have a blob, we consider it "saved" to the memory
  // In a real app this might attach it. here we just clear the UI state but keep the blob?
  // Or maybe it emits immediately?
  // Let's assume hitting checkmark is "Done with audio part", effectively hiding the big recorder
  // but showing a small "Audio Attached" indicator in the main card.
  // BUT the user request implementation plan was just to have the recorder.
  // For simplicity, let's keep it visible or just saveMemory immediately?
  // Let's make it calling saveMemory() if that's the intent of the "Fold it" equivalent?
  // Or maybe it just keeps the audioUrl and returns to the textarea?
  // User design has a dedicated Checkmark. This usually means "Confirm this recording".
  // Let's just minimize audio back to a pill or something?
  // For now: Just Trigger Save
  saveMemory()
}

function saveMemory() {
  // Logic to save memory (text or audio)
  if (memoryText.value.trim() || audioBlob.value) {
    emit('navigate', 'timeline')
  }
}
</script>

<style scoped>
.new-memory-page {
  min-height: 100vh;
  background: #EDEADC;
  padding: 20px;
  display: flex;
  flex-direction: column;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  margin: 0 auto;
  transition: all 0.3s ease;
}

/* Header */
.header {
  display: flex;
  align-items: center;
  margin-bottom: 24px;
  position: relative;
  height: 48px;
}

.close-btn {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0;
  transition: transform 0.2s ease;
  position: absolute;
  left: 0;
  z-index: 10;
}

.close-btn:hover {
  transform: scale(1.1);
}

.page-title {
  flex: 1;
  text-align: center;
  font-size: 20px;
  font-weight: 700;
  color: #181717;
  margin: 0;
}

.content {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 24px;
}

/* Input Card */
.input-card {
  background: rgba(0, 0, 0, 0.05);
  /* Slight dark overlay as seen in SVG */
  border-radius: 24px;
  padding: 24px;
  height: 400px;
  display: flex;
  flex-direction: column;
  position: relative;
  transition: all 0.3s ease;
}

.input-card:focus-within {
  background: rgba(0, 0, 0, 0.07);
}

.memory-input {
  width: 100%;
  flex: 1;
  background: transparent;
  border: none;
  resize: none;
  font-size: 20px;
  font-weight: 500;
  color: #181717;
  outline: none;
  font-family: inherit;
  line-height: 1.5;
}

.memory-input::placeholder {
  color: rgba(24, 23, 23, 0.3);
  font-weight: 700;
}

/* Recorder Container */
.recorder-container {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 32px;
  width: 100%;
}

.timer {
  font-size: 80px;
  font-weight: 700;
  color: #181717;
  font-family: 'Inter', sans-serif;
  font-variant-numeric: tabular-nums;
  letter-spacing: -2px;
}

.waveform {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  height: 64px;
}

.bar {
  width: 8px;
  background: #810100;
  border-radius: 99px;
  transition: height 0.1s ease;
  height: 12px;
}

/* Controls Row */
.recorder-controls {
  display: flex;
  align-items: center;
  gap: 40px;
}

.control-btn {
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.2s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.control-btn:hover {
  transform: scale(1.1);
}

.control-btn.small {
  width: 64px;
  height: 64px;
  border-radius: 50%;
  background: #FDFBF7;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.control-btn.large {
  width: 96px;
  height: 96px;
  border-radius: 50%;
  background: #181717;
  color: white;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
}

.icon-dark {
  color: #181717;
}

.inner-stop {
  width: 32px;
  height: 32px;
  background: #FDFBF7;
  border-radius: 8px;
}

.action-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 16px;
}

.action-group-left {
  display: flex;
  gap: 12px;
}

.action-btn {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0;
  transition: transform 0.2s ease;
}

.action-btn:hover {
  transform: scale(1.1);
}

/* Mood Selector */
.mood-selector {
  background: rgba(0, 0, 0, 0.05);
  border-radius: 24px;
  padding: 20px;
}

.mood-title {
  font-size: 18px;
  font-weight: 700;
  color: #181717;
  margin: 0 0 16px 0;
}

.mood-options {
  display: flex;
  justify-content: space-between;
  gap: 8px;
}

.mood-btn {
  flex: 1;
  border: 1px solid #181717;
  border-radius: 12px;
  padding: 12px 4px;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  transition: all 0.2s ease;
}

.mood-btn.active {
  transform: scale(1.05);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border-width: 2px;
}

.mood-btn:hover {
  transform: translateY(-2px);
}

.mood-label {
  font-size: 11px;
  font-weight: 600;
  color: #181717;
}

/* Footer & Fold Button (Default Mobile) */
.footer {
  margin-top: auto;
  padding-top: 24px;
}

.fold-btn {
  width: 100%;
  background: #810100;
  color: white;
  border: none;
  border-radius: 30px;
  padding: 16px;
  font-size: 20px;
  font-style: italic;
  cursor: pointer;
  transition: all 0.3s ease;
  font-family: serif;
  /* Using serif to match the "Fold it" cursive style */
}

.fold-btn:hover {
  transform: scale(1.02);
  box-shadow: 0 4px 20px rgba(129, 1, 0, 0.3);
}

/* Desktop Responsiveness */
@media (min-width: 1024px) {
  .new-memory-page {
    padding: 40px;
    max-width: 1200px;
    justify-content: center;
  }

  .header {
    margin-bottom: 40px;
    height: auto;
    justify-content: center;
  }

  .page-title {
    font-size: 48px;
    text-align: center;
  }

  .close-btn {
    left: 0;
    width: 48px;
    height: 48px;
  }

  .close-btn svg {
    width: 48px;
    height: 48px;
  }

  /* Grid Layout for Content */
  .content {
    display: grid;
    grid-template-columns: 2fr 1fr;
    gap: 40px;
    align-items: start;
  }

  /* Left Column */
  .input-section {
    height: 100%;
  }

  .input-card {
    height: 600px;
    padding: 40px;
    border-radius: 32px;
  }

  .memory-input {
    font-size: 32px;
    line-height: 1.6;
  }

  .action-bar {
    margin-top: 32px;
  }

  .action-btn svg {
    width: 32px;
    height: 32px;
  }

  /* Right Column */
  .context-section {
    display: flex;
    flex-direction: column;
    gap: 24px;
  }

  .mood-selector {
    padding: 32px;
  }

  .mood-title {
    font-size: 24px;
    margin-bottom: 24px;
  }

  .mood-options {
    flex-direction: column;
    gap: 12px;
  }

  .mood-btn {
    flex-direction: row;
    padding: 16px 24px;
    justify-content: flex-start;
    gap: 16px;
    border-radius: 16px;
  }

  .mood-label {
    font-size: 16px;
  }

  .footer {
    padding-top: 0;
    margin-top: 0;
  }

  .fold-btn {
    padding: 24px;
    font-size: 24px;
    border-radius: 40px;
  }
}
</style>
