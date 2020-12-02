---
title: Azure Data Factory 中的 Wait 活動
description: Wait (等待) 活動會讓管線執行暫停一段指定的時間。
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 12c9027067c00a3db84e6610a0d73090cc011713
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485904"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>在 Azure Data Factory 中執行等候活動
在管線中使用 Wait (等待) 活動時，管線便會等待一段指定的時間，然後再繼續執行後續的活動。 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>語法

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
NAME | `Wait` 活動的名稱。 | String | 是
type | 必須設為 **Wait**。 | String | 是
waitTimeInSeconds | 管線繼續進行處理之前所等待的秒數。 | 整數 | 是

## <a name="example"></a>範例

> [!NOTE]
> 本節提供 JSON 定義和 PowerShell 命令範例，以供執行管線。 針對使用 Azure PowerShell 和 JSON 定義來建立 Data Factory 管線，如需包含逐步指示的逐步解說，請參閱[教學課程：使用 Azure PowerShell 來建立資料處理站](quickstart-create-data-factory-powershell.md)。

### <a name="pipeline-with-wait-activity"></a>具有 Wait 活動的管線
在此範例中，管線有兩個活動：**Until** 與 **Wait**。 Wait (等待) 活動設定為等待一秒鐘。 管線是在迴圈中執行 Wait (等待) 活動，每次執行之間會等待一秒鐘。 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [If 條件活動](control-flow-if-condition-activity.md)
- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
