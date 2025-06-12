<template>
  <div id="app">
    <h1>🌕 Werewolf Night Builder</h1>

    <CharacterSelector @characters-selected="handleSelection" />

    <div class="controls">
      <label>Delay between clips:</label>
      <div class="delay-control">
        <button @click="adjustDelay(-250)">−</button>
        <span>{{ (delayBetweenClips / 1000).toFixed(2) }}s</span>
        <button @click="adjustDelay(250)">+</button>
      </div>
    </div>

    <div class="actions">
      <button class="primary" @click="playAudio">▶ Play Sequence</button>
    </div>

    <div class="timeline">
      <h2>🎧 Audio Timeline</h2>
      <ul>
        <li v-for="(clip, index) in audioTimeline" :key="index">{{ clip }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import CharacterSelector from './components/CharacterSelector.vue'
import { Howl } from 'howler'

export default {
  components: { CharacterSelector },
  data() {
    return {
      audioTimeline: [],
      delayBetweenClips: 1000 // milliseconds
    }
  },
  methods: {
    availableRoles() {
      return [
        { name: 'Sentinel', zIndex: 1 },
        { name: 'Doppelganger', zIndex: 2 },
        { name: 'Minion_Doppelganger', zIndex: 3 },
        { name: 'Werewolf', zIndex: 4 },
        { name: 'Minion', zIndex: 5 },
        { name: 'Seer', zIndex: 6 },
        { name: 'Robber', zIndex: 7 },
        { name: 'Troublemaker', zIndex: 8 },
        { name: 'Drunk', zIndex: 9 },
        { name: 'Insomniac', zIndex: 10 },
        { name: 'Insomniac_Doppelganger', zIndex: 11 },
        { name: 'Curator', zIndex: 12 },
        { name: 'Curator_Doppelganger', zIndex: 13 }
      ]
    },
    handleSelection(selected) {
      const audioMap = {
        Sentinel: 'sentinel_turn.mp3',
        Doppelganger: 'doppelganger_turn.mp3',
        Minion_Doppelganger: 'minion_doppelganger_turn.mp3',
        Werewolf: 'werewolf_turn.mp3',
        Minion: 'minion_turn.mp3',
        Seer: 'seer_turn.mp3',
        Robber: 'robber_turn.mp3',
        Troublemaker: 'troublemaker_turn.mp3',
        Drunk: 'drunk_turn.mp3',
        Insomniac: 'insomniac_turn.mp3',
        Insomniac_Doppelganger: 'insomniac_doppelganger_turn.mp3',
        Curator: 'curator_turn.mp3',
        Curator_Doppelganger: 'curator_doppelganger_turn.mp3'
      }

      const baseRoles = this.availableRoles().map(r => r.name)
      const finalRoles = [...selected]

      if (selected.includes('Doppelganger')) {
        selected.forEach(role => {
          if (role !== 'Doppelganger') {
            const doppleVariant = `${role}_Doppelganger`
            if (baseRoles.includes(doppleVariant) && !finalRoles.includes(doppleVariant)) {
              finalRoles.push(doppleVariant)
            }
          }
        })
      }

      const sortedRoles = this.availableRoles()
        .filter(role => finalRoles.includes(role.name))
        .sort((a, b) => a.zIndex - b.zIndex)

      const timeline = ['intro_night.mp3']
      sortedRoles.forEach(role => {
        const file = audioMap[role.name]
        if (file) timeline.push(file)
      })
      timeline.push('morning_announcement.mp3')

      this.audioTimeline = timeline
    },
    async playAudio() {
      const base_url = import.meta.env.BASE_URL
      for (const file of this.audioTimeline) {
        await this.playSound(`${base_url}/audio/${file}`)
        await this.sleep(this.delayBetweenClips)
      }
    },
    playSound(src) {
      return new Promise(resolve => {
        const sound = new Howl({
          src: [src],
          onend: resolve
        })
        sound.play()
      })
    },
    sleep(ms) {
      return new Promise(res => setTimeout(res, ms))
    },
    adjustDelay(amount) {
      const newDelay = this.delayBetweenClips + amount
      this.delayBetweenClips = Math.max(0, newDelay)
    }
  }
}
</script>

<style scoped>
#app {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  padding: 30px;
  text-align: center;
  background: #1a1a1a;
  color: #f2f2f2;
}

h1 {
  font-size: 2.4rem;
  margin-bottom: 1rem;
  color: #f7e89c;
  text-shadow: 1px 1px 3px #000;
}

.controls {
  margin: 30px 0 10px;
}

.delay-control {
  display: inline-flex;
  align-items: center;
  gap: 12px;
  margin-top: 10px;
}

.delay-control button {
  font-size: 1.4rem;
  width: 40px;
  height: 40px;
  background-color: #444;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s ease;
}

.delay-control button:hover {
  background-color: #666;
}

.delay-control span {
  font-size: 1.2rem;
  width: 80px;
  display: inline-block;
  text-align: center;
  background-color: #222;
  padding: 8px 0;
  border-radius: 6px;
}

.actions {
  margin-top: 20px;
}

button.primary {
  background: #009e60;
  border: none;
  color: white;
  padding: 12px 24px;
  font-size: 1.1rem;
  border-radius: 10px;
  cursor: pointer;
  transition: background 0.2s ease;
}

button.primary:hover {
  background: #00cc7a;
}

.timeline {
  margin-top: 40px;
  text-align: left;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

.timeline h2 {
  font-size: 1.5rem;
  margin-bottom: 10px;
  color: #ffd37a;
}

.timeline ul {
  list-style: none;
  padding: 0;
}

.timeline li {
  background: #2a2a2a;
  padding: 10px;
  margin: 6px 0;
  border-radius: 6px;
  color: #eee;
  font-family: monospace;
}
</style>
