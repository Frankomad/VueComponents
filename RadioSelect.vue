<template>
  <div class="component content RadioSelect" :class="this.classes">
    <label class="radio" v-for="option in options" :key="option.value">
      <input
        type="radio"
        :value="option.value"
        v-model="selectedValue"
        :checked="option.value === selectedValue"
      />
      <span v-html="option.text"></span>
    </label>
  </div>
</template>

<script>
import BaseValue from "armory-sdk/src/components/base/BaseValue.vue";

export default {
  name: "RadioSelect",
  extends: BaseValue,
  props: {
    options: {
      type: Array,
      required: true,
    },
    classes: {
      type: Array,
      default: () => [],
    },
  },
  mounted: function () {
    this.selectedValue = this.value || null;
  },
  computed: {
    selectedValue: {
      get() {
        const inputState = this.$store.state.inputs[this.inputId];
        return inputState ? inputState.value : this.value || null;
      },
      set(value) {
        this.$store.commit("setInputValue", {
          inputId: this.inputId,
          value: {
            value: value,
          },
        });
        this.$store.commit("setInputValidation", {
          inputId: this.inputId,
          valid: this.selectedValue !== null,
        });
      },
    },
  },
};
</script>

<style scoped>
.RadioSelect {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.RadioSelect > label {
  font-family: Helvetica, sans-serif;
  margin-left: 0;
  display: flex;
  gap: 0.5rem;
  font-size: 1.2rem;
}

input[type="radio"] {
  width: 22px;
  height: 22px;
}
</style>
