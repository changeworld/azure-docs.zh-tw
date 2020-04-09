---
title: 語言偵測容器的 Docker 拉
titleSuffix: Azure Cognitive Services
description: 語言偵測容器的 Docker 拉取指令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876972"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>語言偵測容器的 Docker 拉

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令從 Microsoft 容器註冊表下載容器映射。

有關文字分析容器可用標記的完整說明,請參閱 Docker Hub 上[的語言檢測](https://go.microsoft.com/fwlink/?linkid=2018759)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
