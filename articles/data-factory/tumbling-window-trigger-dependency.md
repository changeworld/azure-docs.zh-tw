---
title: 創建翻滾視窗觸發器依賴項
description: 了解如何在 Azure Data Factory 中的輪轉視窗觸發程序上建立相依性。
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 0557c9b9eb65654c4a11c1389ace4776ab60a61d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532565"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>建立輪轉視窗觸發程序相依性

本文提供在輪轉視窗觸發程序上建立相依性的步驟。 如需輪轉視窗觸發程序的一般資訊，請參閱[如何建立輪轉視窗觸發程序](how-to-create-tumbling-window-trigger.md)。

若要建置相依性鏈結，並確保系統只會在成功執行 Data Factory 中的另一個觸發程序之後才執行某個觸發程序，請使用此進階功能來建立輪轉視窗相依性。

## <a name="create-a-dependency-in-the-data-factory-ui"></a>在 Data Factory UI 中建立相依性

若要在觸發程序上建立相依性，請選取 [觸發程序] > [進階] > [新增]****，然後選擇要進行相依的觸發程序，以及適當的偏移與大小。 選取 [完成]**** 並發佈 Data Factory 變更來使相依性生效。

![依賴項創建](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "依賴項創建")

## <a name="tumbling-window-dependency-properties"></a>輪轉視窗相依性屬性

具有依賴項的翻倒視窗觸發器具有以下屬性：

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

下表提供定義輪轉視窗相依性所需之屬性的清單。

| **屬性名稱** | **描述**  | **類型** | **必要** |
|---|---|---|---|
| type  | 所有現有的輪轉視窗觸發程序都會顯示在此下拉式清單中。 選擇要採取相依性的觸發程序。  | 翻滾視窗觸發器依賴引用或自依賴項視窗觸發器引用 | 是 |
| Offset | 相依性觸發程序的偏移。 以時間跨度格式提供值，允許負偏移和正偏移。 如果觸發器本身且在所有其他情況下都是可選的，則此屬性是必需的。 自我相依性應一律為負值偏移。 如果未指定值，則視窗與觸發器本身相同。 | Timespan<br/>(hh:mm:ss) | 自依賴：是<br/>其他： 否 |
| size | 相依性輪轉視窗的大小。 提供正時跨值。 這是選用屬性。 | Timespan<br/>(hh:mm:ss) | 否  |

> [!NOTE]
> 輪轉視窗觸發器可能最多取決於另外兩個觸發器。

## <a name="tumbling-window-self-dependency-properties"></a>輪轉視窗自我相依性屬性

在觸發器在成功完成上一個視窗之前不應轉到下一個視窗的情況下，生成自依賴關係。 依賴于前一 hr 中自身早期運行成功的自我依賴觸發器將具有以下屬性：

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>使用方案和示例

下面是翻倒視窗依賴項屬性的方案和使用方式的插圖。

### <a name="dependency-offset"></a>相依性偏移

![偏移示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "偏移示例")

### <a name="dependency-size"></a>相依性大小

![大小示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "大小示例")

### <a name="self-dependency"></a>自我相依性

![自依賴](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "自我相依性")

### <a name="dependency-on-another-tumbling-window-trigger"></a>另一個輪轉視窗觸發程序上的相依性

每日遙測處理作業，具體取決於另一個每日作業聚合過去七天的輸出並生成七天滾動視窗流：

![依賴項示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "依賴項示例")

### <a name="dependency-on-itself"></a>對其本身的相依性

作業輸出資料流中沒有間隔的每日作業：

![自依賴示例](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "自依賴示例")

有關如何使用翻滾視窗觸發器在 Azure 資料工廠創建從屬管道的演示，請觀看以下視頻：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>監視相依性

可以從觸發器運行監視頁監視依賴項鍊和相應的視窗。 請瀏覽到 [監視] > [觸發程序執行]****。 在操作列下，可以重新運行觸發器或查看其依賴項。

![監視觸發程序執行](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "監視觸發程序執行")

如果按一下"查看觸發器依賴項"，則可以看到依賴項的狀態。 如果其中一個依賴項觸發器失敗，則必須成功重新運行它，以便從屬觸發器運行。 翻滾視窗觸發器將在依賴項上等待七天，然後超時。

![監視相依性](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "監視相依性")

要查看觸發器依賴項計畫，請選擇"甘特"視圖。

![監視相依性](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "監視相依性")

## <a name="next-steps"></a>後續步驟

* 查看[如何創建翻滾視窗觸發器](how-to-create-tumbling-window-trigger.md)
