<template>
  <default-field :field="field">
    <template slot="field">
      <div class="grid grid-cols-6">
        <div :class="checkboxClassObject">
          <input
            id="alt-address"
            type="checkbox"
            class="checkbox"
            v-model="checked"
          />
        </div>

        <div :class="classObject">
          <vue-google-autocomplete
            ref="address"
            :id="field.name"
            class="w-full form-control form-input form-input-bordered"
            :class="errorClasses"
            :placeholder="placeholder"
            :country="field.countries"
            v-on:placechanged="getAddressData"
          >
          </vue-google-autocomplete>
        </div>
      </div>

      <p v-if="hasError" class="my-2 text-danger">
        {{ firstError }}
      </p>
    </template>
  </default-field>
</template>

<script>
import { FormField, HandlesValidationErrors } from "laravel-nova";
import VueGoogleAutocomplete from "vue-google-autocomplete";

export default {
  components: { VueGoogleAutocomplete },

  mixins: [FormField, HandlesValidationErrors],

  props: ["resourceName", "resourceId", "field"],

  created() {
    // console.log(this.field.name === "Address");
    // console.log("value: ", this.field.value, "checked: ", this.checked);
    if (this.field.value) {
      this.checked = true;
    }
  },

  data: function() {
    return {
      address: "",
      checked: false,
    };
  },

  computed: {
    placeholder() {
      return this.field.value ? this.field.value : this.field.name;
    },

    classObject() {
      return {
        "form-group ": true,
        // For Customer Form
        "col-span-6": this.field.name === "Address",
        visible: this.checked || this.field.name === "Address",
        // For Order Form
        "col-span-5 col-start-2": this.field.name !== "Address",
        invisible: !this.checked && this.field.name !== "Address",
      };
    },

    checkboxClassObject() {
      return {
        hidden: this.field.name === "Address",
        "col-span-1 flex justify-center items-center": true,
      };
    },
  },

  methods: {
    getAddressData: function(addressData, placeResultData, id) {
      this.handleChange(placeResultData.formatted_address);
    },
    /*
     * Set the initial, internal value for the field.
     */
    setInitialValue() {
      this.value = this.field.value || "";
    },

    /**
     * Fill the given FormData object with the field's internal value.
     */
    fill(formData) {
      if (this.checked || this.field.name === "Address") {
        formData.append(this.field.attribute, this.value || "");
      } else {
        formData.append(this.field.attribute, null);
      }
    },

    /**
     * Update the field's internal value.
     */
    handleChange(value) {
      this.value = value;
    },
  },
};
</script>
