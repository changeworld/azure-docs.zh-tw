---
title: 執行 docker run 命令的容器範例
titleSuffix: Azure Cognitive Services
description: 語言偵測容器的 Docker run 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 1ac035e669ae6d5fcec8dfa8faee12d34039004b
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779729"
---
若要執行 *語言偵測* 容器，請執行下列 `docker run` 命令。 以您自己的值取代下列預留位置：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | 文字分析資源的索引鍵。 您可以在資源的 [ **金鑰和端點** ] 頁面上找到 Azure 入口網站上的。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 用於存取文字分析 API 的端點。 您可以在資源的 [ **金鑰和端點** ] 頁面上找到 Azure 入口網站上的。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行 *語言偵測* 容器
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。