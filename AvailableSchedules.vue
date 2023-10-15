<template>
  <div class="AvailableSchedule content component">
    <div
      v-for="(options, dateStr) in groupedOptions"
      id="schedules-container"
      :key="dateStr.toString()"
    >
      <div id="schedule-date">
        {{ dateStr }}
      </div>
      <div class="date-options">
        <button
          :class="[checked(option) ? 'button active' : 'button']"
          v-for="option in options"
          :key="option.toString()"
          @click="chooseTime(option)"
        >
          <span>
            {{ formatTime(option) }}
          </span>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AvailableSchedule",
  props: {
    inputId: String,
    options: Array,
    timeZone: {
      type: String,
      default: "Europe/Zagreb",
    },
    required: {
      type: Boolean,
      default: false,
    },
    locale: {
      type: String,
      default: "en-US",
    },
  },
  mounted() {
    if (this.required)
      this.$store.commit("setInputValidation", {
        inputId: this.inputId,
        valid: this.$store.state.inputs[this.inputId] !== undefined,
      });
  },
  methods: {
    formatTime(time) {
      const formattedTime = time.toLocaleTimeString(
        this.locale ? this.locale : "en",
        {
          hour: "2-digit",
          minute: "2-digit",
          timeZone: this.timeZone,
        }
      );

      // Manually adjust hour if it's 24 because it's not supported by toLocaleTimeString
      const parts = formattedTime.split(":");
      let hour = parts[0];
      if (hour === "24") {
        hour = "00";
      }
      const minute = parts[1];

      return `${hour}:${minute}`;
    },
    formatDateTime(option) {
      return option.toISOString().substring(0, 16);
    },
    chooseTime(option) {
      const optionStr = this.formatDateTime(option);
      const value =
        this.$store.state.inputs[this.inputId] === optionStr ? null : optionStr;
      this.$store.commit("setInputValue", {
        inputId: this.inputId,
        value: value,
      });
      if (this.required) {
        this.$store.commit("setInputValidation", {
          inputId: this.inputId,
          valid: this.$store.state.inputs[this.inputId] !== null,
        });
      }
    },
    checked(option) {
      return (
        this.$store.state.inputs[this.inputId] !== undefined &&
        this.$store.state.inputs[this.inputId] === this.formatDateTime(option)
      );
    },
  },
  computed: {
    groupedOptions: function () {
      let grouped = {};
      const optionsArray = this.options;
      for (let index = 0; index < optionsArray.length; index++) {
        const dateObj = new Date(optionsArray[index]);
        const formattedDateStr = dateObj.toLocaleString(
          this.locale ? this.locale : "en",
          {
            day: "numeric",
            month: "short",
            year: "numeric",
            weekday: "long",
            timeZone: this.timeZone,
          }
        );
        if (grouped[formattedDateStr] === undefined)
          grouped[formattedDateStr] = [];
        grouped[formattedDateStr].push(dateObj);
      }
      return grouped;
    },
  },
};
</script>

<style lang="scss" scoped>
@import "~@/assets/css/skin.scss";

div.date-options {
  display: flex;
  flex-wrap: wrap;
  gap: 4%;
}

#schedule-date {
  padding-bottom: 0.6rem;
  padding-top: 1.3rem;
  color: $primary;
  font-size: 1.2rem;
}

button {
  margin-top: 8px;
  padding-right: 0.15rem;
  padding-left: 0.15rem;
  width: 30.65%;
  font-size: 1rem;
}

button.active {
  background-color: $primary-light;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}
</style>
