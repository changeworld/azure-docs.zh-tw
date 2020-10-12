---
title: 監視複製活動
description: 瞭解如何在 Azure Data Factory 中監視複製活動的執行。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: 92119709aa260f3180c503a77064f6e80dece6e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440604"
---
# <a name="monitor-copy-activity"></a>監視複製活動

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何在 Azure Data Factory 中監視複製活動的執行。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="monitor-visually"></a>以視覺化方式監視

一旦在 Azure Data Factory 中建立並發佈管道後，您就可以將其與觸發程序建立關聯，或手動啟動特定回合。 您可以在 Azure Data Factory 使用者體驗中以原生方式監視所有管線回合。 從 [視覺化的監視 Azure Data Factory](monitor-visually.md)瞭解一般 Azure Data Factory 監視。

若要監視複製活動執行，請移至您的 data factory **作者 & 監視器** UI。 在 [ **監視** ] 索引標籤上，您會看到管線執行的清單，按一下 [ **管線名稱** ] 連結可存取管線執行中的活動執行清單。

![監視管線執行](./media/copy-activity-overview/monitor-pipeline-run.png)

在此層級中，您可以看到複製活動輸入、輸出和錯誤的連結 (如果複製活動執行失敗) ，以及持續時間/狀態之類的統計資料。 按一下 [複製活動名稱] 旁邊的 [ **詳細資料** ] 按鈕 (眼鏡) ，可讓您深入瞭解複製活動的執行。 

![監視複製活動執行](./media/copy-activity-overview/monitor-copy-activity-run.png)

在此圖形化監視視圖中，Azure Data Factory 會顯示覆制活動執行資訊，包括資料讀取/寫入磁片區、從來源複製到接收的檔案/資料列數目、輸送量、複製案例所套用的設定、複製活動的步驟，以及對應的持續時間和詳細資料等等。 請參閱 [此表格](#monitor-programmatically) ，以瞭解每個可能的度量及其詳細描述。 

在某些情況下，當您在 Data Factory 中執行複製活動時，您會在複製活動監視視圖的頂端看到「 **效能微調秘訣**  」，如範例所示。 提示會告訴您 ADF 針對特定的複製執行所識別的瓶頸，以及如何變更以提升複製輸送量的建議。 深入瞭解 [自動效能微調秘訣](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。

底部的 **執行詳細資料和持續** 時間描述複製活動所經歷的主要步驟，這對於疑難排解複製效能特別有用。 複製執行的瓶頸是具有最長持續時間的瓶頸。 請參閱針對每個階段所代表的 [複製活動效能進行疑難排解](copy-activity-performance-troubleshooting.md) ，以及詳細的疑難排解指引。

**範例：從 Amazon S3 複製到 Azure Data Lake Storage Gen2**

![監視複製活動執行詳細資料](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>以程式設計方式監視

複製活動執行詳細資料和效能特性也會在 [**複製活動執行結果**  >  **輸出**] 區段中傳回，這是用來呈現 UI 監視視圖。 以下是可能會傳回的完整屬性清單。 您只會看到適用于您的複製案例的屬性。 如需如何以程式設計方式監視活動執行的詳細資訊，請參閱以程式設計 [方式監視 Azure data factory](monitor-programmatically.md)。

| 屬性名稱  | 描述 | 輸出中的單位 |
|:--- |:--- |:--- |
| dataRead | 從來源讀取的實際資料量。 | Int64 值（以位元組為單位） |
| dataWritten | 寫入/認可至接收的資料實際裝載。 大小可能與 `dataRead` 大小不同，因為它會關聯每個資料存放區儲存資料的方式。 | Int64 值（以位元組為單位） |
| filesRead | 從檔案型來源讀取的檔案數目。 | Int64 值 (未指定單位) |
| filesWritten | 寫入/認可至以檔案為基礎之接收的檔案數目。 | Int64 值 (未指定單位) |
| filesSkipped | 從以檔案為基礎的來源略過的檔案數目。 | Int64 值 (未指定單位) |
| dataConsistencyVerification | 資料一致性驗證的詳細資料，您可以在其中查看是否已確認複製的資料在來源與目的地存放區之間是一致的。 若要深入瞭解，請參閱 [這篇文章](copy-activity-data-consistency.md#monitoring)。 | Array |
| sourcePeakConnections | 在複製活動執行期間，建立至來源資料存放區的並行連線尖峰數目。 | Int64 值 (未指定單位) |
| sinkPeakConnections | 在複製活動執行期間，建立至接收資料存放區的並行連線尖峰數量。 | Int64 值 (未指定單位) |
| rowsRead | 從來源讀取的資料列數目。 當您以原樣複製檔案而不剖析它們，例如，當來源和接收資料集是二進位格式類型，或其他具有相同設定的格式類型時，就不適用此度量。 | Int64 值 (未指定單位) |
| rowsCopied | 複製到接收的資料列數目。 當您以原樣複製檔案而不剖析它們，例如，當來源和接收資料集是二進位格式類型，或其他具有相同設定的格式類型時，就不適用此度量。  | Int64 值 (未指定單位) |
| rowsSkipped | 略過的不相容資料列數。 您可以設定為 true 來啟用略過不相容的資料列 `enableSkipIncompatibleRow` 。 | Int64 值 (未指定單位) |
| copyDuration | 複製執行的持續時間。 | Int32 值（以秒為單位） |
| throughput | 資料傳輸的速率。 | 浮點數（以 KBps 為單位） |
| sourcePeakConnections | 在複製活動執行期間，建立至來源資料存放區的並行連線尖峰數目。 | Int32 值 (沒有單位)  |
| sinkPeakConnections| 在複製活動執行期間，建立至接收資料存放區的並行連線尖峰數量。| Int32 值 (沒有單位)  |
| sqlDwPolyBase | 當資料複製到 Azure Synapse Analytics (先前的 SQL 資料倉儲) 時，是否使用 PolyBase。 | 布林值 |
| redshiftUnload | 從 Redshift 複製資料時，是否使用 UNLOAD。 | 布林值 |
| hdfsDistcp | 從 HDFS 複製資料時是否使用 DistCp。 | 布林值 |
| effectiveIntegrationRuntime | 整合執行時間 (IR) 或用來開啟活動執行功能的執行時間（格式為） `<IR name> (<region if it's Azure IR>)` 。 | 文字 (字串) |
| usedDataIntegrationUnits | 複製期間的有效資料整合單位。 | Int32 值 |
| usedParallelCopies | 複製期間有效的 parallelCopies。 | Int32 值 |
| logPath | Blob 儲存體中已略過資料之會話記錄的路徑。 請參閱 [容錯](copy-activity-overview.md#fault-tolerance)。 | 文字 (字串) |
| executionDetails | 複製活動所經歷之階段的詳細資料，以及對應的步驟、持續時間、設定等等。 我們不建議您剖析此區段，因為它可能會變更。 若要進一步瞭解其如何協助您瞭解複製效能並進行疑難排解，請參閱「 [視覺化](#monitor-visually) 」區段。 | Array |
| perfRecommendation | 複製效能微調秘訣。 如需詳細資訊，請參閱 [效能微調秘訣](copy-activity-performance-troubleshooting.md#performance-tuning-tips) 。 | Array |
| billingReference | 給定執行的計費耗用量。 從 [活動-執行層級的監視耗用量](plan-manage-costs.md#monitor-consumption-at-activity-run-level)中深入瞭解。 | Object |
| durationInQueue | 在複製活動開始執行之前，排入佇列的持續時間（秒）。 | Object |

**範例︰**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
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

\- [複製活動概觀](copy-activity-overview.md)

\- [複製活動效能](copy-activity-performance.md)