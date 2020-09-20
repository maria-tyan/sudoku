<template>
  <div id="settings" class="settings">
    <div class="settings__button" @click="toggleSettings()">
      <font-awesome-icon icon="cog" />
    </div>

    <transition name="slide-down">
      <div v-if="active" class="settings__menu">
        <button
          @click="newGame()"
          class="button"
        >
          Create a New Game
        </button>
        <button
          @click="showHint()"
          class="button"
        >
          Show a Hint
        </button>
        <select-component />
      </div>
    </transition>
  </div>
</template>

<script>
import SelectComponent from './Select.vue'

export default {
  name: 'Settings',
  components: {
    SelectComponent,
  },
  data() {
    return {
      active: false
    }
  },
  methods: {
    toggleSettings() {
      this.active = !this.active
    },
    newGame() {
      this.$root.$emit('new-game')
    },
    showHint() {
      console.log('show-hint1')
      this.$root.$emit('show-hint')
    },
  }
}
</script>

<style lang="less">
  @import (reference) "../less/variables.less";
  @import "../less/settings.less";
  @import "../less/transitions.less";  
</style>
