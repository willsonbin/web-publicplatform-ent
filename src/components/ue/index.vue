<template>
  <div>
    <script id="editor" type="text/plain" />
  </div>
</template>
<script>
export default {
  name: 'UE',
  props: {
    defaultMsg: {
      type: String,
      default: ''
    },
    config: {
      type: Object,
      default: () => {}
    }
  },
  data() {
    return {
      editor: null
    }
  },
  mounted() {
    const _this = this
    // eslint-disable-next-line no-undef
    this.editor = UE.getEditor('editor', this.config) // 初始化UE
    this.editor.addListener('ready', function() {
      _this.editor.setContent(_this.defaultMsg) // 确保UE加载完成后，放入内容。
    })
  },
  destroyed() {
    this.editor.destroy()
  },
  methods: {
    getUEContent() { // 获取内容方法
      return this.editor.getContent()
    }
  }
}

</script>
