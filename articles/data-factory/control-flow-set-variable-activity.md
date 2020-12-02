---
title: Azure Data Factory 中的設定變數活動
description: 了解如何使用「設定變數」活動來設定在 Data Factory 管線中定義的現有變數值
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: f3c1fae2cbf2a1ba8b71dcbc8f6639bda4765f5c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498382"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure Data Factory 中的設定變數活動
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用「設定變數」活動，為定義於 Data Factory 管線中的「字串」、「布林」或「陣列」類型設定現有變數值。

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 必要
-------- | ----------- | --------
NAME | 管線中的活動名稱 | 是
description | 說明活動用途的文字 | 否
type | 必須設為 [SetVariable] | 是
value | 變數所指派至的字串常值或運算式物件值 | 是
variableName | 此活動所設定的變數名稱 | 是

## <a name="incrementing-a-variable"></a>遞增變數

牽涉到 Azure Data Factory 中變數的常見案例是在 until 或 foreach 活動內使用變數作為迭代器。 在設定變數活動中，您無法參考要在 `value` 欄位中設定的變數。 若要解決這項限制，請設定暫存變數，然後建立第二個設定變數活動。 第二個設定變數活動會將迭代器的值設定為暫存變數。 

此模式的範例如下：

![遞增變數](media/control-flow-set-variable-activity/increment-variable.png "遞增變數")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>後續步驟
深入了解 Data Factory 所支援的相關控制流程活動： 

- [附加變數活動](control-flow-append-variable-activity.md)
