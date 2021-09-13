---
title: Github Actions 使用
date: "2020-09-10"
description: ""
---

最近重新鼓捣 Gatsby Blog，发现它的数据源支持所有方式。Gatsby allows you to pull data from headless CMSs, databases, SaaS services, public API, and your private APIs.

当然我用的是 markdown 来写，通过 gatsby-source-filesystem 来处理文件数据，而且提供了钩子函数让你在 build 的时候做一些定制化，可以结合自定义的 react 组件完成渲染，也可以处理数据，比如备份起来等等

所以我之前放在 GitHub Hooks 里做的数据备份好像就没那么优雅了，通过 GitHub Hooks 的 push 事件把提交内容发送到我自己的服务上，然后解析保存到数据库，解析这一步处理起来就很繁琐。那把备份放在 Gatsby 提供的数据处理函数里不就很轻松了吗？

那具体该怎么做呢？首先想到的就是做一个数据提交的方法，每次 build 的时候把数据发送到指定接口，这当然不难，但是感觉变得不那么纯粹了。这时突然想到了 Github Actions，那能不能用它来实现呢？
