<template>
  <div class="rect-container">
    <v-sheet :class="['rect-box']" :style="sheetStyle" elevation="2" theme="light"></v-sheet>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';

export default defineComponent({
  name: 'Rect',
  props: {
    width: {
      type: [Number, String] as PropType<number | string>,
      default: 200
    },
    height: {
      type: [Number, String] as PropType<number | string>,
      default: 200
    },
    color: {
      type: String,
      default: '#2196f3'
    }
  },
  computed: {
    sheetStyle(): Record<string, string> {
      const widthValue = typeof this.width === 'number' ? `${this.width}px` : `${parseInt(this.width as string, 10)}px`;
      const heightValue = typeof this.height === 'number' ? `${this.height}px` : `${parseInt(this.height as string, 10)}px`;
      return {
        width: widthValue,
        height: heightValue,
        backgroundColor: this.color
      };
    }
  }
});
</script>

<style scoped>
.rect-container {
  display: flex;
  justify-content: left;
  align-items: center;
  width: auto;
  height: auto;
}

.rect-box {
  display: block;
}
</style>