---
title: 資料流程活動
description: 如何從資料工廠管道內部執行資料流程。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463045"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure 資料工廠中的資料流程活動

使用資料流程活動通過映射資料流程轉換和移動資料。 如果您是資料流程的新人，請參閱[映射資料流程概述](concepts-data-flow-overview.md)

## <a name="syntax"></a>語法

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
資料流程 | 對正在執行的資料流程的引用 | 資料流程參考 | 是
集成執行時間 | 資料流程運行的計算環境。 如果未指定，將使用自動解析 Azure 集成運行時 | 集成運行時參考 | 否
計算.核心計數 | 火花簇中使用的內核數。 僅當使用自動解析 Azure 集成運行時時才能指定 | 8, 16, 32, 48, 80, 144, 272 | 否
計算.計算類型 | 火花群集中使用的計算類型。 僅當使用自動解析 Azure 集成運行時時才能指定 | "常規"、"計算優化"、"記憶體優化" | 否
暫存.連結服務 | 如果您使用的是 SQL DW 源或接收器，則用於 PolyBase 暫存的存儲帳戶 | 連結服務參考 | 僅當資料流程讀取或寫入 SQL DW 時
暫存.資料夾路徑 | 如果使用 SQL DW 源或接收器，則 Blob 存儲帳戶中的資料夾路徑用於 PolyBase 暫存 | String | 僅當資料流程讀取或寫入 SQL DW 時

![執行資料流程](media/data-flow/activity-data-flow.png "執行資料流程")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>運行時動態大小資料流程計算

可以動態設置核心計數和計算類型屬性，以根據運行時傳入來源資料的大小進行調整。 使用管道活動（如查找或獲取中繼資料）來查找源資料集資料的大小。 然後，在資料流程活動屬性中使用"添加動態內容"。

![動態資料流](media/data-flow/dyna1.png "動態資料流")

[這裡是一個簡短的視頻教程解釋這種技術](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>資料流程集成運行時

選擇要用於資料流程活動執行的集成運行時。 預設情況下，資料工廠將使用具有四個工作核心且沒有時間生存 （TTL） 的自動解析 Azure 集成運行時。 此 IR 具有通用計算類型，並且與工廠在同一區域運行。 您可以創建自己的 Azure 集成運行時，用於定義特定區域、計算類型、核心計數和 TTL 以執行資料流程活動。

對於管道執行，群集是作業群集，在執行啟動前需要幾分鐘時間啟動。 如果未指定 TTL，則每次管道運行都需要此啟動時間。 如果指定 TTL，則暖群集池將在上次執行後指定的時間內保持活動狀態，從而導致啟動時間縮短。 例如，如果 TTL 為 60 分鐘，並且每小時在它上運行一次資料流程，群集池將保持活動狀態。 有關詳細資訊，請參閱[Azure 集成運行時](concepts-integration-runtime.md)。

![Azure 集成運行時](media/data-flow/ir-new.png "Azure 集成運行時")

> [!NOTE]
> 資料流程活動中的集成運行時選擇僅適用于管道的*觸發執行*。 使用資料流程調試管道，在調試會話中指定的群集上運行。

### <a name="polybase"></a>PolyBase

如果將 Azure SQL 資料倉儲用作接收器或源，則必須為 PolyBase 批次處理負載選擇暫存位置。 PolyBase 允許批量批量載入批次處理，而不是逐行載入資料。 PolyBase 大大減少了 SQL DW 的載入時間。

## <a name="parameterizing-data-flows"></a>參數化資料流程

### <a name="parameterized-datasets"></a>參數化資料集

如果資料流程使用參數化資料集，請在 **"設置"** 選項卡中設置參數值。

![執行資料流程參數](media/data-flow/params.png "參數")

### <a name="parameterized-data-flows"></a>參數化資料流程

如果資料流程已參數化，請在 **"參數"** 選項卡中設置資料流程參數的動態值。您可以使用 ADF 管道運算式語言（僅適用于字串類型）或資料流程運算式語言來分配動態或文本參數值。 有關詳細資訊，請參閱[資料流程參數](parameters-data-flow.md)。

![執行資料流程參數示例](media/data-flow/parameter-example.png "參數範例")

### <a name="parameterized-compute-properties"></a>參數化計算屬性。

如果使用自動解析 Azure 集成運行時並指定計算值，則可以對核心計數或計算類型進行參數化。

![執行資料流程參數示例](media/data-flow/parameterize-compute.png "參數範例")

## <a name="pipeline-debug-of-data-flow-activity"></a>資料流程活動的管道調試

要執行使用資料流程活動運行的調試管道，必須通過頂部欄上**的資料流程調試**滑塊打開資料流程偵錯模式。 偵錯模式允許您針對活動 Spark 群集運行資料流程。 有關詳細資訊，請參閱[偵錯模式](concepts-data-flow-debug-mode.md)。

![偵錯按鈕](media/data-flow/debugbutton.png "偵錯按鈕")

調試管道針對活動調試群集運行，而不是資料流程活動設置中指定的集成運行時環境。 啟動偵錯模式時，可以選擇調試計算環境。

## <a name="monitoring-the-data-flow-activity"></a>監視資料流程活動

資料流程活動具有特殊的監視體驗，您可以在其中查看分區、階段時間和資料血統資訊。 通過 **"操作"** 下的眼鏡圖示打開監控窗格。 有關詳細資訊，請參閱[監視資料流程](concepts-data-flow-monitoring.md)。

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>使用資料流程活動會導致後續活動

資料流程活動輸出有關寫入每個接收器的行數和從每個源讀取的行數的指標。 這些結果在活動運行結果的部分`output`中返回。 返回的指標採用以下 json 的格式。

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

例如，要獲取在名為"資料流程活動"的活動中寫入名為"sink1"的接收器的行數，請使用`@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`。

要獲取從該接收器中使用的名為"source1"的源讀取的行數，請使用`@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`。

> [!NOTE]
> 如果接收器的行為零，則不會顯示在指標中。 可以使用 函數`contains`驗證存在。 例如，`contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')`將檢查是否將任何行寫入 sink1。

## <a name="next-steps"></a>後續步驟

請參閱資料工廠支援的控制流活動： 

- [If 條件活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [對於每個活動](control-flow-for-each-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
