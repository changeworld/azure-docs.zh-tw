---
title: Azure Batch 集區刪除完成事件
description: Batch 集區刪除完成事件的參考。 集區刪除作業完成時，就會發出此事件。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 6e009b55869b7ba8c81190837086557743d53219
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724049"
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
如需集區調整大小作業狀態與錯誤碼的詳細資訊，請參閱[將集區自帳戶中刪除](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account) (英文)
