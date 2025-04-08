<template>
  <div class="root">
    <div class="main-content">
      <slot></slot>

      <DfxViewer
        ref="viewer"
        :dxfUrl="dxfUrl"
        :fonts="fonts"
        @dxf-loaded="onLoaded"
        @dxf-cleared="onCleared"
        @dxf-message="onMessage"
      />
    </div>

    <div class="layersCol">
      <LayerList
        :layers="layers"
        @toggleLayer="onToggleLayer"
        @toggleAll="_OnToggleAll"
      />
    </div>
  </div>
</template>

<script>
import Vue from "vue";

import DfxViewer from "./dfxViewer.vue";
import { DxfViewer as _DxfViewer } from "dxf-viewer";
import LayerList from "./layerList.vue";

import mainFont from "../assets/fonts/Roboto-LightItalic.ttf";
import aux1Font from "../assets/fonts/NotoSansDisplay-SemiCondensedLightItalic.ttf";
import aux2Font from "../assets/fonts/HanaMinA.ttf";
import aux3Font from "..//assets/fonts/NanumGothic-Regular.ttf";

export default {
  name: "ViewerPage",
  components: { LayerList, DfxViewer },

  props: {
    dxfUrl: {
      type: String,
    },
  },

  data() {
    return {
      layers: null,
      fonts: [],
    };
  },

  methods: {
    onLoaded() {
      const layers = this.$refs.viewer.GetViewer().GetLayers(true);
      layers.forEach((layer) => Vue.set(layer, "isVisible", true));
      this.layers = layers;
    },

    onCleared() {
      this.layers = null;
    },

    onToggleLayer(layer, newState) {
      layer.isVisible = newState;
      this.$refs.viewer.GetViewer().ShowLayer(layer.name, newState);
    },

    _OnToggleAll(newState) {
      if (this.layers) {
        for (const layer of this.layers) {
          if (layer.isVisible !== newState) {
            this.onToggleLayer(layer, newState);
          }
        }
      }
    },

    onMessage(e) {
      let type = "info";
      switch (e.detail.level) {
        case _DxfViewer.MessageLevel.WARN:
          type = "warning";
          break;
        case _DxfViewer.MessageLevel.ERROR:
          type = "error";
          break;
      }
      alert(`[${type.toUpperCase()}] ${e.detail.message}`);
    },
  },

  created() {
    this.fonts = [mainFont, aux1Font, aux2Font, aux3Font];
    // this.fonts = [];
  },
};
</script>

<style scoped>
.root {
  display: flex;
  height: 100vh;
  overflow: hidden;
}

.main-content {
  flex: 1;
  position: relative;
}

.layersCol {
  width: 300px;
  border-left: 1px solid #dbdbdb;
  overflow-y: auto;
}
</style>
