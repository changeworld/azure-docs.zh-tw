---
author: baanders
description: 包含 Azure 數位 Twins 路由篩選選項的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: f9db7c489bb1c64a1649bba74d08fef5f0ec97b8
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801410"
---
| 篩選名稱 | 描述 | 篩選文字架構 | 支援的值 | 
| --- | --- | --- | --- |
| 類型 | 流經數位對應項實例的[事件種類](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | 以下是可能的事件種類值： <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 來源 | Azure 數位 Twins 實例的名稱 | `source = '<hostname>'`| 以下是可能的主機名稱值： <br> **針對通知**：`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **針對遙測**：`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| 主體 | 上述事件來源內容中事件的描述 | `subject = '<subject>'` | 以下是可能的主體值： <br>**通知**：主旨為`<twinid>` <br> 或主體的 URI 格式，其可由多個部分或識別碼唯一識別：<br>`<twinid>/relationships/<relationshipid>`<br> **針對遙測**：如果從對應項元件) （例如）發出遙測，則主體為元件路徑 (`comp1.comp2` 。 如果未從元件發出遙測，則其 [主旨] 欄位會是空的。 |
| 資料結構描述 | DTDL 模型識別碼 | `dataschema = '<model-dtmi-ID>'` | **針對遙測**：資料結構描述是對應項或發出遙測之元件的模型識別碼。 例如， `dtmi:example:com:floor4;2` <br>**通知**：不支援資料架構|
| 內容類型 | 資料值的內容類型 | `datacontenttype = '<contentType>'` | 內容類型為`application/json` |
| 規格版本 | 您所使用之事件架構的版本 | `specversion = '<version>'` | 版本必須是 `1.0` 。 這表示 CloudEvents 架構版本1。0 |
| True/False | 允許建立不含篩選的路由，或停用路由 | `<true/false>` | `true`= 路由已啟用，沒有篩選 <br> `false`= 路由已停用 |

您也可以使用下列作業來合併篩選：

| 篩選名稱 | 篩選文字架構 | 範例 | 
| --- | --- | --- |
| 和/或 | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| 和/或 | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| 嵌套的作業 | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> 在預覽期間，僅支援字串相等 (=、！ =) 。

當您執行或更新篩選時，變更可能需要幾分鐘的時間才會反映在資料管線中。
