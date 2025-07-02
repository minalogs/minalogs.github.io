---
date: '{{ .Date }}'
draft: true
hidemeta: false
comments: false
tags: ["first"]
cover:
    image: "<image URL>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
title: '{{ replace .File.ContentBaseName "-" " " | title }}'
description: "Desc Text."
canonicalURL: 'https://https://minalogs.github.io/post/{{ replace .File.ContentBaseName "-" " " | title }}'
author: "Mina Liu"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
# 日常：願景 → 拆解 → 反饋
# 職能：【創辦人視角 - 問題與價值】 → 【PM視角 - 問題拆解與解決方案】 → 【HR視角 - 成果與效益】
---
