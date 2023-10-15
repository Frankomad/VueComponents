<template>
  <div class="RangeSlider component content">
    <b-slider
      v-if="inputValue"
      v-model="inputValue"
      :min="minValue"
      :max="maxValue"
      :step="step"
      class="custom-slider"
    >
    </b-slider>
    <div class="range-values" v-text="formattedLabel"></div>
  </div>
</template>

<script>
import BaseValue from "armory-sdk/src/components/base/BaseValue.vue";
export default {
  name: "RangeSlider",
  extends: BaseValue,
  props: {
    value: {
      type: Array,
      default() {
        return null;
      },
    },
    minValue: Number,
    maxValue: Number,
    step: Number,
    label: {
      type: String,
      default: "Selected range ${lowerValue} - ${upperValue}",
    },
  },
  mounted() {
    if (!this.inputValue) {
      this.inputValue = [this.minValue, this.maxValue];
    }
  },
  computed: {
    formattedLabel() {
      return this.label
        .replace("${lowerValue}", this.inputValue[0])
        .replace("${upperValue}", this.inputValue[1]);
    },
  },
};
</script>

<style lang="scss">
@import "~@/assets/css/skin.scss";
.RangeSlider {
  padding: 1rem;

  .custom-slider .b-slider-track {
    height: 0.75em !important;
  }

  .custom-slider .b-tooltip {
    background-color: transparent !important;
  }

  .custom-slider .b-slider-handle:hover {
    background-color: $primary;
  }

  .b-tooltip {
    background-color: $secondary;
  }

  .b-slider .b-slider-track {
    background-color: $secondary;
  }

  .custom-slider .b-slider-thumb {
    background-color: $primary !important;
    width: 1.7em;
    height: 1.7em;
    border-radius: 50% !important;
    border: none !important;
  }

  .range-values {
    margin-top: 2rem;
    font-weight: bold;
    font-size: 1.3em;
    text-align: center;
  }
}
</style>
