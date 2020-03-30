---
title: 選擇時間序列 ID 的最佳做法 - Azure 時間序列見解 |微軟文檔
description: 在 Azure 時間序列見解預覽中選擇時序 ID 時瞭解最佳做法。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083520"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>選擇時間序列識別碼的最佳做法

本文總結了時序 ID 對於 Azure 時間序列見解預覽環境的重要性，以及選擇一個時間的最佳做法。

## <a name="choose-a-time-series-id"></a>選擇時間序列識別碼

選擇適當的時間序列 ID 至關重要。 選擇時間序列識別碼就像選擇資料庫的分割區索引鍵一樣。 創建時序見解預覽環境時，需要這樣做。 

> [!IMPORTANT]
> 時間序列 ID 是：
> * *區分大小寫的*屬性：字母和字元大小寫用於搜索、比較、更新和分區時。
> * *不可變*屬性：一旦創建，將無法更改。

> [!TIP]
> 如果事件源是 IoT 中心，則時間序列 ID 可能是***iothub-連接裝置識別碼***。

應遵循的主要最佳實踐包括：

* 選擇具有許多不同值（例如，數百或數千）的分區鍵。 在許多情況下，這可能是 JSON 中的裝置識別碼、感應器 ID 或標記識別項。
* 在您[時間序列模型](./time-series-insights-update-tsm.md).的分葉節點層級，時間序列識別碼應該是唯一的。
* 時間序列 ID 的屬性名稱字串的字元限制為 128。 對於時間序列 ID 的屬性值，字元限制為 1，024。
* 如果缺少時序 ID 的唯一屬性值，則將其視為空值，並遵循唯一性約束的相同規則。
* 您還可以選擇最多*三個*關鍵屬性作為時間序列 ID。 它們的組合將是表示時間序列 ID 的複合鍵。  
  > [!NOTE]
  > 三個鍵屬性必須是字串。
  > 您必須一次查詢此複合鍵而不是一個屬性。

## <a name="select-more-than-one-key-property"></a>選擇多個鍵屬性

以下方案描述選擇多個關鍵屬性作為時間序列 ID。  

### <a name="example-1-time-series-id-with-a-unique-key"></a>示例 1：帶唯一鍵的時間序列 ID

* 您擁有傳統資產佇列。 每個都有一個唯一的鍵。
* 屬性**設備 Id**唯一標識了一個佇列。 對於另一個佇列，唯一屬性是**objectId**。 兩支艦隊都不包含其他艦隊的獨特屬性。 在此示例中，您可以選擇兩個鍵，**設備 Id**和**objectId**，作為唯一鍵。
* 我們接受 null 值，並且缺少屬性在事件裝載中的存在計為空值。 這也是處理將資料發送到兩個事件源的適當方法，其中每個事件源中的資料具有唯一的時間序列 ID。

### <a name="example-2-time-series-id-with-a-composite-key"></a>示例 2：帶複合鍵的時間序列 ID

* 您需要讓同一資產群內的多個特性都必須是唯一的。 
* 您是智慧建築的製造商，在每個房間都部署感應器。 在每個房間中，您通常對**感應器 Id**具有相同的值。 例如**感應器1、****感應器2**和**感應器3。**
* 您的建築有重疊的樓層和房間號碼跨網站在物業**FLrRm。** 這些數位具有**1a、2b****2b**和**3a**等值。
* 您有一個屬性，**位置**，其中包含值，如**雷德蒙德**，**巴賽隆納**和**東京**。 要創建唯一性，請指定以下三個屬性作為時間序列 ID 鍵：**感應器**Id、flrRm 和**位置**。 **flrRm**

原始事件示例：

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

然後，在 Azure 門戶中，可以輸入複合鍵，如下所示： 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>後續步驟

* 閱讀更多有關[資料建模](./time-series-insights-update-tsm.md)。

* 規劃[Azure 時間序列見解預覽環境](./time-series-insights-update-plan.md)。