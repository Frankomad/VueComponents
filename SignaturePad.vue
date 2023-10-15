<template>
  <div class="SignaturePad content component">
    <div class="container">
      <VueSignaturePad
        id="signature"
        ref="signaturePad"
        height="100%"
        width="100%"
        :options="{ options, onEnd }"
      />
      <div id="button-container">
        <button @click="clearSignature()">
          <span class="clear">{{ clearText }}</span>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "SignaturePad",
  props: {
    inputId: String,
    clearText: {
      type: String,
      default: "Clear",
    },
    required: {
      type: Boolean,
      default: false,
    },
  },
  data: () => ({
    options: {
      penColor: "#000000",
    },
    signatureSrc: String,
  }),
  methods: {
    drawGuideLine(context, canvas) {
      context.beginPath();
      const guideY = canvas.offsetHeight * 0.7;
      const startXPercentage = 0.15;
      const endXPercentage = 0.85;
      const startX = canvas.offsetWidth * startXPercentage;
      const endX = canvas.offsetWidth * endXPercentage;
      context.moveTo(startX, guideY);
      context.lineTo(endX, guideY);
      context.strokeStyle = "black";
      context.lineWidth = 1;
      context.stroke();
    },
    cutOutSignature() {
      const canvas = document.querySelector("canvas");
      const context = canvas.getContext("2d");
      const signatureData = context.getImageData(
        0,
        0,
        canvas.width,
        canvas.height
      );

      let minX = canvas.width;
      let minY = canvas.height;
      let maxX = 0;
      let maxY = 0;

      for (let y = 0; y < canvas.height; y++) {
        for (let x = 0; x < canvas.width; x++) {
          const pixelIndex = (y * canvas.width + x) * 4;
          if (signatureData.data[pixelIndex + 3] > 0) {
            minX = Math.min(minX, x);
            minY = Math.min(minY, y);
            maxX = Math.max(maxX, x);
            maxY = Math.max(maxY, y);
          }
        }
      }
      const padding = 15;
      const drawnWidth = maxX - minX + 2 * padding;
      const drawnHeight = maxY - minY + 2 * padding;
      const croppedImageData = context.getImageData(
        minX - padding,
        minY - padding,
        drawnWidth,
        drawnHeight
      );

      const tempCanvas = document.createElement("canvas");
      tempCanvas.width = drawnWidth;
      tempCanvas.height = drawnHeight;
      const tempContext = tempCanvas.getContext("2d");
      tempContext.putImageData(croppedImageData, 0, 0);

      return tempCanvas.toDataURL("image/png");
    },
    onEnd() {
      this.$store.commit("setInputValue", {
        inputId: this.inputId,
        value: this.cutOutSignature(),
      });
      if (this.required) {
        this.$store.commit("setInputValidation", {
          inputId: this.inputId,
          valid: !this.$refs.signaturePad.saveSignature().isEmpty,
        });
      }
      this.signatureSrc = this.$refs.signaturePad.signaturePad.toDataURL();
    },
    clearSignature() {
      this.$refs.signaturePad.clearSignature();
      if (this.required) {
        this.$store.commit("setInputValidation", {
          inputId: this.inputId,
          valid: false,
        });
      }
      this.$store.commit("setInputValue", {
        inputId: this.inputId,
        value: null,
      });
      const canvas = document.querySelector("canvas");
      const context = canvas.getContext("2d");
      this.drawGuideLine(context, canvas);
      this.signatureSrc = this.$refs.signaturePad.signaturePad.toDataURL();
    },
    resizeCanvas() {
      const canvas = document.querySelector("canvas");
      const context = canvas.getContext("2d");

      const canvasRect = canvas.getBoundingClientRect();
      canvas.width = canvasRect.width;
      canvas.height = canvasRect.height;

      const signatureImage = new Image();
      signatureImage.src = this.signatureSrc;

      signatureImage.onload = () => {
        context.drawImage(
          signatureImage,
          0,
          0,
          canvas.offsetWidth,
          canvas.offsetHeight
        );
      };
    },
  },
  mounted() {
    if (this.required) {
      this.$store.commit("setInputValidation", {
        inputId: this.inputId,
        valid: !this.$refs.signaturePad.saveSignature().isEmpty,
      });
    }
    this.$store.commit("setInputValue", {
      inputId: this.inputId,
      value: null,
    });
    const canvas = document.querySelector("canvas");
    const context = canvas.getContext("2d");
    this.drawGuideLine(context, canvas);
    window.addEventListener("resize", this.resizeCanvas);
  },
  beforeDestroy() {
    window.removeEventListener("resize", this.resizeCanvas);
  },
};
</script>

<style lang="scss" scoped>
@import "~@/assets/css/skin.scss";

#signature {
  border: solid 3px $primary;
  border-radius: 5px;
  background-origin: border-box;
  background-clip: content-box, border-box;
  max-height: 8.5rem;
}

.container {
  display: flex;
  flex-direction: column;
  height: 10.5rem;
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

#button-container {
  display: flex;
  justify-content: flex-end;
  margin-top: 0.25rem;
}
</style>
