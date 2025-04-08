<template>
  <div class="canvasContainer" ref="canvasContainer">
    <div v-if="isLoading" class="loading">Carregando...</div>

    <div v-if="progress !== null" class="progress">
      <div class="progressBarContainer">
        <div
          class="progressBar"
          :style="{ width: progress < 0 ? '100%' : progress * 100 + '%' }"
        ></div>
      </div>
      <div v-if="progressText" class="progressText">{{ progressText }}</div>
    </div>

    <div v-if="error !== null" class="error" :title="error">⚠️ Erro: {{ error }}</div>
  </div>
</template>

<script>
import { DxfViewer } from "dxf-viewer";
import * as THREE from "three";
import DxfViewerWorker from "worker-loader!./dfxWorker"

// const DxfViewerWorker = () =>
//   new Worker(new URL("./dfxWorker.js", import.meta.url), { type: "module" });

export default {
  name: "DxfViewer",

  props: {
    dxfUrl: {
      type: String,
      default: null,
    },
    fonts: {
      type: Array,
      default: () => null,
    },
    options: {
      type: Object,
      default: () => ({
        clearColor: new THREE.Color("#ffffff"),
        autoResize: true,
        colorCorrection: true,
        sceneOptions: {
          wireframeMesh: true,
        },
      }),
    },
  },

  data() {
    return {
      dxfViewer: null,
      isLoading: false,
      progress: null,
      progressText: null,
      curProgressPhase: null,
      error: null,
    };
  },

  watch: {
    async dxfUrl(newUrl) {
      if (newUrl) {
        await this.loadDxf(newUrl);
      } else {
        this.dxfViewer?.Clear();
        this.error = null;
        this.isLoading = false;
        this.progress = null;
      }
    },
  },

  methods: {
    async loadDxf(url) {
      this.isLoading = true;
      this.error = null;

      try {
        await this.dxfViewer.Load({
          url,
          fonts: this.fonts,
          progressCbk: this.onProgress,
          workerFactory: DxfViewerWorker,
        });
      } catch (err) {
        console.warn(err);
        this.error = err.toString();
      } finally {
        this.isLoading = false;
        this.progress = null;
        this.progressText = null;
        this.curProgressPhase = null;
      }
    },

    onProgress(phase, size, totalSize) {
      const phaseText = {
        font: "Carregando fontes...",
        fetch: "Baixando arquivo...",
        parse: "Interpretando DXF...",
        prepare: "Preparando dados para renderização...",
      };

      if (phase !== this.curProgressPhase) {
        this.progressText = phaseText[phase] || "";
        this.curProgressPhase = phase;
      }

      this.progress = totalSize ? size / totalSize : -1;
    },

    getViewer() {
      return this.dxfViewer;
    },
  },

  mounted() {
    this.dxfViewer = new DxfViewer(this.$refs.canvasContainer, this.options);

    const events = [
      "loaded",
      "cleared",
      "destroyed",
      "resized",
      "pointerdown",
      "pointerup",
      "viewChanged",
      "message",
    ];

    events.forEach((eventName) => {
      this.dxfViewer.Subscribe(eventName, (e) => {
        this.$emit("dxf-" + eventName, e);
      });
    });

    if (this.dxfUrl) {
      this.loadDxf(this.dxfUrl);
    }
 },

  destroyed() {
    this.dxfViewer?.Destroy();
    this.dxfViewer = null;
  },
};
</script>

<style scoped>
.canvasContainer {
  position: relative;
  width: 100%;
  height: 100%;
  min-width: 100px;
  min-height: 100px;
  background: #f5f5f5;
}

.loading {
  position: absolute;
  z-index: 10;
  top: 10px;
  left: 10px;
  background: rgba(255, 255, 255, 0.9);
  padding: 6px 12px;
  font-size: 14px;
  font-weight: bold;
  border-radius: 4px;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.1);
}

.progress {
  position: absolute;
  z-index: 20;
  width: 90%;
  margin: 20px 5%;
}

.progressBarContainer {
  background-color: #ddd;
  height: 10px;
  border-radius: 5px;
  overflow: hidden;
}

.progressBar {
  height: 100%;
  background-color: #2196f3;
  transition: width 0.2s ease;
}

.progressText {
  margin-top: 8px;
  font-size: 14px;
  color: #333;
  text-align: center;
}

.error {
  position: absolute;
  top: 20px;
  left: 20px;
  background: #ffebee;
  color: #c62828;
  padding: 10px;
  font-size: 14px;
  font-weight: bold;
  border: 1px solid #c62828;
  border-radius: 4px;
}
</style>
