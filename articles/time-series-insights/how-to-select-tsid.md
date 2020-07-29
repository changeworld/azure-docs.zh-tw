---
title: 選擇時間序列識別碼的最佳做法-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解在 Azure 時間序列深入解析 Gen2 中選擇時間序列識別碼時的最佳作法。
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: seodec18
ms.openlocfilehash: 94abdf8735fa487f46d423f17f7e1ff7bc853eb7
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289881"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>選擇時間序列識別碼的最佳做法

本文摘要說明 Azure 時間序列深入解析 Gen2 環境之時間序列識別碼的重要性，以及選擇其中一項的最佳做法。

## <a name="choose-a-time-series-id"></a>選擇時間序列識別碼

選取適當的時間序列識別碼是很重要的。 選擇時間序列識別碼就像選擇資料庫的分割區索引鍵一樣。 當您建立 Azure 時間序列深入解析 Gen2 環境時，這是必要的。 

> [!IMPORTANT]
> 時間序列識別碼如下：
>
> * 區分*大小寫*的屬性：在搜尋、比較、更新及進行分割時，會使用字母和字元轉換。
> * *不可變*的屬性：一旦建立之後，就無法變更。

> [!TIP]
> 如果您的事件來源是 IoT 中樞，您的時間序列識別碼可能會是 iothub-連線***裝置識別碼***。

遵循的主要最佳做法包括：

* 挑選具有許多相異值的分割區索引鍵（例如，上百或上千）。 在許多情況下，這可能是您的 JSON 中的裝置識別碼、感應器識別碼或標記識別項。
* 在您[時間序列模型](./concepts-model-overview.md).的分葉節點層級，時間序列識別碼應該是唯一的。
* 時間序列識別碼的屬性名稱字串的字元限制為128。 若為時間序列識別碼的屬性值，字元限制為1024。
* 如果遺漏時間序列識別碼的唯一屬性值，則會將它視為 null 值，並遵循唯一性條件約束的相同規則。
* 如果您的時間序列識別碼是嵌套在複雜的 JSON 物件中，請務必在提供您的屬性名稱時遵循輸入簡維[規則](./concepts-json-flattening-escaping-rules.md)。 請參閱範例[B](concepts-json-flattening-escaping-rules.md#example-b)。 
* 您也可以選取最多*三個*索引鍵屬性作為時間序列識別碼。 其組合會是代表時間序列識別碼的複合索引鍵。  
  > [!NOTE]
  > 您的三個索引鍵屬性必須是字串。
  > 您必須一次查詢此複合索引鍵，而不是一個屬性。

## <a name="select-more-than-one-key-property"></a>選取一個以上的索引鍵屬性

下列案例說明如何選取一個以上的索引鍵屬性作為時間序列識別碼。  

### <a name="example-1-time-series-id-with-a-unique-key"></a>範例1：具有唯一索引鍵的時間序列識別碼

* 您有舊版的資產機群。 每個都有唯一的索引鍵。
* 一個車隊由屬性**deviceId**唯一識別。 針對另一個車隊，unique 屬性為**objectId**。 這兩個車隊都不包含其他車隊的唯一屬性。 在此範例中，您會選取兩個金鑰（ **deviceId**和**objectId**）做為唯一索引鍵。
* 我們接受 null 值，而在事件裝載中缺少屬性的目前狀態會計算為 null 值。 這也是處理將資料傳送至兩個事件來源的適當方式，其中每個事件來源中的資料都有唯一的時間序列識別碼。

### <a name="example-2-time-series-id-with-a-composite-key"></a>範例2：具有複合索引鍵的時間序列識別碼

* 您需要讓同一資產群內的多個特性都必須是唯一的。
* 您是智慧型建築的製造商，並在每個房間部署感應器。 在每個空間中，您通常會有相同的**sensorId**值。 範例包括**sensor1**、 **sensor2**和**sensor3**。
* 您的大樓在屬性**flrRm**中的各個網站之間有重迭的樓層和房間號碼。 這些數位具有**1a**、 **2b**和**3a**之類的值。
* 您有一個屬性（ **location**），其中包含**Redmond**、**巴塞羅納**和**東京**等值。 若要建立唯一性，您可以指定下列三個屬性做為時間序列識別碼索引鍵： **sensorId**、 **flrRm**和**location**。

範例原始事件：

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

在 Azure 入口網站中，您可以接著輸入複合索引鍵，如下所示：
 
[![設定環境的時間序列識別碼。](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > 在 Azure 入口網站中，請勿在一個文字方塊中輸入以逗號分隔的屬性名稱，否則會被視為包含逗號的單一屬性名稱。
  > 在自己的文字方塊中輸入每個屬性名稱。

## <a name="next-steps"></a>後續步驟

* 閱讀 JSON 簡維[和「轉義規則](./concepts-json-flattening-escaping-rules.md)」，以瞭解事件的儲存方式。

* 規劃您的[Azure 時間序列深入解析 Gen2 環境](./time-series-insights-update-plan.md)。
