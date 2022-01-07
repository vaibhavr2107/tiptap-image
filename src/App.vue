<template>
  <div id="app">
    <button type="button" @click="chooseFile">Upload Image</button>
    <input
      ref="imageFileSelector"
      class="image-file-selector"
      type="file"
      @change="onFileChange"
    />
    <template v-if="editor">
      <ImageBubbleMenu :editor="editor" />
      <editor-content :editor="editor" />
    </template>
  </div>
</template>

<script>
import { Editor, EditorContent, Extension } from "@tiptap/vue-2";
import Image from "@tiptap/extension-image";
import ImageBubbleMenu from "@/components/ImageBubbleMenu";
import ImageExtended from "@/extensions/ImageExtended";
import StarterKit from "@tiptap/starter-kit";

import { Schema } from "prosemirror-model";
import {
  defaultSettings,
  updateImageNode,
  imagePlugin,
} from "prosemirror-image-plugin";
import "prosemirror-image-plugin/dist/styles/common.css";
import "prosemirror-image-plugin/dist/styles/withResize.css";
import "prosemirror-image-plugin/dist/styles/sideResize.css";

const ImageTools = Extension.create({
  name: "image-tools",

  defaultOptions: {
    ...defaultSettings,
  },

  onBeforeCreate() {
    this.editor.schema = new Schema({
      nodes: updateImageNode(this.editor.schema.spec.nodes, this.options),
      marks: this.editor.schema.spec.marks,
    });
  },

  addProseMirrorPlugins() {
    return [imagePlugin(this.editor.schema, this.options)];
  },
});

export default {
  name: "App",
  components: {
    EditorContent,
    ImageBubbleMenu,
  },
  data() {
    return {
      editor: null,
    };
  },
  mounted() {
    this.editor = new Editor({
      extensions: [StarterKit, Image, ImageExtended, ImageTools],
      content: "Click on an image for options menu",
    });
  },

  beforeUnload() {
    this.editor.destroy();
  },
  methods: {
    chooseFile() {
      this.$refs.imageFileSelector.click();
    },
    onFileChange(e) {
      if (!!e.target.files[0]) {
        const reader = new FileReader();

        reader.readAsDataURL(e.target.files[0]);
        reader.onload = () => {
          this.editor.chain().focus().setImage({ src: reader.result }).run();
        };
      }
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;

  .image-file-selector {
    width: 0;
    height: 0;
    padding: 0;
    opacity: 0;
  }
}
</style>
