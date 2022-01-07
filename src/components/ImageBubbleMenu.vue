<template>
  <bubble-menu
    v-show="editor.isActive('custom-image')"
    class="bubble-menu"
    :editor="editor"
    :tippy-options="{ animation: false }"
  >
    <button
      :class="{
        'is-active': isActive({ size: 'small' }),
      }"
      @click="setImage({ size: 'small' })"
    >
      Small
    </button>
    <button
      :class="{
        'is-active': isActive({ size: 'medium' }),
      }"
      @click="setImage({ size: 'medium' })"
    >
      Medium
    </button>
    <button
      :class="{
        'is-active': isActive({ size: 'large' }),
      }"
      @click="setImage({ size: 'large' })"
    >
      Large
    </button>
    <span style="color: #aaa">|</span>
    <button
      :class="{
        'is-active': isActive({
          float: 'left',
        }),
      }"
      @click="setImage({ float: 'left' })"
    >
      Left
    </button>
    <button
      :class="{
        'is-active': isActive({
          float: 'none',
        }),
      }"
      @click="setImage({ float: 'none' })"
    >
      No Float
    </button>
    <button
      :class="{
        'is-active': isActive({
          float: 'right',
        }),
      }"
      @click="setImage({ float: 'right' })"
    >
      Right
    </button>
    <span style="color: #aaa">|</span>
    <button @click="addImage">Change</button>
    <input
      ref="bubbleImageFileSelector"
      class="bubble-image-file-selector"
      type="file"
      @change="onFileChange"
    />
  </bubble-menu>
</template>

<script>
import { BubbleMenu } from "@tiptap/vue-2";

export default {
  name: "ImageBubblemenu",
  components: {
    BubbleMenu,
  },
  props: {
    editor: {
      type: Object,
      required: true,
    },
  },
  methods: {
    addImage() {
      this.$refs.bubbleImageFileSelector.click();
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
    isActive(options) {
      return this.editor.isActive("custom-image", options);
    },
    setImage(options) {
      this.editor.chain().focus().setImage(options).run();
    },
  },
};
</script>

<style lang="scss">
.ProseMirror {
  &:focus,
  &:active {
    outline: none;
  }
  > * + * {
    margin-top: 0.75em;
  }
  img {
    width: 100%;
    height: auto;
    display: block;
    margin-left: auto;
    margin-right: auto;
    &.ProseMirror-selectednode {
      outline: 3px solid #68cef8;
    }
  }
  .custom-image-small {
    width: 200px;
  }
  .custom-image-medium {
    width: 500px;
  }
  .custom-image-large {
    width: 100%;
  }
  .custom-image-float-none {
    float: none;
  }
  .custom-image-float-left {
    float: left;
  }
  .custom-image-float-right {
    float: right;
  }
}

.bubble-menu {
  display: flex;
  background-color: #0d0d0d;
  padding: 0.2rem;
  border-radius: 0.5rem;

  button {
    border: none;
    background: none;
    color: #fff;
    font-size: 0.85rem;
    font-weight: 500;
    padding: 0 0.2rem;
    opacity: 0.6;
    &:hover,
    &.is-active {
      opacity: 1;
    }
    &.is-active {
      text-decoration: underline;
    }
  }

  .bubble-image-file-selector {
    position: absolute;
    top: 0;
    right: 0;
    width: 0;
    height: 0;
    padding: 0;
    opacity: 0;
  }
}
</style>
