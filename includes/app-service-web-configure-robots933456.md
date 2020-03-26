---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78255806"
---
## <a name="robots933456-in-logs"></a>記錄中的 robots933456

您可能會在容器記錄中看到下列訊息：

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

您可以放心地忽略此訊息。 `/robots933456.txt` 是一個虛擬 URL 路徑，App Service 會使用該路徑來檢查容器是否可以處理要求。 404 回應只是指出路徑不存在，但其可讓 App Service 知道容器狀況良好，並已準備好回應要求。

