<template>
  <div class="GroupedComponents component content">
    <div
      v-for="(component, index) in duplicateChildComponents"
      :key="index + '-parent'"
      class="component content"
    >
      <component
        :is="name"
        v-for="(spec, name) in component"
        :key="index + '-' + name"
        :inputId="getFormattedInputId(spec.inputId, index)"
        v-bind="spec"
      >
      </component>
    </div>
    <div id="container">
      <button @click="addItem">{{ addText }}</button>
      <button @click="removeItem">{{ removeText }}</button>
    </div>
  </div>
</template>

<script>
import GenericTemplate from "armory-sdk/src/templates/GenericTemplate.vue";
import App from "@/App.vue";

export default {
  name: "GroupedComponents",
  props: {
    addText: {
      type: String,
    },
    removeText: {
      type: String,
    },
    initialCount: {
      type: Number,
    },
    childComponents: {
      type: Array,
      required: true,
    },
  },
  mounted() {
    // eslint-disable-next-line vue/no-mutating-props
    this.removeText = this.removeText || "Remove item";
    // eslint-disable-next-line vue/no-mutating-props
    this.addText = this.addText || "Add item";
  },
  data() {
    return {
      currentDuplication: this.initialCount || 1,
    };
  },
  computed: {
    duplicateChildComponents() {
      const duplicatedComponents = [];
      for (let i = 0; i < this.currentDuplication; i++) {
        duplicatedComponents.push(...this.childComponents);
      }
      return duplicatedComponents;
    },
  },
  beforeMount() {
    const allComponents = this.getAllComponents();
    for (const componentId in allComponents)
      this.$options.components[componentId] = allComponents[componentId];
  },
  methods: {
    getAllComponents() {
      return {
        ...GenericTemplate.components,
        ...App.methods.getCustomComponents(),
      };
    },
    addItem() {
      this.currentDuplication += 1;
    },
    removeItem() {
      if (this.currentDuplication > 1) {
        this.childComponents.map((component) => {
          const deletedInputId = `${component.Input.inputId}${this.currentDuplication}`;
          this.$store.state.inputs[deletedInputId] = null;
          this.$store.commit("setInputValidation", {
            inputId: deletedInputId,
            valid: true,
          });
        });
        this.currentDuplication -= 1;
      }
    },
    getFormattedInputId(originalInputId, index) {
      const partsCount = this.childComponents.length;
      const partIndex = Math.floor(index / partsCount) + 1;
      return `${originalInputId}${partIndex}`;
    },
  },
};
</script>

<style lang="scss" scoped>
@import "~@/assets/css/skin.scss";

.switch {
  display: flex;
  justify-content: space-between;
}

#container {
  display: flex;
  justify-content: flex-end;
  gap: 5px;
}

button {
  height: 1.75rem;
  border: unset;
  box-shadow: unset;
  background-color: $primary;
  color: white;
  border-radius: 999px;
  padding: 8px 12px 8px 12px;
}

::v-deep span.control-label {
  font-weight: bold;
}
</style>
