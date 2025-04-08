<template>
  <div class="root">
    <div class="scroll-area">
      <ul class="layer-list">
        <li class="header">Layers</li>

        <li v-if="layers !== null" class="layer-item all-layers">
          <label class="checkbox-label">
            <input
              type="checkbox"
              :checked="showAll"
              @input="toggleAll($event.target.checked)"
            />
            <span class="label-text italic">All layers</span>
          </label>
        </li>

        <template v-if="layers !== null">
          <li v-for="layer in layers" :key="layer.name" class="layer-item">
            <label class="checkbox-label">
              <span
                class="color-icon"
                :style="{ backgroundColor: getCssColor(layer.color) }"
              ></span>

              <input
                type="checkbox"
                :checked="layer.isVisible"
                @input="toggleLayer(layer, $event.target.checked)"
              />

              <span class="label-text">{{ layer.displayName }}</span>
            </label>
          </li>
        </template>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "LayersList",

  props: {
    layers: {
      /* Expecting array of {name: string, color: number, isVisible: boolean} */
      type: Array,
      default: null,
    },
  },

  watch: {
    layers() {
      this.showAll = null;
    },
  },

  data() {
    return {
      showAll: null,
    };
  },

  methods: {
    toggleLayer(layer, newState) {
      this.$emit("toggleLayer", layer, newState);
      this.showAll = null;
    },

    toggleAll(newState) {
      this.showAll = newState;
      this.$emit("toggleAll", newState);
    },

    getCssColor(value) {
      let s = value.toString(16);
      while (s.length < 6) {
        s = "0" + s;
      }
      return "#" + s;
    },
  },
};
</script>

<style scoped>
.root {
  height: 100%;
  max-height: 100%;
  width: 300px;
  overflow-y: auto;
  background: #fff;
  border: 1px solid #ddd;
  padding: 0.5rem;
  box-sizing: border-box;
  font-family: sans-serif;
}

.scroll-area {
  max-height: 100%;
  overflow-y: auto;
}

.layer-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.header {
  font-weight: bold;
  margin-bottom: 0.5rem;
  font-size: 14px;
}

.layer-item {
  margin-bottom: 0.5rem;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.color-icon {
  width: 12px;
  height: 12px;
  border-radius: 2px;
  display: inline-block;
  border: 1px solid #aaa;
}

.label-text {
  font-size: 14px;
}

.italic {
  font-style: italic;
}
</style>
