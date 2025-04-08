<template>
  <div id="app">
    <ViewerPage :dxf-url="dxfUrl">
      <div
        v-if="inputFile === null"
        class="centralUploader row justify-center items-center"
      >
        <div class="col-auto" style="width: 300px">
          <input type="file" accept=".dxf" @change="_OnFileSelected" ref="fileInput" />
          <div>
            <small>File is processed locally in your browser</small>
          </div>
        </div>
        <div class="col-auto" style="margin: 0 24px 24px">
          <button @click="urlDialog = true">Load URL</button>
        </div>
      </div>
    </ViewerPage>
  </div>
</template>

<script>
import ViewerPage from "./components/viewerPage.vue";
export default {
  name: "App",
  components: {
    ViewerPage,
  },
  data() {
    return {
      inputFile: null,
      dxfUrl: null,
      urlDialog: false,
    };
  },
  methods: {
    _OnFileSelected(event) {
      const file = event.target.files[0];
      if (file) {
        this.inputFile = file;
        this.dxfUrl = URL.createObjectURL(file);
      }
    },
    _OnFileCleared() {
      this.inputFile = null;
      this.dxfUrl = null;
      this.$refs.fileInput.value = null;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
