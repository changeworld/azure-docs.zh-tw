---
title: 健全狀況容器的 Docker pull
titleSuffix: Azure Cognitive Services
description: 健全狀況容器的文字分析 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108901"
---
填寫並提交[認知服務容器要求表單](https://aka.ms/cognitivegate)，以要求容器的存取權。

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

使用 docker login 命令搭配上線電子郵件中提供的認證，連接到認知服務容器的私人容器登錄。

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令從我們的私人容器登錄下載此容器映射。

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
