<template>
  <div>
    <button @click="createDocument">Create Document</button>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
// import { saveAs } from 'file-saver'
import * as docx from 'docx'
import { HeadingLevel, Paragraph, StyleLevel, TableOfContents } from 'docx'

const documentName = ref('My Document')
// const documentContent = ref('Hello, world!')

// 创建用于创建文档的方法
const createDocument = () => {
  const paragraph = new Paragraph({
    text: 'Hello World',
    heading: HeadingLevel.HEADING_1
  })
  // 创建一个新的Document对象
  const doc = new docx.Document({
    styles: {
      paragraphStyles: [
        {
          id: 'MySpectacularStyle',
          name: 'My Spectacular Style',
          basedOn: 'Heading1',
          next: 'Heading1',
          quickFormat: true,
          run: {
            italics: true,
            color: '990000'
          }
        }
      ]
    },
    sections: [
      {
        children: [
          new TableOfContents('Summary', {
            hyperlink: true,
            headingStyleRange: '1-5',
            stylesWithLevels: [new StyleLevel('MySpectacularStyle', 1)]
          }),
          paragraph,
          new Paragraph({
            text: 'Header #1',
            heading: HeadingLevel.HEADING_1
          }),
          new Paragraph("I'm a little text very nicely written.'"),
          new Paragraph({
            text: 'Header #2',
            heading: HeadingLevel.HEADING_1
          }),
          new Paragraph("I'm a other text very nicely written.'"),
          new Paragraph({
            text: 'Header #2.1',
            heading: HeadingLevel.HEADING_2
          }),
          new Paragraph("I'm a another text very nicely written.'"),
          new Paragraph({
            text: 'My Spectacular Style #1',
            style: 'MySpectacularStyle'
          })
        ]
      }
    ]
  })

  // 将Document对象打包成二进制数据（Blob）
  docx.Packer.toBlob(doc).then((blob) => {
    // 创建一个下载链接并触发下载
    const link = document.createElement('a')
    link.href = window.URL.createObjectURL(blob)
    link.download = `${documentName.value}.docx`
    link.click()
    window.URL.revokeObjectURL(link.href)
  })
}
</script>

<style></style>
