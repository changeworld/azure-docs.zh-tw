---
title: 監視複製活動
description: 瞭解如何在 Azure 資料工廠中監視複製活動執行。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 6494352bf957af83b45488493bf12a094c730c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79125754"
---
# <a name="monitor-copy-activity"></a>監視複製活動

本文概述了如何在 Azure 資料工廠中監視複製活動執行。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="monitor-visually"></a>以視覺化方式監視

在 Azure 資料工廠中創建和發佈管道後，可以將其與觸發器關聯，或手動啟動臨時運行。 您可以在 Azure 資料工廠使用者體驗中監視本機運行的所有管道。 從[視覺化監視 Azure 資料工廠](monitor-visually.md)瞭解 Azure 資料工廠監視。

要監視"複製"活動運行，請轉到資料工廠 **"作者&監視器**UI"。 在 **"監視器"** 選項卡上，您將看到管道運行的清單，按一下**管道名稱**連結以訪問管道運行中運行的活動清單。

![監視複製活動運行](./media/copy-activity-overview/monitor-pipeline-run.png)

在此級別，您可以看到用於複製活動輸入、輸出和錯誤（如果複製活動運行失敗）以及持續時間/狀態等統計資訊的連結。 按一下複製活動名稱旁邊的 **"詳細資訊**"按鈕（眼鏡）將為您提供有關複製活動執行的深層詳細資訊。 

![監視複製活動運行](./media/copy-activity-overview/monitor-copy-activity-run.png)

在此圖形監視視圖中，Azure 資料工廠會顯示覆制活動執行資訊，包括資料讀取/寫入卷、從源複製到接收器的檔/行資料數、輸送量、應用於複製方案的配置、複製活動經歷的步驟以及相應的持續時間和詳細資訊等。 有關每個可能的指標及其詳細說明，請參閱[此表](#monitor-programmatically)。 

在某些情況下，在資料工廠中運行複製活動時，您將在複製活動監視視圖的頂部看到 **"性能調優提示"，** 如示例所示。 這些提示告訴您 ADF 為特定複製運行確定的瓶頸，以及有關要更改哪些內容以提高複製輸送量的建議。 瞭解有關[自動性能調優提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)的更多。

底部**執行詳細資訊和持續時間**描述了複製活動執行的關鍵步驟，這對於解決複製性能的疑難問題特別有用。 複製運行的瓶頸是持續時間最長的瓶頸。 有關每個階段表示的內容和詳細的故障排除指南，請參閱["疑難排解"複製活動性能](copy-activity-performance-troubleshooting.md)。

**示例：從 Amazon S3 複製到 Azure 資料存儲單元 2**

![監視複製活動運行詳細資訊](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>以程式設計方式監視

複製活動執行詳細資訊和性能特徵也會在 **"複製活動運行結果** > **輸出"** 部分中返回，該部分用於呈現 UI 監視視圖。 下面是可能返回的屬性的完整清單。 您只能看到適用于複製方案的屬性。 有關如何監視活動通常以程式設計方式運行的資訊，請參閱[以程式設計方式監視 Azure 資料工廠](monitor-programmatically.md)。

| 屬性名稱  | 描述 | 輸出單位 |
|:--- |:--- |:--- |
| dataRead | 從源讀取的實際資料量。 | int64 值（以位元組為單位） |
| dataWritten | 寫入/提交到接收器上的資料的實際裝載量。 大小可能與`dataRead`大小不同，因為它涉及每個資料存儲存儲資料的方式。 | int64 值（以位元組為單位） |
| filesRead | 從基於檔的源讀取的檔數。 | Int64 值 (未指定單位) |
| filesWritten | 寫入/提交到基於檔的接收器的檔數。 | Int64 值 (未指定單位) |
| 源峰值連接 | 在"複製"活動運行期間建立到來源資料存儲的併發連接的高峰數。 | Int64 值 (未指定單位) |
| 接收器峰值連接 | 在"複製"活動運行期間建立到接收器資料存儲的併發連接的高峰數。 | Int64 值 (未指定單位) |
| 行讀取 | 從源讀取的行數（不適用於二進位副本）。 | Int64 值 (未指定單位) |
| rowsCopied | 複製到接收器的行數（不適用於二進位副本）。 | Int64 值 (未指定單位) |
| rowsSkipped | 跳過的不相容行數。 可以通過設置為`enableSkipIncompatibleRow`true 來啟用不相容的行。 | Int64 值 (未指定單位) |
| copyDuration | 複製運行的持續時間。 | Int32 值，以秒為單位 |
| throughput | 資料傳輸速率。 | 浮點數，以 KBps 表示 |
| 源峰值連接 | 在"複製"活動運行期間建立到來源資料存儲的併發連接的高峰數。 | Int32 值（無單位） |
| 接收器峰值連接| 在"複製"活動運行期間建立到接收器資料存儲的併發連接的高峰數。| Int32 值（無單位） |
| sqlDwPolyBase | 將資料複製到 SQL 資料倉儲時是否使用 PolyBase。 | Boolean |
| redshiftUnload | 從紅移複製資料時是否使用 UNLOAD。 | Boolean |
| hdfsDistcp | 從 HDFS 複製資料時是否使用 DistCp。 | Boolean |
| effectiveIntegrationRuntime | 用於為活動運行供電的集成運行時 （IR） 或運行時，格式`<IR name> (<region if it's Azure IR>)`為 。 | 文字 (字串) |
| usedDataIntegrationUnits | 複製期間的有效資料整合單位。 | Int32 值 |
| usedParallelCopies | 複製期間有效的 parallelCopies。 | Int32 值 |
| redirectRowPath | 在屬性中配置的 blob 存儲中跳過的不相容行的日誌`redirectIncompatibleRowSettings`的路徑。 請參閱[容錯](copy-activity-overview.md#fault-tolerance)。 | 文字 (字串) |
| executionDetails | 有關複製活動的各個階段以及相應的步驟、持續時間、配置等的更多詳細資訊。 我們不建議您分析此部分，因為它可能會更改。 要更好地瞭解它如何説明您理解和排除複製性能，請參閱[Monitor 視覺](#monitor-visually)部分。 | Array |
| 建議 | 複製性能調優技巧。 有關詳細資訊，請參閱[性能調優提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。 | Array |

**例子：**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

\-[複製活動概述](copy-activity-overview.md)

\-[複製活動性能](copy-activity-performance.md)