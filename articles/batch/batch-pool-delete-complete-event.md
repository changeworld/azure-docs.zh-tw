---
title: Azure Batch 集區刪除完成事件
description: Batch 集區刪除完成事件的參考。 集區刪除作業完成時，就會發出此事件。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f00d32fd5e4eb49fddf4975a9b64ce792d0226dd
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962453"
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
|`id`|String|集區識別碼。|
|`startTime`|Datetime|集區刪除開始時間。|
|`endTime`|Datetime|集區刪除完成時間。|

## <a name="remarks"></a>備註
如需集區調整大小作業狀態與錯誤碼的詳細資訊，請參閱[將集區自帳戶中刪除](/rest/api/batchservice/delete-a-pool-from-an-account) (英文)
