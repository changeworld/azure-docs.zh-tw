---
title: Azure 批次處理池調整啟動事件的大小
description: Batch 集區調整大小開始事件的參考。 示例顯示池調整大小啟動事件的主體，用於使用手動調整大小將池大小從 0 調整到 2 個節點。
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 1866e51da30fe5ed148d019c8720755e99757df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023577"
---
# <a name="pool-resize-start-event"></a>集區調整大小開始事件

 集區調整大小開始時，就會發出此事件。 由於集區調整大小為非同步事件，因此您可以預期當調整大小作業完成時，就會發出集區調整大小完成事件。

 下列範例顯示以手動調整大小的方式將集區大小從 0 調整為 2 個節點的集區調整大小開始事件內文。

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|`id`|String|池的 ID。|
|`nodeDeallocationOption`|String|指定當集區大小一直減少時，會自集區中移除節點。<br /><br /> 可能的值包括：<br /><br /> **requeue** – 終止執行中工作並重新排入佇列。 當作業啟用時，工作將再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **terminate** – 終止執行中工作。 工作將不會再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **taskcompletion** – 允許目前執行中工作完成。 等待時不排程任何新的工作。 所有工作完成時，即移除節點。<br /><br /> **Retaineddata** - 允許目前執行中工作完成，然後等待所有工作資料保留期到期。 等待時不排程任何新的工作。 當所有工作保留期到期時即移除節點。<br /><br /> 預設值為 requeue。<br /><br /> 如果集區大小增加，則值會設定為 [無效]****。|
|`currentDedicatedNodes`|Int32|目前指派至集區的計算節點數目。|
|`targetDedicatedNodes`|Int32|向集區要求的計算節點數目。|
|`currentLowPriorityNodes`|Int32|目前指派至集區的計算節點數目。|
|`targetLowPriorityNodes`|Int32|向集區要求的計算節點數目。|
|`enableAutoScale`|Bool|指定集區大小是否隨著時間自動調整。|
|`isAutoPool`|Bool|指定是否已透過作業的 AutoPool 機制建立集區。|
