---
title: V3 API 查詢字串參數
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610775"
---
V3 API 查詢字串參數包括：

|查詢參數|LUIS 入口網站名稱|類型|版本|預設|目的|
|--|--|--|--|--|--|
|`log`|儲存記錄檔|boolean|V2 & V3|false|將查詢儲存在記錄檔中。 預設值為 false。|
|`query`|-|字串|僅限第 3 版|無預設值-GET 要求中需要它|**在 V2 中**，要預測的語句是在 `q` 參數中。 <br><br>**在 V3**中，此功能會在參數中傳遞 `query` 。|
|`show-all-intents`|包含所有意圖的分數|boolean|僅限第 3 版|false|傳回在**預測. 意圖**物件中具有對應分數的所有意圖。 意圖會以物件的形式傳回父 `intents` 物件。 這可讓您以程式設計方式存取，而不需要在陣列中尋找意圖： `prediction.intents.give` 。 在 V2 中，這些會在陣列中傳回。 |
|`verbose`|包含更多實體詳細資料|boolean|V2 & V3|false|**在第2版中**，當設定為 true 時，會傳回所有預測意圖。 如果您需要所有預測的意圖，請使用的 V3 參數 `show-all-intents` 。<br><br>**在 V3 中**，這個參數只提供實體預測的實體中繼資料詳細資料。  |
|`timezoneOffset`|-|字串|V2|-|適用于 datetimeV2 實體的時區。|
|`datetimeReference`|-|字串|V3|-|適用于 datetimeV2 實體的[時區](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 取代為 `timezoneOffset` V2。|