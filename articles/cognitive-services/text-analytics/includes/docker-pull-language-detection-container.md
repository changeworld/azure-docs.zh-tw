---
title: 語言檢測容器的 Docker 拉
titleSuffix: Azure Cognitive Services
description: 語言檢測容器的 Docker 拉取命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966732"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>語言檢測容器的 Docker 拉

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令從 Microsoft 容器註冊表下載容器映射。

有關文本分析容器可用標記的完整說明，請參閱 Docker Hub 上[的語言檢測](https://go.microsoft.com/fwlink/?linkid=2018759)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
