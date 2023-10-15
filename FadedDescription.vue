<template>
  <div class="FadedDescription component content">
    <div class="faded" v-html="text"></div>
  </div>
</template>

<script>
export default {
  name: "FadedDescription",
  props: {
    text: String,
  },
  data() {
    return {
      isAtBottom: false,
    };
  },
  mounted() {
    this.resizeDescription();
    this.addScrollHeight();
    window.addEventListener("resize", this.resizeDescription);
  },
  methods: {
    resizeDescription() {
      const headerGroup = document.querySelector("#HeaderGroup--internal");
      const fadedDescription = document.querySelector(".FadedDescription");

      fadedDescription.style.height = 0;

      const headerComponentsHeight = headerGroup.clientHeight;

      const app = document.querySelector("#app");
      const appPadding =
        parseFloat(window.getComputedStyle(app).paddingBottom) +
        parseFloat(window.getComputedStyle(app).paddingTop);

      const componentMargin = parseFloat(
        window.getComputedStyle(document.querySelector(".component"))
          .marginBottom
      );

      const footerHeight = document.querySelector(
        "#FooterGroup--internal"
      ).clientHeight;

      const legalElement = document.querySelector(".legal");
      const legalHeight =
        legalElement.clientHeight +
        parseFloat(window.getComputedStyle(legalElement).marginTop);

      fadedDescription.style.height = `calc(100vh - ${
        headerComponentsHeight +
        appPadding +
        componentMargin +
        footerHeight +
        legalHeight
      }px)`;
    },
    addScrollHeight() {
      const fadeHeight = 200;
      const textHeight =
        document.querySelector(".faded").offsetHeight + fadeHeight;
      document.querySelector(".faded").style.height = `${textHeight}px`;
    },
  },
};
</script>

<style scoped>
.FadedDescription {
  mask-image: linear-gradient(
    to bottom,
    black calc(100% - 200px),
    transparent 110%
  );
  overflow-y: auto;
}
</style>
