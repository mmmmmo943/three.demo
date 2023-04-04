<template>
  <div>
    <button @click="createDocument">Create Document</button>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
// import { saveAs } from 'file-saver'
import * as docx from 'docx'
import imagePath from '../../assets/imgs/avatar.jpg'
import {
  File,
  HeadingLevel,
  Paragraph,
  StyleLevel,
  TableOfContents,
  AlignmentType,
  Table,
  TableCell,
  TableRow,
  WidthType,
  Footer,
  Header,
  NumberFormat,
  PageNumber,
  TextRun,
  ImageRun
} from 'docx'

const documentName = ref('My Document')
// const documentContent = ref('Hello, world!')

// 创建用于创建文档的方法
const createDocument = async () => {
  const paragraph = new Paragraph({
    text: 'Hello World',
    heading: HeadingLevel.HEADING_1,
    pageBreakBefore: true,
    alignment: AlignmentType.CENTER
  })
  const table = new Table({
    columnWidths: [3505, 5505],
    rows: [
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 3505,
              type: WidthType.DXA
            },
            children: [new Paragraph('Hello')]
          }),
          new TableCell({
            width: {
              size: 5505,
              type: WidthType.DXA
            },
            children: []
          })
        ]
      }),
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 3505,
              type: WidthType.DXA
            },
            children: []
          }),
          new TableCell({
            width: {
              size: 5505,
              type: WidthType.DXA
            },
            children: [new Paragraph('World')]
          })
        ]
      })
    ]
  })

  const response = await fetch(imagePath)
  const arrayBuffer = await response.arrayBuffer()
  const imageBytes = new Uint8Array(arrayBuffer)

  // 创建一个新的Document对象
  const doc = new docx.Document({
    features: {
      updateFields: true
    },
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
        },
        {
          id: 'MySpectacularStyle2',
          name: 'My Spectacular Style2',
          basedOn: 'Heading2',
          next: 'Heading2',
          quickFormat: true,
          run: {
            italics: true,
            color: '930000'
          }
        }
      ]
    },
    sections: [
      {
        properties: {
          page: {
            pageNumbers: {
              start: 1,
              formatType: NumberFormat.DECIMAL
            }
          }
        },
        headers: {
          default: new Header({
            children: [
              new Paragraph({
                children: [
                  new TextRun('Foo Bar corp. '),
                  new TextRun({
                    children: ['Page Number ', PageNumber.CURRENT]
                  }),
                  new TextRun({
                    children: [' to ', PageNumber.TOTAL_PAGES]
                  })
                ]
              })
            ]
          })
        },
        footers: {
          default: new Footer({
            children: [
              new Paragraph({
                alignment: AlignmentType.CENTER,
                children: [
                  new TextRun('Foo Bar corp. '),
                  new TextRun({
                    children: ['Page Number: ', PageNumber.CURRENT]
                  }),
                  new TextRun({
                    children: [' to ', PageNumber.TOTAL_PAGES]
                  })
                ]
              })
            ]
          })
        },
        children: [
          new TableOfContents('Summary', {
            hyperlink: true,
            headingStyleRange: '1-2',
            stylesWithLevels: [
              new StyleLevel('MySpectacularStyle', 1),
              new StyleLevel('MySpectacularStyle2', 2)
            ]
          }),
          paragraph,
          table,
          new Paragraph({
            children: [
              new ImageRun({
                data: imageBytes,
                transformation: {
                  width: 100,
                  height: 100
                }
              })
            ]
          }),
          new Paragraph({
            text: 'Header #1234',
            heading: HeadingLevel.HEADING_1,
            pageBreakBefore: true,
            alignment: AlignmentType.CENTER,
            style: 'MySpectacularStyle'
          }),
          new Paragraph("I'm a little text very nicely written.'"),
          new Paragraph({
            text: 'Header #2',
            heading: HeadingLevel.HEADING_1,
            pageBreakBefore: true,
            alignment: AlignmentType.CENTER,
            style: 'MySpectacularStyle'
          }),
          new Paragraph("I'm a other text very nicely written.'"),
          new Paragraph({
            text: 'Header #2.1',
            heading: HeadingLevel.HEADING_2,
            pageBreakBefore: true,
            style: 'MySpectacularStyle2'
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
