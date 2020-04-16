---
title: 資料流程活動
description: 如何從數據工廠管道內部執行數據流。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 32088dd712cd0c70fc01de48add17a0b6a828dc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415321"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure 資料工廠中的資料串流活動

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用數據流活動通過映射數據流轉換和移動數據。 如果您是資料串流的新人,請參閱[映射資料串流概述](concepts-data-flow-overview.md)

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
資料流程 | 正在執行的資料串流的參考 | 資料流參考 | 是
整合時間 | 數據流運行的計算環境。 沒有未指定,會使用自動解析 Azure 整合時 | 整合執行時參考 | 否
計算.核心計數 | 火花簇中使用的內核數。 只當使用自動解析 Azure 整合時時才能指定 | 8, 16, 32, 48, 80, 144, 272 | 否
計算.計算類型 | 火花群集中使用的計算類型。 只當使用自動解析 Azure 整合時時才能指定 | "常規"、"計算優化"、"記憶體優化" | 否
暫存連結服務 | 如果您使用的是 SQL DW 源或接收器,則用於 PolyBase 暫存的儲存帳戶 | 連結服務參考 | 只讀取或寫入 SQL DW 時
暫存.資料夾路徑 | 如果使用 SQL DW 源或接收器,則 Blob 儲存帳戶中的資料夾路徑用於 PolyBase 暫存 | String | 只讀取或寫入 SQL DW 時

![執行資料串流](media/data-flow/activity-data-flow.png "執行資料串流")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>執行時動態大小資料流程

可以動態設置核心計數和計算類型屬性,以根據運行時傳入源數據的大小進行調整。 使用導管活動(如查找或獲取中繼資料)來查找源資料集資料的大小。 然後,在數據流活動屬性中使用"添加動態內容"

![動態資料流程](media/data-flow/dyna1.png "動態資料流程")

[這裡是一個簡短的視頻教程解釋這種技術](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>資料流程整合執行時

選擇要用於資料流活動執行的整合式執行時。 默認情況下,資料工廠將使用具有四個工作核心且沒有時間生存 (TTL) 的自動解析 Azure 集成運行時。 此 IR 具有通用計算類型,並且與工廠在同一區域運行。 您可以創建自己的 Azure 整合執行時,用於定義特定區域、計算類型、核心計數和 TTL 以執行資料串流活動。

對於管道執行,群集是作業群集,在執行啟動前需要幾分鐘時間啟動。 如果未指定 TTL,則每次管道運行都需要此啟動時間。 如果指定 TTL,則暖群集池將在上次執行後指定的時間內保持活動狀態,從而導致啟動時間縮短。 例如,如果 TTL 為 60 分鐘,並且每小時在它上運行一次數據流,群集池將保持活動狀態。 有關詳細資訊,請參閱[Azure 整合時](concepts-integration-runtime.md)。

![Azure 整合時](media/data-flow/ir-new.png "Azure 整合時")

> [!NOTE]
> 資料串流活動中的整合執行時選擇僅適用於導管的*觸發執行*。 使用數據流調試管道,在調試會話中指定的群集上運行。

### <a name="polybase"></a>PolyBase

如果將 Azure SQL 資料倉庫用作接收器或源,則必須為 PolyBase 批次處理負載選擇暫存位置。 PolyBase 允許批量批量載入批處理,而不是逐行載入數據。 PolyBase 大大減少了 SQL DW 的載入時間。

## <a name="parameterizing-data-flows"></a>參數化資料流程

### <a name="parameterized-datasets"></a>參數化資料集

如果資料串流使用參數化資料集,請在 **「設定」** 選項卡中設定參數值。

![執行資料串流參數](media/data-flow/params.png "參數")

### <a name="parameterized-data-flows"></a>參數化資料流程

如果資料串流已參數化,請在 **「參數」** 選項卡中設定資料流參數的動態值。您可以使用 ADF 管道表示式語言(僅適用於字串類型)或資料流運算式語言來分配動態或文字參數值。 有關詳細資訊,請參考[資料串流參數](parameters-data-flow.md)。

![執行資料串流參數範例](media/data-flow/parameter-example.png "參數範例")

### <a name="parameterized-compute-properties"></a>參數化計算屬性。

如果使用自動解析 Azure 整合執行時並指定計算值,則可以對核心計數或計算類型進行參數化。

![執行資料串流參數範例](media/data-flow/parameterize-compute.png "參數範例")

## <a name="pipeline-debug-of-data-flow-activity"></a>資料流程活動的導管除錯

要執行使用數據流活動運行的調試管道,必須通過頂部欄上**的數據流調試**滑塊打開數據流調試模式。 調試模式允許您針對活動 Spark 群集運行數據流。 有關詳細資訊,請參閱[除錯模式](concepts-data-flow-debug-mode.md)。

![偵錯按鈕](media/data-flow/debugbutton.png "偵錯按鈕")

調試管道針對活動調試群集運行,而不是數據流活動設置中指定的集成運行時環境。 啟動除錯模式時,可以選擇除錯計算環境。

## <a name="monitoring-the-data-flow-activity"></a>監視資料流程活動

資料流活動具有特殊的監視體驗,您可以在其中查看分區、階段時間和數據血統資訊。 通過 **「操作」** 下的眼鏡圖示打開監控窗格。 有關詳細資訊,請參閱[監察資料串](concepts-data-flow-monitoring.md)流 。

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>使用資料串流活動會導致後續活動

數據流活動輸出有關寫入每個接收器的行數和從每個源讀取的行數的指標。 這些結果在活動運行結果的部分`output`中返回。 返回的指標採用以下 json 的格式。

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

例如,要取得在名為「資料串流活動」的活動中寫入名為「sink1」的接收器的行數,請`@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`使用 。

要取得從該接收器中使用的名為 'source1'的來源讀取的行數,請`@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`使用 。

> [!NOTE]
> 如果接收器的行為零,則不會顯示在指標中。 可以使用函數`contains`驗證存在。 例如,`contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')`將檢查是否將任何行寫入 sink1。

## <a name="next-steps"></a>後續步驟

請參考資料工廠支援的控制串流活動: 

- [If 條件活動](control-flow-if-condition-activity.md)
- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [網路活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
