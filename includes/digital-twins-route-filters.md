---
author: baanders
description: 包含 Azure 數位 Twins 路由篩選選項的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: e06e660a43aaa0ff5eb79bc00bd8a5d2c61c6580
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045305"
---
| 篩選名稱 | 描述 | 篩選文字架構 | 支援的值 | 
| --- | --- | --- | --- |
| True/False | 允許建立未篩選的路由，或停用路由，不傳送任何事件 | `<true/false>` | `true` = 未篩選的路由已啟用 <br> `false` = 路由已停用 |
| 類型 | 流經數位對應項實例的[事件種類](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | 以下是可能的事件種類值： <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 來源 | Azure 數位 Twins 實例的名稱 | `source = '<hostname>'`| 以下是可能的主機名稱值： <br> **針對通知**： `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **針對遙測**： `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| 主體 | 上述事件來源內容中事件的描述 | `subject = '<subject>'` | 以下是可能的主體值： <br>**針對通知**：主體為 `<twinid>` <br> 或主體的 URI 格式，可由多個元件或識別碼唯一識別：<br>`<twinid>/relationships/<relationshipid>`<br> **針對遙測**：主體是元件路徑 (如果遙測是從對應項元件) 發出，例如 `comp1.comp2` 。 如果遙測並非從元件發出，則其 [主旨] 欄位為空白。 |
| 資料結構描述 | DTDL 模型識別碼 | `dataschema = '<model-dtmi-ID>'` | 若 **為遙測**：資料架構是對應項的模型識別碼或發出遙測的元件。 例如， `dtmi:example:com:floor4;2` <br>**針對 (建立/刪除) 的通知**：在的通知主體中，可以存取資料架構 `$body.$metadata.$model` 。 <br>**針對 (更新) 的通知**：可于下列位置存取通知主體中的資料架構： `$body.modelId`|
| 內容類型 | 資料值的內容類型 | `datacontenttype = '<contentType>'` | 內容類型為 `application/json` |
| 規格版本 | 您正在使用的事件架構版本 | `specversion = '<version>'` | 版本必須是 `1.0` 。 這表示 CloudEvents 架構版本1。0 |
| 通知主體 | 參考通知欄位中的任何屬性 `data` | `$body.<property>` | 請參閱作法：瞭解通知範例的 [*事件資料*](../articles/digital-twins/how-to-interpret-event-data.md) 。 您 `data` 可以使用來參考欄位中的任何屬性。 `$body`

請注意，您可以將多個篩選新增至要求，如下所示： 

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter-multiple.json":::

下列資料類型支援作為上述資料參考所傳回的值：

| 資料類型 | 範例 |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**整數**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

定義路由篩選時支援下列運算子：

|Family|運算子|範例|
|-|-|-|
|邏輯|和、或 ( ) |`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|比較|<、<=、>、>=、=、！ =|`$body.temperature <= 5.5`

定義路由篩選時支援下列功能：

|函式|描述|範例|
|--|--|--|
|STARTS_WITH (x，y) |如果值是以字串開頭，則傳回 true `x` `y` 。|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x，y)  | 如果值 `x` 以字串結尾，則傳回 true `y` 。|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 如果值包含字串，則傳回 true `x` `y` 。|`CONTAINS(subject, '<twinID>')`|

當您執行或更新篩選時，變更可能需要幾分鐘的時間才會反映在資料管線中。