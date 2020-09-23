---
title: 關鍵片語擷取容器的 Docker pull
titleSuffix: Azure Cognitive Services
description: 關鍵片語擷取容器的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906003"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>關鍵片語擷取容器的 Docker pull

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令從 Microsoft Container Registry 下載容器映射。

如需文字分析容器可用標籤的完整描述，請參閱 Docker Hub 上的 [關鍵片語擷取](https://go.microsoft.com/fwlink/?linkid=2018757) 容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
