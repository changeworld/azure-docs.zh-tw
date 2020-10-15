---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545386"
---
在 [Azure 資源] 頁面 (左側功能表) 上的 [管理] 區段 (右上方功能表) 中，複製**範例查詢** URL，然後貼到新的瀏覽器索引標籤中。

端點 URL 會類似下列格式，APP-ID 和 KEY-ID 會取代為您自己的自訂子網域、應用程式識別碼和端點金鑰：

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```