---
title: Azure 批次處理池調整完整事件的大小
description: Batch 集區調整大小完成事件的參考。 查看增加大小並成功完成的池的示例。
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
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022217"
---
# <a name="pool-resize-complete-event"></a>集區調整大小完成事件

 集區調整大小完成或失敗時，就會發出此事件。

 下列範例顯示大小增加且成功完成的集區其集區調整大小完成事件內文。

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|`id`|String|池的 ID。|
|`nodeDeallocationOption`|String|指定當集區大小一直減少時，會自集區中移除節點。<br /><br /> 可能的值包括：<br /><br /> **requeue** – 終止執行中工作並重新排入佇列。 當作業啟用時，工作將再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **terminate** – 終止執行中工作。 工作將不會再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **taskcompletion** – 允許目前執行中工作完成。 等待時不排程任何新的工作。 所有工作完成時，即移除節點。<br /><br /> **保留資料**- 允許當前正在運行的任務完成，然後等待所有任務資料保留期過期。 等待時不排程任何新的工作。 當所有工作保留期到期時即移除節點。<br /><br /> 預設值為 requeue。<br /><br /> 如果集區大小增加，則值會設定為 [無效]****。|
|`currentDedicatedNodes`|Int32|當前分配給池的專用計算節點數。|
|`targetDedicatedNodes`|Int32|為池請求的專用計算節點數。|
|`currentLowPriorityNodes`|Int32|當前分配給池的低優先順序計算節點數。|
|`targetLowPriorityNodes`|Int32|為池請求的低優先順序計算節點數。|
|`enableAutoScale`|Bool|指定集區大小是否隨著時間自動調整。|
|`isAutoPool`|Bool|指定是否已透過作業的 AutoPool 機制建立集區。|
|`startTime`|Datetime|集區調整大小開始時間。|
|`endTime`|Datetime|集區調整大小完成時間。|
|`resultCode`|String|調整大小的結果。|
|`resultMessage`|String| 有關結果的詳細消息。<br /><br /> 如果調整大小已成功完成，表示作業已成功。|
