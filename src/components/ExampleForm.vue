<template>
<b-container class="bv-example-row">
  <link rel="stylesheet" href="https://unpkg.com/vue-multiselect@2.1.0/dist/vue-multiselect.min.css">
    <b-form @submit="onSubmit" @reset="onReset">
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
                                v-model="lastAdded"
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

      <div v-for="example in form.examples.slice(currentPage -1, perPage)">
        <hr>
        <b-row >
          <b-col cols="5">
            <b-form-group label="Selected options:">
              <multiselect v-model="example.selected_options" :options="available_options" :multiple="true" :disabled="available_options.length === 0" :loading="loadingMultiselect"></multiselect>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col cols="3">
            <b-form-group label="Name:">
              <b-form-input type="text"
                          v-model="example.name"
                          required
                          placeholder="Example name">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="3">
            <b-form-group label="User:">
              <b-form-input type="text"
                            v-model="example.user"
                            placeholder="GitHub user">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="3">
            <b-form-group label="Gist:">
              <b-form-input type="text"
                            v-model="example.gist"
                            required
                            placeholder="Github gist id">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="3">
            <b-form-group label="API key:">
              <b-form-input type="text"
                            v-model="example.api_key"
                            placeholder="API key">
              </b-form-input>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col>
            <b-form-group label="Description:">
              <b-form-textarea v-model="example.description"
                            placeholder="Description"
                            :rows="3"
                            :max-rows="6">
              </b-form-textarea>
            </b-form-group>
          </b-col>
        </b-row>
      </div>

      <b-row>
        <b-col cols="2">
          <b-button v-on:click="addRow" variant="success">Add row</b-button>
        </b-col>
      </b-row>

      <br>
      
      <b-row>
        <b-col cols="3" offset="10">
          <b-button type="submit" variant="primary" class="mr-2">Submit</b-button>
          <b-button type="reset" variant="danger">Reset</b-button>
        </b-col>
      </b-row>
    </b-form>

<!-- <b-table :items="form.examples" :current-page="currentPage" :per-page="perPage">
      <template scope="item">
      <div>{{item.name}}</div>
    </template>
    
  </b-table> -->

  <div>
    <b-pagination size="md" :total-rows="form.examples.length" :per-page="perPage" v-model="currentPage" />
  </div>
  </b-container>  
</template>

<script>




  

export default {
  name: 'ExampleForm',
  data() {
    return {
      form: {
        default_ak: '',
        default_user: '',
        examples: []
      },
      perPage: 2,
      currentPage: 1,
      lastAdded: '',
      available_options: [],
      value: [],
      loadingMultiselect: false
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
    },
    onAddOption: function() {
      if (!this.lastAdded) {
        return;
      }
      const self = this;

      this.loadingMultiselect = true;

      this.available_options.push(this.lastAdded);

      this.lastAdded = '';

      this.loadingMultiselect = false;
      console.log(this.available_options);
    },
    addRow() {      
        this.form.examples.push({
          name: '',
          user: '',
          gist: '',
          api_key: '',
          description: '',
          available_options: [],
          selected_options: []

        });
      
      console.log(this.form.examples);
    }
  }
};
</script>