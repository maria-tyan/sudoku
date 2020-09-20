<template>
  <div
    id="select-component"
    class="select-component"
    :class="[
      'select-component_selected-' + selectedOption,
    ]"
  >
    <div
      v-for="item in optionsList"
      :key="item.option"
      class="select-component__option"
    >
      <input
        :id="item.name"
        type="radio"
        name="option"
        class="select-component__input"
        :value="item.option"
        @change="selectOption(item.option)"
      >
      <label
        :for="item.name"
        class="select-component__label"
      >
        {{ item.label }}
      </label>
    </div>
  </div>
</template>

<script>
export default {
  name: 'selectComponent',
  data() {
    return {
      selectedOption: 0,
      optionsList: [
        {
          name: 'beginner',
          option: 0,
          label: 'Beginner'
        },
        {
          name: 'easy',
          option: 1,
          label: 'Easy'
        },
        {
          name: 'medium',
          option: 2,
          label: 'Medium'
        },
        {
          name: 'hard',
          option: 3,
          label: 'Hard'
        },
      ],
    };
  },
  methods: {
    selectOption(index) {
      this.selectedOption = index
    },
  }
}
</script>

<style lang="less">
  @import (reference) "../less/variables.less";
  @import "../less/select-component.less";
</style>
