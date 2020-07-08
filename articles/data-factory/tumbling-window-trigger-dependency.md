---
title: 建立輪轉視窗觸發程式相依性
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
ms.openlocfilehash: 3b417e7c4589f3a4214400a877812d196a63349b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82870046"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>建立輪轉視窗觸發程序相依性
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供在輪轉視窗觸發程序上建立相依性的步驟。 如需輪轉視窗觸發程序的一般資訊，請參閱[如何建立輪轉視窗觸發程序](how-to-create-tumbling-window-trigger.md)。

若要建置相依性鏈結，並確保系統只會在成功執行 Data Factory 中的另一個觸發程序之後才執行某個觸發程序，請使用此進階功能來建立輪轉視窗相依性。

如需如何使用輪轉視窗觸發程式在您的 Azure Data Factory 中建立相依管線的示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>在 Data Factory UI 中建立相依性

若要在觸發程序上建立相依性，請選取 [觸發程序] > [進階] > [新增]****，然後選擇要進行相依的觸發程序，以及適當的偏移與大小。 選取 [完成]**** 並發佈 Data Factory 變更來使相依性生效。

![建立相依性](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "建立相依性")

## <a name="tumbling-window-dependency-properties"></a>輪轉視窗相依性屬性

具有相依性的輪轉視窗觸發程式具有下列屬性：

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

| **屬性名稱** | **說明**  | **型別** | **必要** |
|---|---|---|---|
| type  | 所有現有的輪轉視窗觸發程序都會顯示在此下拉式清單中。 選擇要採取相依性的觸發程序。  | TumblingWindowTriggerDependencyReference 或 SelfDependencyTumblingWindowTriggerReference | Yes |
| Offset | 相依性觸發程序的偏移。 請提供時間範圍格式的值，並允許負與正位移。 如果觸發程式是以其本身為依據，而且在其他所有情況下是選擇性的，則這個屬性是必要的。 自我相依性應一律為負值偏移。 如果未指定任何值，視窗會與觸發程式本身相同。 | Timespan<br/>(hh:mm:ss) | 自我相依性：是<br/>其他：否 |
| 大小 | 相依性輪轉視窗的大小。 提供正的 timespan 值。 這是選用屬性。 | Timespan<br/>(hh:mm:ss) | No  |

> [!NOTE]
> 輪轉視窗觸發程式可能會相依于最多五個其他觸發程式。

## <a name="tumbling-window-self-dependency-properties"></a>輪轉視窗自我相依性屬性

在觸發程式不應繼續到下一個視窗，直到上一個視窗成功完成為止，請建立自我相依性。 依賴先前一小時內的成功執行的自我相依性觸發程式，將會有下列程式碼中所示的屬性。

> [!NOTE]
> 如果您的觸發管線依賴先前觸發的 windows 中的管線輸出，我們建議您只使用輪轉視窗觸發程式自我相依性。 若要限制平行觸發程式的執行，請設定上限觸發程式並行。

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
## <a name="usage-scenarios-and-examples"></a>使用案例和範例

以下是輪轉視窗相依性屬性的案例和使用方式圖例。

### <a name="dependency-offset"></a>相依性偏移

![Offset 範例](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Offset 範例")

### <a name="dependency-size"></a>相依性大小

![大小範例](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "大小範例")

### <a name="self-dependency"></a>自我相依性

![自我相依性](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "自我相依性")

### <a name="dependency-on-another-tumbling-window-trigger"></a>另一個輪轉視窗觸發程序上的相依性

每日遙測處理工作，取決於匯總過去七天輸出的另一個日常作業，並產生七天的輪流時段串流：

![相依性範例](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "相依性範例")

### <a name="dependency-on-itself"></a>對其本身的相依性

作業輸出資料流中沒有間隔的每日作業：

![自我相依性範例](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "自我相依性範例")

## <a name="monitor-dependencies"></a>監視相依性

您可以從 [觸發程式執行監視] 頁面監視相依性鏈和對應的視窗。 請瀏覽到 [監視] > [觸發程序執行]****。 在 [動作] 資料行下，您可以重新執行觸發程式或查看其相依性。

![監視觸發程序執行](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "監視觸發程序執行")

如果您按一下 [查看觸發程式相依性]，就可以看到相依性的狀態。 如果其中一個相依性觸發程式失敗，您必須成功地重新執行它，才能讓 dependency 觸發程式執行。 輪轉視窗觸發程式會在超時前的七天等待相依性。

![監視相依性](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "監視相依性")

如需更多視覺效果以查看觸發程式相依性排程，請選取 [甘特圖] 視圖。

![監視相依性](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "監視相依性")

## <a name="next-steps"></a>後續步驟

* 回顧[如何建立輪轉視窗觸發](how-to-create-tumbling-window-trigger.md)程式
