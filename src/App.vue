<template>
  <div id="app">
    <ViewerPage :dxf-url="dxfUrl">
      oi {{ currentPath }}
      <div
        v-if="inputFile === null"
        class="centralUploader row justify-center items-center"
      >
        <div class="col-auto" style="width: 300px">
          <input type="file" accept=".dxf" @change="onFileSelected" ref="fileInput" />
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
  mounted() {
    window.addEventListener("message", this.onMessageFromParent);
  },

  beforeDestroy() {
    window.removeEventListener("message", this.onMessageFromParent);
  },
  data() {
    return {
      inputFile: null,
      dxfUrl: null,
      urlDialog: false,
      currentPath: "",
    };
  },
  methods: {
    onMessageFromParent(event) {
      if (event.origin === "http://localhost:3000") {
        this.currentPath = event.data.href;
      }
    },
    onFileSelected(event) {
      const file = event.target.files[0];
      if (file) {
        this.inputFile = file;
        this.dxfUrl = URL.createObjectURL(file);
      }
    },
    onFileCleared() {
      this.inputFile = null;
      this.dxfUrl = null;
      this.$refs.fileInput.value = null;
    },
  },
  computed: {
    // currentPath() {
    //   // return window?.parent.location;
    //   console.log(document.referrer, '1')
    //   console.log(document.location, '2')
    //   return window.location != window.parent.location ? document.referrer : document.location.href
    // },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
</style>
