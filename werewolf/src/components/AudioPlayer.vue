<template>
  <div>
    <h2>Audio Timeline</h2>
    <ul>
      <li v-for="(clip, i) in timeline" :key="i">{{ clip }}</li>
    </ul>
    <button @click="playTimeline" :disabled="playing">Play Round</button>
  </div>
</template>
<script>
import { Howl } from 'howler'

export default {
  props: {
    timeline: Array,
    delay: Number // milliseconds
  },
  data() {
    return {
      playing: false
    }
  },
  methods: {
    async playTimeline() {
      this.playing = true
      for (const file of this.timeline) {
        await this.playSound(`/audio/${file}`)
        await this.sleep(this.delay || 0)
      }
      this.playing = false
    },
    playSound(src) {
      return new Promise((resolve) => {
        const sound = new Howl({
          src: [src],
          onend: resolve
        })
        sound.play()
      })
    },
    sleep(ms) {
      return new Promise(res => setTimeout(res, ms))
    }
  }
}
</script>
