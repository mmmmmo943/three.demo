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
import fileFace1 from '../../assets/imgs/fileFace.png'
import fileFace2 from '../../assets/imgs/fileFace2.jpeg'
import headerPic from '../../assets/imgs/headerPic.png'
import {
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
  ImageRun,
  HeightRule,
  BorderStyle
} from 'docx'

const documentName = ref('My Document')
// const documentContent = ref('Hello, world!')

const checkedBox = '☑'
const uncheckedBox = '☐'
// 图片处理方法
const imageMethod = async (image: any) => {
  let response = await fetch(image)
  let arrayBuffer = await response.arrayBuffer()
  let imageBytes = new Uint8Array(arrayBuffer)
  return imageBytes
}
// 创建用于创建文档的方法
const createDocument = async () => {
  const paragraph = new Paragraph({
    text: 'Hello World',
    heading: HeadingLevel.HEADING_1,
    pageBreakBefore: true,
    alignment: AlignmentType.CENTER
  })
  const table = new Table({
    width: {
      size: 100,
      type: WidthType.PERCENTAGE
    },
    // borders: {
    //   insideHorizontal: grayDashedBorder,
    //   insideVertical: grayDashedBorder,
    //   top: grayDashedBorder,
    //   bottom: grayDashedBorder,
    //   left: grayDashedBorder,
    //   right: grayDashedBorder
    // },
    alignment: AlignmentType.CENTER,
    rows: [
      new TableRow({
        height: {
          value: 600, // 设置行高，单位为twips（1/20磅）
          rule: HeightRule.ATLEAST
        },
        children: [
          new TableCell({
            width: {
              size: 20,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                alignment: AlignmentType.CENTER,
                children: [
                  new TextRun({
                    text: '项目名称',
                    size: 40, // 设置字体大小，单位为半点（1/144英寸）
                    font: {
                      name: 'Arial'
                    },
                    bold: true
                  })
                ]
              })
            ]
          }),
          new TableCell({
            width: {
              size: 80,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                children: [
                  new TextRun({
                    text: '项目名称',
                    size: 40, // 设置字体大小，单位为半点（1/144英寸）
                    font: {
                      name: 'Arial'
                    },
                    bold: true
                  })
                ],
                alignment: AlignmentType.CENTER
              })
            ]
          })
        ]
      }),
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 20,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                text: '主题',
                alignment: AlignmentType.CENTER
              })
            ]
          }),
          new TableCell({
            width: {
              size: 80,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                text: '关于信息化的咨询意见',
                alignment: AlignmentType.CENTER
              })
            ]
          })
        ]
      }),
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 20,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                text: '咨询类别',
                alignment: AlignmentType.CENTER
              })
            ]
          }),
          new TableCell({
            width: {
              size: 80,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                children: [
                  new TextRun(`${checkedBox} 选项 1 `),
                  new TextRun('    '),
                  new TextRun(`${uncheckedBox} 选项 2`)
                ],
                alignment: AlignmentType.CENTER
              })
            ]
          })
        ]
      }),
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 20,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                text: '主送单位/部门',
                alignment: AlignmentType.CENTER
              })
            ]
          }),
          new TableCell({
            width: {
              size: 80,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                children: [
                  new Paragraph({
                    text: '信息化部',
                    alignment: AlignmentType.CENTER
                  })
                ],
                alignment: AlignmentType.CENTER
              })
            ]
          })
        ]
      }),
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 20,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                text: '抄送单位/部门',
                alignment: AlignmentType.CENTER
              })
            ]
          }),
          new TableCell({
            width: {
              size: 80,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                children: [
                  new Paragraph({
                    text: '第五事业部',
                    alignment: AlignmentType.CENTER
                  })
                ],
                alignment: AlignmentType.CENTER
              })
            ]
          })
        ]
      }),
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 20,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                text: '编制人',
                alignment: AlignmentType.CENTER
              })
            ]
          }),
          new TableCell({
            width: {
              size: 80,
              type: WidthType.PERCENTAGE
            },
            children: []
          })
        ]
      }),
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 20,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                text: '审核人',
                alignment: AlignmentType.CENTER
              })
            ]
          }),
          new TableCell({
            width: {
              size: 80,
              type: WidthType.PERCENTAGE
            },
            children: []
          })
        ]
      }),
      new TableRow({
        children: [
          new TableCell({
            width: {
              size: 20,
              type: WidthType.PERCENTAGE
            },
            children: [
              new Paragraph({
                text: '审定人',
                alignment: AlignmentType.CENTER
              })
            ]
          }),
          new TableCell({
            width: {
              size: 80,
              type: WidthType.PERCENTAGE
            },
            children: []
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
        children: [
          new Paragraph({
            alignment: AlignmentType.RIGHT,
            children: [
              new ImageRun({
                data: await imageMethod(fileFace2),
                transformation: {
                  width: 400,
                  height: 100
                }
              })
            ]
          }),
          new Paragraph({
            alignment: AlignmentType.RIGHT,
            children: [
              new ImageRun({
                data: await imageMethod(headerPic),
                transformation: {
                  width: 350,
                  height: 40
                }
              })
            ]
          }),
          new Paragraph({
            spacing: {
              before: 720 // 在图片上方添加 0.5 英寸的空间
            },
            alignment: AlignmentType.CENTER,
            children: [
              new ImageRun({
                data: await imageMethod(fileFace1),
                transformation: {
                  width: 1000,
                  height: 400
                }
              })
            ]
          })
        ]
      },
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
              // new Paragraph({
              //   alignment: AlignmentType.LEFT,
              //   children: [
              //     new ImageRun({
              //       data: await imageMethod(headerPic),
              //       transformation: {
              //         width: 350,
              //         height: 40
              //       }
              //     }),
              //     new TextRun({
              //       text: '咨询报告',
              //       size: 30, // 设置字体大小，单位为半点（1/144英寸）
              //       font: {
              //         name: 'Arial'
              //       },
              //       color: '#A5A292',
              //       bold: true
              //     }),
              //     new TextRun({
              //       text: '\r\n日期 2023.2.8',
              //       size: 15, // 设置字体大小，单位为半点（1/144英寸）
              //       font: {
              //         name: 'Arial'
              //       },
              //       color: '#A5A292',
              //       bold: true
              //     })
              //   ]
              // }),
              new Table({
                rows: [
                  new TableRow({
                    height: {
                      value: 60, // 设置行高，单位为twips（1/20磅）
                      rule: HeightRule.ATLEAST
                    },
                    children: [
                      new TableCell({
                        width: {
                          size: 70,
                          type: WidthType.PERCENTAGE
                        },
                        children: [
                          new Paragraph({
                            alignment: AlignmentType.CENTER,
                            children: [
                              new ImageRun({
                                data: await imageMethod(headerPic),
                                transformation: {
                                  width: 350,
                                  height: 40
                                }
                              })
                            ]
                          })
                        ]
                      }),
                      new TableCell({
                        width: {
                          size: 30,
                          type: WidthType.PERCENTAGE
                        },
                        children: [
                          new Paragraph({
                            alignment: AlignmentType.CENTER,
                            children: [
                              new TextRun({
                                text: '咨询报告',
                                size: 30, // 设置字体大小，单位为半点（1/144英寸）
                                font: {
                                  name: 'Arial'
                                },
                                color: '#A5A292',
                                bold: true
                              })
                            ]
                          }),
                          new Paragraph({
                            alignment: AlignmentType.CENTER,
                            children: [
                              new TextRun({
                                text: '日期 2023.2.8',
                                size: 15, // 设置字体大小，单位为半点（1/144英寸）
                                font: {
                                  name: 'Arial'
                                },
                                color: '#A5A292',
                                bold: true
                              })
                            ]
                          })
                        ]
                      })
                    ]
                  })
                ],
                width: {
                  size: 100,
                  type: WidthType.PERCENTAGE
                },
                borders: {
                  top: {
                    style: BorderStyle.NONE
                  },
                  right: {
                    style: BorderStyle.NONE
                  },
                  bottom: {
                    style: BorderStyle.NONE
                  },
                  left: {
                    style: BorderStyle.NONE
                  }
                }
              })
            ]
          }),
          first: new Header({ children: [] })
        },
        footers: {
          default: new Footer({
            children: [
              new Paragraph({
                alignment: AlignmentType.CENTER,
                children: [
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
            text: 'Header #124556',
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
