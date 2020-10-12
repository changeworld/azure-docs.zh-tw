---
title: 情感分析容器的 Docker pull
titleSuffix: Azure Cognitive Services
description: 情感分析容器的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906085"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>情感分析 v3 容器的 Docker pull

情感分析容器 v3 容器有數種語言版本。 若要下載英文容器的容器，請使用下列命令。 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

若要下載其他語言的容器，請以 `en` 下列其中一種語言代碼取代。 

| 文字分析容器 | 語言代碼 |
|--|--|
| 英文 | `en` |
| 西班牙文 | `es` |
| 法文 | `fr` |
| 義大利文 | `it` |
| 德文 | `de` |
| 簡體中文 | `zh` |
| 繁體中文 | `zht` |
| 日文 | `ja` |
| 葡萄牙文 | `pt` |
| 荷蘭文 | `nl` |

如需文字分析容器可用標籤的完整描述，請參閱 [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654)。