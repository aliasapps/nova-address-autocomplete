<template>
  <default-field :field="field">
    <template slot="field">
      <div class="grid grid-cols-6">
        <div class="col-span-1 flex justify-center items-center">
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
            :placeholder="field.name"
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
    // Nova.$on("has_alternate_address-change", this.handleListener);
  },

  data: function() {
    return {
      address: "",
      //   showAutocomplete: false,
      checked: false,
    };
  },

  computed: {
    classObject() {
      return {
        "form-group col-span-5 col-start-2": true,

        invisible: !this.checked,
        visible: this.checked,
      };
    },
  },

  methods: {
    // handleChange() {
    //   this.showAutocomplete = this.checked;
    //   console.log("showAutcomplete: ", this.showAutocomplete);
    //   //   Nova.$emit(`${this.field.attribute}-change`, this.value);
    // },

    // handleListener(booleanValue) {
    //   this.showAutocomplete = booleanValue;
    // },

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
      formData.append(this.field.attribute, this.value || "");
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
