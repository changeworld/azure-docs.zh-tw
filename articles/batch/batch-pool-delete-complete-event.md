---
title: Azure Batch 集區刪除完成事件
description: Batch 集區刪除完成事件的參考。 集區刪除作業完成時，就會發出此事件。
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 56dc4c91566cc5c41de6c91618d09e7a0ebb7172
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76929843"
---
# <a name="pool-delete-complete-event"></a>集區刪除完成事件

 集區刪除作業完成時，就會發出此事件。

 下列事件顯示集區刪除完成事件內文。

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|`id`|String|集區的識別碼。|
|`startTime`|日期時間|集區刪除開始時間。|
|`endTime`|日期時間|集區刪除完成時間。|

## <a name="remarks"></a>備註
如需集區調整大小作業狀態與錯誤碼的詳細資訊，請參閱[將集區自帳戶中刪除](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account) (英文)
