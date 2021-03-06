---
title: 預先建立的網域參考-LUIS
titleSuffix: Azure Cognitive Services
description: 預先建立的定義域參考是從 Language Understanding Intelligent Service (LUIS) 預先建立的意圖和實體集合。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 73b279f98011181b329cdb010041022ab0da57f0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018628"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>您 LUIS 應用程式預先建置的網域參考
此參考提供[預先建立的定義域](./howto-add-prebuilt-models.md)相關資訊，這是 LUIS 提供之預先建立的意圖和實體集合。

相反地，[自訂網域](luis-how-to-start-new-app.md)一開始沒有任何意圖和模型。 您可以將任何預先建立的定義域意圖和實體新增至自訂模型。

## <a name="prebuilt-domains-per-language"></a>每一語言的預建網域

下表摘要列出目前支援的網域。 英文版的支援通常比其他支援更完整。

| 實體類型       | ZH-TW      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Calendar  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|通訊  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 電子郵件     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 備註     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 下訂   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Todo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 公用程式      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 天氣        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

在下列情況中 **不支援** 預先建立的網域：

* 加拿大法文
* Hindi
* 西班牙文墨西哥

## <a name="next-steps"></a>下一步

瞭解 [簡單的實體](reference-entity-simple.md)。