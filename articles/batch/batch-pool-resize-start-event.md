---
title: Azure Batch 集區調整大小開始事件
description: Batch 集區調整大小開始事件的參考。 範例顯示集區的主體調整大小開始事件（從0到2個節點，並以手動方式調整大小）。
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: b4aa503c5dfbe00a77216277bdaf7e4c0dc3b1bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115868"
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
|`id`|字串|集區的識別碼。|
|`nodeDeallocationOption`|字串|指定當集區大小一直減少時，會自集區中移除節點。<br /><br /> 可能的值包括：<br /><br /> **requeue** – 終止執行中工作並重新排入佇列。 當作業啟用時，工作將再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **terminate** – 終止執行中工作。 工作將不會再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **taskcompletion** – 允許目前執行中工作完成。 等待時不排程任何新的工作。 所有工作完成時，即移除節點。<br /><br /> **Retaineddata** - 允許目前執行中工作完成，然後等待所有工作資料保留期到期。 等待時不排程任何新的工作。 當所有工作保留期到期時即移除節點。<br /><br /> 預設值為 requeue。<br /><br /> 如果集區大小增加，則值會設定為 [無效]****。|
|`currentDedicatedNodes`|Int32|目前指派至集區的計算節點數目。|
|`targetDedicatedNodes`|Int32|向集區要求的計算節點數目。|
|`currentLowPriorityNodes`|Int32|目前指派至集區的計算節點數目。|
|`targetLowPriorityNodes`|Int32|向集區要求的計算節點數目。|
|`enableAutoScale`|Bool|指定集區大小是否隨著時間自動調整。|
|`isAutoPool`|Bool|指定是否已透過作業的 AutoPool 機制建立集區。|
