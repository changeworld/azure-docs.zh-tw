---
title: Docker run 命令的執行容器範例
titleSuffix: Azure Cognitive Services
description: 健全狀況容器的 Docker run 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108897"
---
若要執行容器，請先尋找其映射識別碼：
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

執行下列 `docker run` 命令。 以您自己的值取代下列預留位置：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | 文字分析資源的索引鍵。 您可以在資源的 [**金鑰和端點**] 頁面上，于 [Azure 入口網站上找到它。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 用來存取文字分析 API 的端點。 您可以在資源的 [**金鑰和端點**] 頁面上，于 [Azure 入口網站上找到它。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | 容器的映射識別碼。 | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | 容器的輸入目錄。 | Windows：`C:\healthcareMount` <br> Linux/MacOS：`/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

此命令：

- 假設輸入目錄存在於主機電腦上
- 從容器映射執行健康情況容器的文字分析
- 配置6個 CPU 核心和 12 gb 的記憶體
- 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
- 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。
