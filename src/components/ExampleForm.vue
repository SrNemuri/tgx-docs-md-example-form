<template>
<b-container class="bv-example-row">
  <link rel="stylesheet" href="https://unpkg.com/vue-multiselect@2.1.0/dist/vue-multiselect.min.css">
    <b-form @submit="onSubmit" @reset="onReset">
      <b-row>
          <b-col cols="3">
            <!-- description="We'll never share your email with anyone else." -->
            <b-form-group label="Title:">
              <b-form-input type="text"
                            v-model="form.title"
                            required
                            placeholder="Title">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="3">
            <b-form-group label="Page title:">
              <b-form-input type="text"
                            v-model="form.pagetitle"
                            required
                            placeholder="Enter page title">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="4">
            <b-form-group label="Description:">
               <b-form-input type="text"
                            v-model="form.description"
                            required
                            placeholder="Description">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="2">
            <b-form-group label="Weight:">
              <b-form-input type="text"
                            v-model="form.weight"
                            required
                            placeholder="Weight">
              </b-form-input>
            </b-form-group>
            </b-col>
      </b-row>
      <b-row>
          <b-col cols="4">
            <b-form-group label="Default API key:">
              <b-form-input type="text"
                            v-model="form.apiKey"
                            required
                            placeholder="Enter API key">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="4">
            <b-form-group label="Default user:">
              <b-form-row>
                 <b-col cols="8">
              <b-form-input type="text"
                            v-model="form.defaultUser"
                            required
                            placeholder="Enter default user">
              </b-form-input>
               </b-col>
                <b-col cols="4">
                   <b-button v-on:click="addUserGists" variant="info" :disabled="!form.defaultUser">Add user</b-button>
                </b-col>
              </b-form-row>
            </b-form-group>
          </b-col>
          <b-col cols="4">
            <b-form-group label="Add available options:">
              <b-form-row>
                <b-col cols="10">
                  <b-form-input type="text"
                                v-model="lastAdded"
                                required
                                placeholder="Enter available option">
                  </b-form-input>
                </b-col>
                <b-col cols="2">
                  <b-button v-on:click="onAddOption" variant="info">Add</b-button>
                </b-col>
              </b-form-row>
            </b-form-group>
          </b-col>
      </b-row>

      <div v-for="example in getPageExamples">
        <hr>
        <b-row >
          <b-col cols="5">
            <b-form-group label="Selected options:">
              <multiselect v-model="example.selectedOptions" :options="availableOptions" :multiple="true" :disabled="availableOptions.length === 0" :loading="loadingMultiselectOptions"></multiselect>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col cols="9">
            <b-form-group label="GitHub gist:">
              <b-row>
                <b-col cols="9">
                  <b-form-select v-model="example.gistURL" v-on:change="loadRaw(example)" :options="gists" class="mb-3"/>
                </b-col>
                <b-col cols="3">
                  <b-button :disabled="!example.gistRaw" v-on:click="showRaw(example)" variant="info">Show gist</b-button>
                </b-col>
              </b-row>
            </b-form-group>
          </b-col>
          
          <b-col cols="3">
            <b-form-group label="API key:">
              <b-form-input type="text"
                            v-model="example.apiKey"
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
          <b-button type="reset" variant="danger">Reset</b-button>
          <b-button type="submit" variant="primary" class="mr-2">Submit</b-button>
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
  <!-- Problema de renderizado al fetchear gist -->
  <!-- https://github.com/vuejs/vue-cli/commit/47fb3b8189ca76a3157eead7814cb7e11f1a4771 -->
  <b-modal ref="modalRawGist" title="Gist">
    <p class="gist-raw">{{modalRawGist.example.gistRaw}}</p>
  </b-modal>
  </b-container>  
</template>

<script>
export default {
  name: 'ExampleForm',
  data() {
    return {
      form: {
        title: '',
        pagetitle: '',
        description: '',
        icon: '',
        weight: '1',
        alwaysopen: false,
        defaultAK: '',
        defaultUser: '',
        examples: []
      },
      perPage: 2,
      currentPage: 1,
      lastAdded: '',
      availableOptions: [],
      value: [],
      loadingMultiselectOptions: false,
      gists: [],
      modalRawGist: {
        example: {}
      }
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
      this.form.defaultAK = '';
      this.form.defaultUser = '';
      this.availableOptions = [];
    },
    onAddOption: function() {
      if (!this.lastAdded) {
        return;
      }
      const self = this;

      this.loadingMultiselectOptions = true;

      this.availableOptions.push(this.lastAdded);

      this.lastAdded = '';

      this.loadingMultiselectOptions = false;
    },
    addUserGists: function() {
      const self = this;
      fetch('https://api.github.com/users/' + this.form.defaultUser + '/gists')
        .then(function(response) {
          return response.json();
        })
        .then(function(json) {
          if (json.length) {
            json.map(gist =>
              Object.keys(gist.files).map(key => {
                if (!self.gists.find(g => g.id === gist.id)) {
                  const raw_url = gist.files[key].raw_url.substring(
                    0,
                    gist.files[key].raw_url.indexOf('/raw/') + 4
                  );
                  console.log(raw_url);
                  self.gists.push({
                    id: gist.id,
                    text: `[${gist.owner.login}]: ${gist.files[key].filename}`,
                    value: raw_url
                  });
                }
              })
            );
          }
        });
    },
    addRow() {
      this.form.examples.push({
        gistURL: '',
        gistRaw: '',
        apiKey: '',
        description: '',
        availableOptions: [],
        selectedOptions: []
      });
    },
    loadRaw(example) {
      console.log(example);
      fetch(example.gistURL)
        .then(function(response) {
          return response.text();
        })
        .then(function(text) {
          console.log(text);
          example.gistRaw = text;
        });
    },
    showRaw(example) {
      this.modalRawGist.example = example;
      console.log(example);
      this.$refs.modalRawGist.show();
    },
    saveFile() {
      const data = JSON.stringify(this.arr);
      const blob = new Blob([data], { type: 'text/plain' });
      const e = document.createEvent('MouseEvents'),
        a = document.createElement('a');
      a.download = 'test.json';
      a.href = window.URL.createObjectURL(blob);
      a.dataset.downloadurl = ['text/json', a.download, a.href].join(':');
      e.initEvent(
        'click',
        true,
        false,
        window,
        0,
        0,
        0,
        0,
        0,
        false,
        false,
        false,
        false,
        0,
        null
      );
      a.dispatchEvent(e);
    }
  },
  computed: {
    getPageExamples() {
      const curr = this.currentPage - 1;
      const base = curr * this.perPage;
      return this.form.examples.slice(base, this.perPage + base);
    }
  }
};
</script>

<style>
  p.gist-raw {
        white-space: pre;
  }
</style>
