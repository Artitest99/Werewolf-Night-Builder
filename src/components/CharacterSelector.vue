<template>
  <div class="character-grid">
    <div
      v-for="character in characters"
      :key="character"
      class="character"
      :class="{ selected: selectedCharacters.includes(character) }"
      @click="toggleCharacter(character)"
    >
      <img :src="calcCharacterImg(character)" :alt="character" />
      <span>{{ character }}</span>
    </div>

    <div class="actions">
      <button class="primary" @click="confirmSelection">Confirm Selection</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      characters: [
        'Sentinel',
        'Doppelganger',
        'Werewolf',
        'Minion',
        'Seer',
        'Robber',
        'Troublemaker',
        'Drunk',
        'Insomniac',
        'Curator'
      ],
      selectedCharacters: 
        [
        'Werewolf', 
        'Seer',
        'Robber',
        'Troublemaker',
        'Drunk',
        'Insomniac',]
    }
  },
  methods: {
    toggleCharacter(character) {
      const index = this.selectedCharacters.indexOf(character)
      if (index === -1) {
        this.selectedCharacters.push(character)
      } else {
        this.selectedCharacters.splice(index, 1)
      }
    },
    confirmSelection() {
      this.$emit('characters-selected', this.selectedCharacters)
    },
    calcCharacterImg(character){
      const base_url = import.meta.env.BASE_URL
      return `${base_url}images/${character}.png`
    }
  }
}
</script>

<style scoped>
.character-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  max-width: 800px;
  margin: 0 auto;
  justify-content: center;
}

button.primary {
  background: #009e60;
  border: none;
  color: white;
  padding: 12px 24px;
  font-size: 1.0rem;
  border-radius: 10px;
  cursor: pointer;
  transition: background 0.2s ease;
}

button.primary:hover {
  background: #00cc7a;
}

.character {
  width: 120px;
  text-align: center;
  cursor: pointer;
  transition: transform 0.2s ease;
  opacity: 0.4;
}

.character img {
  width: 100%;
  height: auto;
  border-radius: 12px;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.3s;
}

.character.selected {
  opacity: 1;
  transform: scale(1.05);
}

.character.selected img {
  box-shadow: 0 0 12px rgba(255, 255, 255, 0.5);
}

.character span {
  display: block;
  margin-top: 6px;
  font-size: 14px;
  font-weight: 500;
  color: #333;
}

.actions {
  width: 100%;
  text-align: center;
  margin-top: 20px;
}
</style>
