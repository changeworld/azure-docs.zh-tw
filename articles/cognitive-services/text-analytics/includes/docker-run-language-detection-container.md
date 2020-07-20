---
title: Docker run 命令的執行容器範例
titleSuffix: Azure Cognitive Services
description: 語言偵測容器的 Docker run 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: e39f9b537b2b57b455d2e0ab40ba09f1a6a890ba
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108899"
---
若要執行*語言偵測*容器，請執行下列 `docker run` 命令。 以您自己的值取代下列預留位置：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | 文字分析資源的索引鍵。 您可以在資源的 [**金鑰和端點**] 頁面上，于 [Azure 入口網站上找到它。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 用來存取文字分析 API 的端點。 您可以在資源的 [**金鑰和端點**] 頁面上，于 [Azure 入口網站上找到它。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行*語言偵測*容器
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。