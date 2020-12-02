---
title: Azure Data Factory 中的驗證活動
description: 驗證活動不會繼續執行管線，直到它以使用者指定的特定準則驗證附加的資料集為止。
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 63f4a7df76fc0480a2b0cdf2de13ff5e85aa93ef
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485819"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure Data Factory 中的驗證活動
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

您可以在管線中使用驗證，以確保只有在驗證附加的資料集參考存在、符合指定的準則或已達到 timeout 時，管線才會繼續執行。


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
NAME | 「驗證」活動的名稱 | String | 是 |
type | 必須設定為  **驗證**。 | String | 是 |
資料集 | 活動會封鎖執行，直到它已驗證此資料集參考存在，而且它符合指定的準則，或已達到 timeout 為止。 提供的資料集應該支援 "MinimumSize" 或 "ChildItems" 屬性。 | 資料集參考 | 是 |
timeout | 指定活動執行的逾時。 如果未指定任何值，預設值為7天 ( "7.00：00： 00" ) 。 格式為 d. hh： mm： ss | 字串 | 否 |
sleep | 驗證嘗試之間的延遲（以秒為單位）。 如果未指定任何值，預設值為10秒。 | 整數 | 否 |
childItems | 檢查資料夾是否有子專案。 可以設定為-true：驗證資料夾存在且具有專案。 封鎖直到至少有一個專案出現在資料夾中或達到 timeout 值為止。-false：驗證資料夾存在，而且是空的。 封鎖直到資料夾空白或達到 timeout 值為止。 如果未指定任何值，則會封鎖活動，直到資料夾存在或達到 timeout 為止。 | 布林值 | 否 |
minimumSize | 檔案的大小下限（以位元組為單位）。 如果未指定任何值，預設值為0個位元組 | 整數 | 否 |


## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動：

- [If 條件活動](control-flow-if-condition-activity.md)
- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
