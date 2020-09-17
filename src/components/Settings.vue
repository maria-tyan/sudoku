<template>
  <div id="settings" class="settings">
    <div class="settings__button" @click="toggleSettings()">
      <font-awesome-icon icon="cog" />
    </div>

    <transition name="slide-down">
      <div v-if="active" class="settings__menu">
        <button
          @click="hideCells()"
          class="button"
        >
          Create a New Game
        </button>
        <button
          @click="hideCells()"
          class="button"
        >
          Show a hint
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
    };
  },
  methods: {
    toggleSettings() {
      this.active = !this.active
    }
  }
}
</script>

<style lang="less">
  @import (reference) "../less/variables.less";
  @import "../less/settings.less";
  @import "../less/transitions.less";  
</style>
