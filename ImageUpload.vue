<template>
  <div class="component content ImageUpload">
    <div class="file is-boxed">
      <label class="file-label">
        <image-uploader
          :quality="0.9"
          :autoRotate="false"
          outputFormat="verbose"
          :preview="false"
          :className="['fileinput', { 'fileinput--loaded': image !== '' }]"
          :capture="false"
          accept="image/png,image/jpeg"
          @input="processImage"
        ></image-uploader>
        <span class="file-cta">
          <span class="is-large">
            <FontAwesomeIcon
              id="check-icon"
              v-if="imageName"
              icon="check-circle"
            />
            <FontAwesomeIcon id="arrow-icon" v-else icon="cloud-upload-alt" />
          </span>
          <span
            class="file-label"
            v-if="(!image && !value) || (image === value && value)"
            v-html="uploadText"
          />
          <span class="file-label" v-else v-html="uploadedText" />
          <span class="file-name" v-if="imageName !== null">
            {{ imageName }}
          </span>
        </span>
        <div v-if="image" class="img-wrapper">
          <img :src="image" />
        </div>
      </label>
    </div>
  </div>
</template>

<script>
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import ImageUploader from "vue-image-upload-resize";
import BaseValue from "armory-sdk/src/components/base/BaseValue.vue";

export default {
  name: "ImageUpload",
  extends: BaseValue,
  components: {
    ImageUploader,
    FontAwesomeIcon,
  },
  props: {
    uploadText: String,
    uploadedText: String,
    value: {
      type: String,
      default: null,
    },
  },
  mounted: function () {
    if (this.required) {
      this.$store.commit("setInputValidation", {
        inputId: this.inputId,
        valid: this.image !== null,
      });
    }
    if (this.value != null && this.image == null) this.image = this.value;
  },
  computed: {
    imageName: {
      get() {
        let value = null;
        if (this.$store.state.inputs["tmp"]) {
          value = this.$store.state.inputs["tmp"].value;
          if (value.length > 20) {
            return value.substring(0, 17) + "...";
          }
          return value;
        }
        return null;
      },
      set(value) {
        this.$store.commit("setInputValue", {
          inputId: "tmp",
          value: {
            value: value,
          },
        });
      },
    },
    image: {
      get() {
        let value = null;
        if (this.$store.state.inputs[this.inputId])
          value = this.$store.state.inputs[this.inputId].value;
        return value;
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
          valid: this.image !== null,
        });
      },
    },
  },
  methods: {
    processImage(data) {
      this.image = data.dataUrl;
      this.imageName = data.info.name;
      this.$store.commit("setInputValidation", {
        inputId: this.inputId,
        valid: this.image !== null,
      });
    },
  },
};
</script>

<style scoped>
.file,
.file-name {
  justify-content: center;
  width: 100%;
  text-align: center;
}

.file {
  display: unset;
}

.file-name {
  max-width: unset;
  height: auto;
  text-align: center;
  border: none;
}

#arrow-icon {
  color: #000;
}

#check-icon {
  color: #9dea3a;
}

.file-cta {
  background: #dfdfdf;
  padding: 1em 1.5em !important;
  border-radius: 5px 5px 0 0;
}

.img-wrapper {
  display: flex;
  justify-content: center;
  border: 1px solid #d5d5d5;
  border-radius: 0 0 5px 5px;
  padding: 15px;
}

img {
  max-height: 16em;
  max-width: 10em;
  object-fit: contain;
}

>>> .fileinput {
  display: none;
}
</style>
