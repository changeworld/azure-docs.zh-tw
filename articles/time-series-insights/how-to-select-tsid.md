---
title: 選擇時間序列識別碼的最佳做法-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解在 Azure 時間序列深入解析 Gen2 中選擇時間序列識別碼時的最佳做法。
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3f6f6a5ac1068f2eabca351e85376b8e16d1058
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016746"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>選擇時間序列識別碼的最佳做法

本文摘要說明您 Azure 時間序列深入解析 Gen2 環境的時間序列識別碼重要性，以及選擇其中一個的最佳作法。

## <a name="choose-a-time-series-id"></a>選擇時間序列識別碼

選取適當的時間序列識別碼是很重要的。 選擇時間序列識別碼就像選擇資料庫的分割區索引鍵一樣。 當您建立 Azure 時間序列深入解析 Gen2 環境時，這是必要的。

> [!IMPORTANT]
> 時間序列識別碼為：
>
> * 區分 *大小寫* 的屬性：字母和字元轉換用於搜尋、比較、更新和資料分割。
> * *不可變* 的屬性：建立之後就無法變更。

> [!TIP]
> 如果您的事件來源是 IoT 中樞，則您的時間序列識別碼可能是 ***iothub-連線-裝置識別碼** _。

下列主要的最佳作法包括：

_ 挑選具有許多相異值的分割區索引鍵 (例如數百個或數千個) 。 在許多情況下，這可能是您 JSON 中的裝置識別碼、感應器識別碼或標記識別項。
* 在您[時間序列模型](./concepts-model-overview.md).的分葉節點層級，時間序列識別碼應該是唯一的。
* 時間序列識別碼的屬性名稱字串的字元限制為128。 對於時間序列識別碼的屬性值，字元限制為1024。
* 如果時間序列識別碼缺少唯一的屬性值，則會將它視為 null 值，並遵循唯一性條件約束的相同規則。
* 如果您的時間序列識別碼是在複雜的 JSON 物件內進行嵌套，請務必在提供屬性名稱時遵循輸入簡維 [規則](./concepts-json-flattening-escaping-rules.md) 。 查看範例 [B](concepts-json-flattening-escaping-rules.md#example-b)。
* 您也可以選取最多 *三個* 索引鍵屬性作為時間序列識別碼。 它們的組合將會是表示時間序列識別碼的複合索引鍵。  
  > [!NOTE]
  > 您的三個索引鍵屬性必須是字串。
  > 您必須一次對此複合索引鍵進行查詢，而不是一個屬性。

## <a name="select-more-than-one-key-property"></a>選取一個以上的索引鍵屬性

下列案例說明如何選取多個索引鍵屬性做為您的時間序列識別碼。  

### <a name="example-1-time-series-id-with-a-unique-key"></a>範例1：具有唯一索引鍵的時間序列識別碼

* 您有舊版的資產機群。 每個都有唯一的索引鍵。
* 一個車隊是由屬性 **deviceId** 唯一識別。 對於其他車隊，唯一的屬性是 **objectId**。 這兩個車隊都不包含其他車隊的唯一財產。 在此範例中，您會選取兩個索引鍵（ **deviceId** 和 **objectId**）作為唯一索引鍵。
* 我們接受 null 值，且事件承載中缺少屬性的情況會計算為 null 值。 這也是適當的方式，可將資料傳送至兩個事件來源，其中每個事件來源的資料都有唯一的時間序列識別碼。

### <a name="example-2-time-series-id-with-a-composite-key"></a>範例2：具有複合索引鍵的時間序列識別碼

* 您需要讓同一資產群內的多個特性都必須是唯一的。
* 您是智慧型建築的製造商，並在每個房間內部署感應器。 在每個房間中，您通常會有相同的 **>sensorid** 值。 範例包括 **sensor1**、 **sensor2** 和 **sensor3**。
* 您的大樓在屬性 **>flrrm** 中的各個網站都有重迭的樓層和房間號碼。 這些數位具有值，例如 **1a**、 **2b** 和 **3a**。
* 您有一個包含值（例如 **Redmond**、**巴塞羅納** 和 **東京**）的屬性 [**位置**]。 若要建立唯一性，您必須將下列三個屬性指定為時間序列識別碼索引鍵： **>sensorid**、 **>flrrm** 和 **location**。

範例原始事件：

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

在 Azure 入口網站中，您可以輸入複合索引鍵，如下所示：

[![設定環境的時間序列識別碼。](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > 在 Azure 入口網站中，請勿在一個文字方塊中輸入以逗號分隔的屬性名稱，否則會將其視為包含逗號的單一屬性名稱。
  > 在自己的文字方塊中輸入每個屬性名稱。

## <a name="next-steps"></a>下一步

* 閱讀 JSON 簡維 [和轉義規則](./concepts-json-flattening-escaping-rules.md) ，以瞭解事件的儲存方式。

* 規劃您的 [Azure 時間序列深入解析 Gen2 環境](./how-to-plan-your-environment.md)。