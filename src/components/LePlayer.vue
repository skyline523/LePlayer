<script setup lang="ts">
import { formatTime } from '~/utils';

defineOptions({
  name: 'LePlayer',
})

const props = defineProps({
  src: {
    type: String,
    required: true,
  },
})

const audioRef = ref<HTMLAudioElement | null>(null)

const isPlaying = ref(false)
const currentTime = ref(0)
const duration = ref(0)
const volume = ref(10)

const formattedCurrentTime = computed<string>(() => formatTime(currentTime.value))
const formattedDuration = computed<string>(() => formatTime(duration.value))

onMounted(() => {
  if (audioRef.value) {
    audioRef.value.volume = volume.value / 100
    audioRef.value.addEventListener('timeupdate', updateCurrentTime)
    audioRef.value.addEventListener('durationchange', updateDuration)
    audioRef.value.addEventListener('play', play)
    audioRef.value.addEventListener('pause', pause)
  }
})

onUnmounted(() => {
  if (audioRef.value) {
    audioRef.value.removeEventListener('timeupdate', updateCurrentTime)
    audioRef.value.removeEventListener('durationchange', updateDuration)
    audioRef.value.removeEventListener('play', play)
    audioRef.value.removeEventListener('pause', pause)
  }
})


function onPlay() {
  if (audioRef.value)
    audioRef.value.play()
}
function play() { isPlaying.value = true }
function pause() { isPlaying.value = false }

function onPause() {
  if (audioRef.value)
    audioRef.value.pause()

}

function updateCurrentTime() {
  if (audioRef.value) {
    currentTime.value = audioRef.value.currentTime
  }
}

function updateDuration() {
  if (audioRef.value) {
    duration.value = audioRef.value.duration
  }
}
</script>

<template>
  <div>
    <div>
      <div>
        <button @click="onPlay">Play</button>
        <span>/</span>
        <button @click="onPause">Pause</button>
      </div>
      <div>
        <span>{{ formattedCurrentTime }}</span>
        <span>/</span>
        <span>{{ formattedDuration }}</span>
      </div>
    </div>

    <audio ref="audioRef" :src="props.src" />
  </div>
</template>
