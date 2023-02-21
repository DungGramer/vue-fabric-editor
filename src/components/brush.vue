<template>
  <button @click="big">Toggle Draw</button>
</template>

<script>
import select from '@/mixins/select';

export default {
  name: 'BrushTool',
  mixins: [select],
  data() {
    return {
      canvas: null,
      brush: null,
      isDrawingMode: false,
    };
  },
  // computed: {
  //   toggleDraw() {
  //     this.canvas.editor.editorWorkspace.toggleDraw();
  //   },
  //   big() {
  //     this.canvas.editor.editorWorkspace.big();
  //   },
  // },
};
</script>

<style></style>
