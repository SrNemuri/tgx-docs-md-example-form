<template>
<b-container class="bv-example-row">
  <link rel="stylesheet" href="https://unpkg.com/vue-multiselect@2.1.0/dist/vue-multiselect.min.css">
    <b-form @submit="onSubmit" @reset="onReset">
      <b-row>
          <b-col cols="3">
            <!-- description="We'll never share your email with anyone else." -->
            <b-form-group label="Title:">
              <b-form-input type="text"
                            v-on:keyup.enter.native="$refs.pageTitle.$el.focus()"
                            v-model="form.title"
                            required
                            placeholder="Title">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="3">
            <b-form-group label="Page title:">
              <b-form-input type="text"
                            ref="pageTitle"
                            v-on:keyup.enter.native="$refs.description.$el.focus()"
                            v-model="form.pagetitle"
                            required
                            placeholder="Enter page title">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="4">
            <b-form-group label="Page description:">
               <b-form-input type="text"
                            ref="description"
                            v-on:keyup.enter.native="$refs.weight.$el.focus()"
                            v-model="form.description"
                            required
                            placeholder="Description">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="2">
            <b-form-group label="Weight:">
              <b-form-input type="text"
                            ref="weight"
                            v-on:keyup.enter.native="$refs.defaultApi.$el.focus()"
                            v-model="form.weight"
                            required
                            placeholder="Weight">
              </b-form-input>
            </b-form-group>
            </b-col>
      </b-row>
      <b-row>
          <b-col cols="6">
            <b-form-group label="Default API key:">
              <b-form-input type="text"
                            ref="defaultApi"
                            v-model="form.apiKey"
                            v-on:keyup.enter.native="$refs.defaultUser.$el.focus()"
                            required
                            placeholder="Enter API key">
              </b-form-input>
            </b-form-group>
          </b-col>
          <b-col cols="6">
            <b-form-group label="Default user:">
              <b-form-input type="text"
                            ref="defaultUser"
                            v-model="defaultUser"
                            v-on:keyup.enter.native="addUserGists"
                            required
                            placeholder="Enter default user">
              </b-form-input>
              <span>{{getSelectedUsers}}</span>
            </b-form-group>
          </b-col>
      </b-row>

      <div v-for="(example, index) in getPageExamples" :key="index">
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
                <b-col cols="8">
                  <b-form-select v-model="example.gist" :options="gists" class="mb-3"/>
                </b-col>
                <b-col cols="2">
                  <b-button :disabled="!example.gist" v-on:click="loadRaw(example)" variant="info">Load gist</b-button>
                </b-col>
                <b-col cols="2">
                  <b-button :disabled="!example.gistFiles || example.gistFiles.length === 0" v-on:click="showRaw(example.gistFiles)" variant="info">Show gist</b-button>
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
          <b-button type="button" variant="primary" class="mr-2">Submit</b-button>
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

  <!-- MODAL -->

  <b-modal ref="modalRawGist" title="Gist" size="lg">
    <div class="gist-raw">
      <b-tabs>
        <template v-for="(file, index) in modalRawGist.files">
          <b-tab :title="`${file.label}`" :key="index" >
            <code v-text="file.value"></code>
          </b-tab>
        </template>
      </b-tabs>
    </div>
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
        examples: []
      },
      modalIndex: 0,
      defaultUser: '',
      loadingMultiselectOptions: false,
      perPage: 2,
      currentPage: 1,
      lastAdded: '',
      availableOptions: [],
      value: [],
      gists: [],
      modalRawGist: {
        files: []
      },
      selectedUsers: []
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

      this.loadingMultiselectOptions = true;

      this.availableOptions.push(this.lastAdded);

      this.lastAdded = '';

      this.loadingMultiselectOptions = false;
    },
    addUserGists: function() {
      const self = this;
      fetch('https://api.github.com/users/' + this.defaultUser + '/gists')
        .then(function(response) {
          return response.json();
        })
        .then(function(json) {
          if (json.length) {
            json.map(gist => {
              if (!self.gists.find(g => g.id === gist.id)) {
                self.gists.push({
                  id: gist.id,
                  text: `[${gist.owner.login}]: ${gist.description}`,
                  value: gist.files
                });
              }
            });
            if (self.defaultUser) {
              self.selectedUsers.push(self.defaultUser);
              self.defaultUser = '';
            }
          }
        });
    },
    addRow() {
      this.form.examples.push({
        gist: '',
        gistFiles: [],
        apiKey: '',
        description: '',
        availableOptions: [],
        selectedOptions: []
      });
    },
    loadRaw(example) {
      const promises = [];
      const types = [];
      example.gistFiles = [];
      for (const key in example.gist) {
        const request = fetch(example.gist[key].raw_url).then(function(
          response
        ) {
          return response.text();
        });
        const type = example.gist[key].filename.slice(
          example.gist[key].filename.lastIndexOf('.') + 1
        );
        types.push(type);
        promises.push(request);
      }

      Promise.all(promises).then(responses => {
        responses.forEach((response, index) => {
          example.gistFiles.push({
            label: types[index].toUpperCase(),
            value: response.trim()
          });
        });
      });
    },
    showRaw(files) {
      this.modalRawGist.files = files;
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
    },
    getSelectedUsers() {
      if ((this.selectedUsers || []).length) {
        return this.selectedUsers.join(', ');
      }
      return '';
    }
  }
};
</script>

<style>
div.gist-raw div.tab-content {
  padding: .5rem 0 .5rem 1rem;
  background-color: #f7f8f9 !important;
}
div.gist-raw code {
  white-space: pre;
  color: #22a459 !important;
  
}
</style>

