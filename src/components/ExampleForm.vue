<template>
<b-container class="bv-example-row">
    <b-form @submit="onSubmit" @reset="onReset" v-if="show">
    <b-row>
        <b-col cols="4">
          <!-- description="We'll never share your email with anyone else." -->
          <b-form-group label="Default API key:">
            <b-form-input type="text"
                          v-model="form.apiKey"
                          required
                          placeholder="Enter API key">
            </b-form-input>
          </b-form-group>
        </b-col>
        <b-col cols="3">
          <b-form-group label="Default user:">
            <b-form-input type="text"
                          v-model="form.defaultUser"
                          required
                          placeholder="Enter default user">
            </b-form-input>
          </b-form-group>
        </b-col>
        <b-col cols="5">
          <b-form-group label="Add available options:">
            <b-form-row>
              <b-col cols="10">
                <b-form-input type="text"
                              v-model="form.defaultUser"
                              required
                              placeholder="Enter available option">
                </b-form-input>
              </b-col>
              <b-col  cols="2">
                <b-button v-on:click="onAddOption" variant="info">Add</b-button>
              </b-col>
            </b-form-row>
          </b-form-group>
        </b-col>
    </b-row>

    <!-- <b-row>
      <b-col>
        <b-form-group label="Default user:">
            <b-form-input type="text"
                          v-model="form.defaultUser"
                          required
                          placeholder="Enter default user">
            </b-form-input>
          </b-form-group>
      </b-col>
    </b-row> -->
      
      
      <b-form-group label="Food:">
        <b-form-select :options="foods"
                      required
                      v-model="form.food">
        </b-form-select>
      </b-form-group>
       <b-form-group label="Choose options:">
        <b-form-select :options="options"
                      required
                      v-model="form.options">
        </b-form-select>
      </b-form-group>
      <b-form-group id="exampleGroup4">
        <b-form-checkbox-group v-model="form.checked" id="exampleChecks">
          <b-form-checkbox value="me">Check me out</b-form-checkbox>
          <b-form-checkbox value="that">Check that out</b-form-checkbox>
        </b-form-checkbox-group>
      </b-form-group>
      <b-button type="submit" variant="primary">Submit</b-button>
      <b-button type="reset" variant="danger">Reset</b-button>
    </b-form>
      </b-container>
</template>

<script>
// import { validationMixin } from 'vuelidate';
// import {
//   required,
//   email,
//   minLength,
//   maxLength
// } from 'vuelidate/lib/validators';

export default {
  name: 'ExampleForm',
  data() {
    return {
      form: {
        email: '',
        name: '',
        default_ak: '',
        default_user: '',
        available_options: [],
        food: null,
        checked: []
      },
      foods: [
        { text: 'Select One', value: null },
        'Carrots',
        'Beans',
        'Tomatoes',
        'Corn'
      ],
      default_options: {},
      show: true
    };
  },
  methods: {
    onSubmit(evt) {
      evt.preventDefault();
      alert(JSON.stringify(this.form));
    },
    onReset(evt) {
      evt.preventDefault();
      /* Reset our form values */
      this.form.email = '';
      this.form.name = '';
      this.form.default_ak = '';
      this.form.default_user = '';
      this.available_options = [];
      this.form.food = null;
      this.form.checked = [];
      /* Trick to reset/clear native browser form validation state */
      this.show = false;
      this.$nextTick(() => {
        this.show = true;
      });
    },
    onAddOption: function() {
      console.log('adding');
    }
  }
};
</script>