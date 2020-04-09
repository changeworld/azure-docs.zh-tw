---
title: Azure Data Factory 中的設定變數活動
description: 了解如何使用「設定變數」活動來設定在 Data Factory 管線中定義的現有變數值
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879255"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>在 Azure 資料工廠中設定變數活動

使用「設定變數」活動，為定義於 Data Factory 管線中的「字串」、「布林」或「陣列」類型設定現有變數值。

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 必要
-------- | ----------- | --------
NAME | 管線中的活動名稱 | 是
description | 說明活動用途的文字 | 否
type | 必須設置為 **"設置變數"** | 是
value | 將變數配置給的字串文字或表示式物件值值 | 是
variableName | 此活動所將設定的變數名稱 | 是

## <a name="incrementing-a-variable"></a>增加變數

Azure 資料工廠中涉及變數的常見方案是在每個活動之前或每個活動中使用變數作為反覆運算器。 在集變數活動中,不能引用欄位中正在設置的`value`變數。 要解決此限制,請設置臨時變數,然後創建第二個集變數活動。 第二組變數活動將反覆運算器的值設置為臨時變數。 

下面是此模式的範例:

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
