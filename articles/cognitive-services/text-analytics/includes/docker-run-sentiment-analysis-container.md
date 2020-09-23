---
title: 執行 docker run 命令的容器範例
titleSuffix: Azure Cognitive Services
description: 情感分析容器的 Docker run 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: e4a08217b5476f4934325518bec00d640003aece
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906093"
---
若要執行 *情感分析 v3* 容器，請執行下列 `docker run` 命令。 以您自己的值取代下列預留位置：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | 文字分析資源的索引鍵。 您可以在資源的 [ **金鑰和端點** ] 頁面上找到 Azure 入口網站上的。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 用於存取文字分析 API 的端點。 您可以在資源的 [ **金鑰和端點** ] 頁面上找到 Azure 入口網站上的。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行 *情感分析* 容器
* 配置一個 CPU 核心和 8 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。