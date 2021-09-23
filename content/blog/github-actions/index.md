---
title: Github Actions的使用
date: "2021-09-10 16:52:00"
tags:
  - Gatsby
  - github
description: "最近重新鼓捣 Gatsby Blog，翻看它了文档，发现了它支持多种数据来源。虽然我是采用markdown编写，处理成本地数据再利用GraphQL查询，这就利用了Gatsby提供的hooks，刚好在这里可以让我用来把数据提交到数据库。"
---

最近重新鼓捣 Gatsby Blog，认真翻看它了文档，发现用markdown本地文件只是其中一种数据来源方式，它还支持CMS、数据库或者其他第三方数据。用markdown编写发布文章是通过处理成本地数据再利用GraphQL查询，然后利用Gatsby提供的hooks自定义UI组件，而我刚好也可以在这个过程里把处理好的数据完成备份。

> A core feature of Gatsby is its ability to load data from anywhere -- CMSs, Markdown, other third-party systems, even spreadsheets. This allows teams to manage their content in nearly any backend they prefer.

之前的数据备份是通过GitHub webhooks来做的，但是通过Github Api获取的数据有限而且麻烦。而且项目本身就要使用Github Actions在push事件触发后来构建项目并发布到Github Pages，所以想在构建的过程中读取项目里的数据然后发送到指定的接口。

具体做法通过 gatsby-source-filesystem 来处理文件数据，构建时在gatsby-node.js里生成所需的json文件，然后读取json发送给服务器。

#### 首先第一步是读取json数据，输出结果为 package.outputs.content

```yml
    - name: Read json file
        id: package
        uses: juliangruber/read-file-action@v1
        with:
          path: ./public/gatsby-posts.json
```

#### 其次是获取此次数据变更的内容,得到的结果为file_changes.outputs:{files_modified,files_added,files_removed}

```yml
      - name: Get commit changes
        id: file_changes
        uses: trilom/file-changes-action@v1.2.3
        with:
          githubRepo: liweili50/liweili50.github.io
```
#### 最终就是把数据发送到指定的服务器接口

```yml
      - name: Make a HTTP Request
        uses: actionsflow/axios@v1
        with:
          url: ${{ secrets.WEBHOOK_URL }}
          method: POST
          body: |
            {
              "modified":${{ steps.file_changes.outputs.files_modified }},
              "added":${{ steps.file_changes.outputs.files_added}},
              "removed":${{ steps.file_changes.outputs.files_removed}},
              "posts":${{ steps.package.outputs.content }}
            }
```

这样就完成了在每次提交markdown文件后就会把Gatsby生成的数据以及变更的文件内容发送到服务器，服务端就可以按需更新数据库。

具体代码请移步：[https://github.com/liweili50/liweili50.github.io/blob/master/.github/workflows/main.yml](https://github.com/liweili50/liweili50.github.io/blob/master/.github/workflows/main.yml)
