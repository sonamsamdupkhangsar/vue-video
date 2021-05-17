<template>
  <v-container>
    <div>
         <v-file-input
        accept="image/*"
        label="File input"
        v-model="chosenFile"
      ></v-file-input>
      <v-btn
        :loading="loading"
        :disabled="loading"
        color="blue-grey"
        class="ma-2 white--text"
        @click="loader = 'loading'"
        v-on:click="postVideo()"
      >
        Upload
        <v-icon right dark>
          mdi-cloud-upload
        </v-icon>
      </v-btn>
      <v-snackbar v-model="snackbar" :multi-line="multiLine" :timeout="timeout">
        {{ text }}

        <template v-slot:action="{ attrs }">
          <v-btn color="red" text v-bind="attrs" @click="snackbar = false">
            Close
          </v-btn>
        </template>
      </v-snackbar>
    </div>
    <div class="hello">
      <h1>{{ msg }}</h1>
      <v-card class="mx-auto" max-width="344" outlined v-for="f in files" v-bind:key="f.id">
        <v-list-item three-line>
          <v-list-item-content>
            <div class="overline mb-4">
              {{f.id}}
            </div>
            <v-list-item-title class="headline mb-1">
             <v-img  
             max-height="150"
            max-width="250"
             :src="'http://localhost:9001/videofiles/'+f.path"/>
             {{f.path}}
            </v-list-item-title>
            <v-list-item-subtitle>{{f.stored}}</v-list-item-subtitle>
              
          </v-list-item-content>

          <v-list-item-avatar tile size="80" color="grey"></v-list-item-avatar>
        </v-list-item>

        <v-card-actions>
          <v-btn outlined rounded text>
            Button
          </v-btn>
        </v-card-actions>
      </v-card>

   
    </div>
  </v-container>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  props: {
    msg: String,
    baseUrl: String,
  },
  data() {
    return {
      loading: false,
      chosenFile: null,
      fileMessage: "",
      multiLine: true,
      snackbar: false,
      timeout: 1000,
      text: `I'm a multi-line snackbar.`,
      files: [],
    };
  },
  methods: {
    getVideos() {
      this.loading = true;

      try {
        axios.get(this.baseUrl).then((response) => {
          console.info("got response, ", response);
          this.loading = false;
          this.files = response.data;
        });
      } catch (e) {
        console.error(`Errors! ${e}`);
      }
    },

    postVideo() {
      if (!this.chosenFile) {
        this.text = "No File Chosen";
        this.snackbar = true;
      }
      console.log("chosen file: ", this.chosenFile);
      try {
        this.submitting = true;
        let formData = new FormData();

        formData.append("file", this.chosenFile);

        axios
          .post(this.baseUrl + "/upload", formData, {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          })
          .then((response) => {
            if (response.status == 401) {
              console.log("request user to login");
              this.$emit("login");
            }
            console.log("response: ", response);
          });
      } catch (e) {
        console.error(` failed to get services by applicationid, Errors! ${e}`);
      }
    },
  },

  async created() {
    this.getVideos();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
