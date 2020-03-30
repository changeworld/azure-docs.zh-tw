---
title: Azure 資料工廠中的驗證活動
description: 驗證活動不會繼續執行管道，直到它驗證附加資料集與使用者指定的特定條件。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678358"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure 資料工廠中的驗證活動
您可以在管道中使用驗證，以確保管道僅在驗證附加資料集引用存在、滿足指定條件或已達到超時後繼續執行。


## <a name="syntax"></a>語法

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
NAME | "驗證"活動的名稱 | String | 是 |
type | 必須設置為**驗證**。 | String | 是 |
資料集 | 活動將阻止執行，直到它驗證此資料集引用存在，並且它滿足指定的條件，或者已達到超時。 提供的資料集應支援"最小大小"或"子專案"屬性。 | 資料集引用 | 是 |
timeout | 指定活動執行的逾時。 如果未指定值，則預設值為 7 天（"7.00：00：00"）。 格式為 d.hh：mm：ss | String | 否 |
sleep | 驗證嘗試之間的延遲（以秒為單位）。 如果未指定值，則預設值為 10 秒。 | 整數  | 否 |
childItems | 檢查資料夾是否具有子專案。 可以設置為 true：驗證該資料夾是否存在，並且該資料夾是否具有專案。 塊，直到資料夾中至少有一個專案或達到超時值。 塊，直到資料夾為空或達到超時值。 如果未指定值，則活動將阻止，直到資料夾存在或達到超時。 | Boolean | 否 |
最小尺寸 | 以位元組為單位的檔的最小大小。 如果未指定值，則預設值為 0 位元組 | 整數  | 否 |


## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動：

- [If 條件活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [對於每個活動](control-flow-for-each-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
