---
title: 容器支援
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422073"
---
## <a name="create-a-speech-resource"></a>建立語音資源

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [ [建立 **語音**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) 資源]
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [建立]  ，並等待系統建立資源。 建立之後，流覽至資源頁面
1. 收集已設定 `endpoint` 和 API 金鑰：

    |入口網站的 [資源] 索引標籤|設定|值|
    |--|--|--|
    |**概觀**|端點|複製端點。 看起來像 `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**[索引鍵]**|API 金鑰|複製這兩個金鑰的1。 它是32英數位元字串，沒有空格或連字號 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 。|
