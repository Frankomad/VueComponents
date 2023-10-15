<template>
  <div class="DateTimePicker content component">
    <vue-ctk-date-time-picker
      v-model="selectedDate"
      :name="'Date'"
      :format="format"
      :formatted="formatted"
      :only-date="false"
      :data-vv-as="'date'"
      :first-day-of-week="1"
      :range="false"
      :only-time="false"
      :minute-interval="minuteInterval ? minuteInterval : 1"
      :no-button-now="true"
      :label="label ? label : 'Select date & time'"
      :disabled-hours="disabledHours ? disabledHours : []"
      :no-weekends-days="noWeekendDays ? noWeekendDays : false"
      :locale="locale ? locale : 'en-US'"
    ></vue-ctk-date-time-picker>
  </div>
</template>

<script>
import "vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css";
import moment from "moment";
import VueCtkDateTimePicker from "vue-ctk-date-time-picker";

export default {
  name: "DateTimePicker",
  components: {
    VueCtkDateTimePicker,
  },
  props: {
    inputId: String,
    minuteInterval: {
      type: Number,
      default: 5,
    },
    label: {
      type: String,
      default: "Select date & time",
    },
    noWeekendDays: {
      type: Boolean,
      default: false,
    },
    disabledHours: {
      type: Array,
      default: () => [],
    },
    required: {
      type: Boolean,
      default: false,
    },
    value: {
      type: String,
      default: null,
    },
    locale: {
      type: String,
      default: "en-US",
    },
    hour12: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      format: this.hour12 ? "YYYY-MM-DDTHH:mm a" : "YYYY-MM-DDTHH:mm",
      formatted: this.hour12 ? "DD MMM, YYYY hh:mm a" : "DD MMM, YYYY HH:mm",
      selectedDate: this.value
        ? moment(this.value, this.format).format(this.format)
        : null,
    };
  },
  computed: {
    formattedDate() {
      return moment(this.selectedDate, this.format).format(this.format);
    },
  },
  mounted() {
    if (this.hour12 && this.value)
      document.querySelector(".flex-fixed").textContent =
        this.adjustTimeTo12HourFormat(this.selectedDate).split("T")[1];
    this.$store.commit("setInputValue", {
      inputId: this.inputId,
      value: {
        value: this.value
          ? moment(this.value, this.format).format("YYYY-MM-DDTHH:mm")
          : null,
      },
    });
    if (this.required) {
      this.$store.commit("setInputValidation", {
        inputId: this.inputId,
        valid: this.value !== null,
      });
    }
  },
  watch: {
    formattedDate(newValue) {
      if (this.hour12)
        document.querySelector(".flex-fixed").textContent =
          this.adjustTimeTo12HourFormat(newValue).split("T")[1];
      this.$store.commit("setInputValue", {
        inputId: this.inputId,
        value: {
          value: moment(newValue, this.format).format("YYYY-MM-DDTHH:mm"),
        },
      });
      if (this.required)
        this.$store.commit("setInputValidation", {
          inputId: this.inputId,
          valid: newValue !== "Invalid date",
        });
    },
  },
  methods: {
    adjustTimeTo12HourFormat(dateTimeStr) {
      const dateTime = moment(dateTimeStr, this.format);
      let meridiem = dateTime.format("a") === "pm" ? " pm" : " am";
      if (meridiem === " pm" && dateTime.format("HH") !== "12")
        dateTime.subtract(12, "hours");
      else if (meridiem === " am" && dateTime.format("HH") === "00")
        dateTime.add(12, "hours");
      return dateTime.format("YYYY-MM-DDTHH:mm").concat(meridiem);
    },
  },
};
</script>
<style lang="scss" scoped>
@import "~@/assets/css/skin.scss";

::v-deep .datepicker-day-effect {
  background-color: $primary !important;
  box-shadow: unset !important;
}

::v-deep .datepicker-day {
  box-shadow: unset !important;
}

::v-deep .datepicker-button {
  box-shadow: unset !important;
}

::v-deep .header-picker {
  background-color: $primary !important;
}

::v-deep .header-picker-time {
  padding-left: 0 !important;
}

::v-deep .custom-button {
  box-shadow: unset !important;
}

::v-deep .custom-button-content {
  color: $primary !important;
  box-shadow: unset !important;
}

::v-deep .time-picker:before {
  top: 49.7% !important;
}

::v-deep .time-picker-column-item-effect {
  background-color: $primary !important;
}

::v-deep .time-picker-column-item {
  box-shadow: unset !important;
}

::v-deep .header-picker-date {
  width: 5.5rem !important;
  flex: unset !important;
}

::v-deep .justify-content-between {
  justify-content: left !important;
}

::v-deep .h-100.justify-content-right .justify-content-center {
  justify-content: right !important;
}

::v-deep .h-100 .justify-content-center {
  justify-content: left !important;
}

::v-deep #undefined-wrapper {
  margin-top: unset !important;
}

::v-deep .time-picker-column-item:not(.active) .time-picker-column-item-effect {
  background-color: transparent !important;
}

::v-deep .time-picker-column-item:not(.active) .time-picker-column-item-text {
  color: #252525 !important;
}

::v-deep .datepicker-buttons-container {
  background-color: $primary !important;
}
</style>
