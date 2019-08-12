<template>
  <div id="app">
    <Tabs type="card" closable :on-tab-remove="closePDF">
      <TabPane v-for="tab in files" :key="tab.name" :label="tab.name">test</TabPane>
      <Button
        @click="handleTabsAdd"
        size="small"
        type="primary"
        shape="circle"
        icon="ios-add"
        slot="extra"
      ></Button>
    </Tabs>
    <Upload
      multiple
      ref="pdfUploader"
      v-show="!files.length"
      style="flex-grow:1"
      type="drag"
      action="/"
      accept=".pdf"
      :format="['pdf']"
      :before-upload="readPDF"
    >
      <div style="padding: 20px 0">
        <Icon type="ios-cloud-upload" size="74" style="color: #3399ff"></Icon>
        <p style="font-size:36px">Click or drag PDF files here</p>
      </div>
    </Upload>
  </div>
</template>

<script>
import PDF from "pdfjs-dist";
import { Promise } from 'q';

export default {
  data () {
    return {
      files: []
    }
  },
  methods: {
    handleTabsAdd () {
      this.$refs.pdfUploader.$el.children[0].click()
    },
    closePDF () {

    },
    readPDF (e) {
      const reader = new FileReader()
      reader.onload = async () => {
        const pdf = await PDF.getDocument(reader.result)
        const pages = await Promise.all(Array(pdf.numPages).fill(0).map((_, i) => pdf.getPage(i + 1)))
        const textContexts = await Promise.all(pages.map(page => page.getTextContent()))
        const naiveText = textContexts.map(c => c.items.map(t => t.str).join('')).join('')
        console.log(naiveText)
      }
      reader.readAsArrayBuffer(e)
      return false
    }
  }
}
</script>

<style>
body {
  height: 100vh;
}

#app {
  height: 100%;
  padding: 12px;
  display: flex;
  flex-direction: column;
}

.ivu-upload {
  flex-grow: 1;
  height: 100%;
  display: flex;
  flex-direction: row;
  align-items: center;
  align-content: center;
  justify-content: space-around;
}

.ivu-upload-list {
  display: none;
}
</style>