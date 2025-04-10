<template>
  <div id="app">
    <ViewerPage :dxf-url="dxfUrl" />
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
      dxfUrl: null,
    };
  },
  methods: {
    onMessageFromParent(event) {
      if (event.origin === process.env.VUE_APP_URL_RECIEVER)
        this.dxfUrl = event.data.arquivo_link;
    },
    // async getPreSignedLink({ arquivo_link, token }) {
    //   try {
    //     const req = await fetch(
    //       `https://tanodocs-api.juejkf.easypanel.host/arquivo/generate-s3-get-link`,
    //       {
    //         method: "POST",
    //         headers: {
    //           "Content-Type": "application/json",
    //           Authorization: `Bearer ${token}`,
    //         },
    //         body: JSON.stringify({
    //           arquivo_link: arquivo_link,
    //           arquivo_descricao: `file.dxf`,
    //         }),
    //       }
    //     );

    //     const res = await req.json();

    //     console.log(res);
    //   } catch (error) {
    //     console.error("Err:", error);
    //   }
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
