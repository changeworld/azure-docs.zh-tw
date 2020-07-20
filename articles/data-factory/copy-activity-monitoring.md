---
title: 監視複製活動
description: 瞭解如何在 Azure Data Factory 中監視複製活動執行。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: jingwang
ms.openlocfilehash: 4e7828810a069756d1a0cde55ab47915ad11acc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85249681"
---
# <a name="monitor-copy-activity"></a>監視複製活動

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何在 Azure Data Factory 中監視複製活動執行。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="monitor-visually"></a>以視覺化方式監視

一旦在 Azure Data Factory 中建立並發佈管道後，您就可以將其與觸發程序建立關聯，或手動啟動特定回合。 您可以在 Azure Data Factory 使用者體驗中以原生方式監視所有管線回合。 從[視覺化的監視 Azure Data Factory](monitor-visually.md)瞭解一般 Azure Data Factory 監視。

若要監視複製活動執行，請前往您的 data factory**作者 & 監視器**UI。 在 [**監視器**] 索引標籤上，您會看到一份管線執行清單，按一下 [**管線名稱**] 連結以存取管線執行中的活動執行清單。

![監視複製活動執行](./media/copy-activity-overview/monitor-pipeline-run.png)

在此層級中，您可以看到複製活動輸入、輸出和錯誤的連結（如果複製活動執行失敗），以及「持續時間/狀態」之類的統計資料。 按一下複製活動名稱旁邊的**詳細資料**按鈕（眼鏡），可讓您深入瞭解複製活動的執行。 

![監視複製活動執行](./media/copy-activity-overview/monitor-copy-activity-run.png)

在此圖形化監視視圖中，Azure Data Factory 會提供複製活動執行資訊，包括資料讀取/寫入的磁片區、從來源複製到接收的資料的檔案/列數目、輸送量、複製案例所套用的設定、複製活動透過對應的持續時間和詳細資料進行的步驟等等。 請參閱[此表格](#monitor-programmatically)中每個可能的計量及其詳細描述。 

在某些情況下，當您在 Data Factory 中執行複製活動時，您會在 [複製活動監視] 視圖頂端看到「**效能調整秘訣**」，如範例中所示。 這些秘訣會告訴您 ADF 針對特定複本執行所識別的瓶頸，並提供要變更哪些內容來提升複製輸送量的建議。 深入瞭解[自動效能微調提示](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。

底部的**執行詳細資料和持續**時間說明複製活動所經歷的重要步驟，這對於疑難排解複製效能特別有用。 複製執行的瓶頸是持續時間最長的一個。 請參閱針對每個階段所代表的內容[進行複製活動效能疑難排解](copy-activity-performance-troubleshooting.md)和詳細的疑難排解指引。

**範例：從 Amazon S3 複製到 Azure Data Lake Storage Gen2**

![監視複製活動執行詳細資料](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>以程式設計方式監視

複製活動執行詳細資料和效能特性也會在 [**複製活動執行結果**  >  **輸出**] 區段中傳回，用來呈現 UI 監視視圖。 以下是可能會傳回的完整屬性清單。 您只會看到適用于您的複製案例的屬性。 如需如何以程式設計方式監視活動執行的詳細資訊，請參閱以程式設計[方式監視 Azure data factory](monitor-programmatically.md)。

| 屬性名稱  | 描述 | 輸出中的單位 |
|:--- |:--- |:--- |
| dataRead | 從來源讀取的實際資料量。 | Int64 值（以位元組為單位） |
| dataWritten | 實際裝載寫入/認可至接收的資料。 大小可能會與大小不同 `dataRead` ，因為它會與每個資料存放區儲存資料的方式產生關聯。 | Int64 值（以位元組為單位） |
| filesRead | 從以檔案為基礎的來源讀取的檔案數目。 | Int64 值 (未指定單位) |
| filesWritten | 以檔案為基礎的接收寫入/認可的檔案數目。 | Int64 值 (未指定單位) |
| sourcePeakConnections | 在複製活動執行期間，建立到來源資料存放區的並行連線尖峰數目。 | Int64 值 (未指定單位) |
| sinkPeakConnections | 在複製活動執行期間，建立至接收資料存放區的並行連線尖峰數目。 | Int64 值 (未指定單位) |
| rowsRead | 從來源讀取的資料列數目。 當您就地複製檔案但未剖析時，就不會套用此計量，例如，當來源和接收資料集是二進位格式類型，或其他具有相同設定的格式類型時。 | Int64 值 (未指定單位) |
| rowsCopied | 複製到接收的資料列數目。 當您就地複製檔案但未剖析時，就不會套用此計量，例如，當來源和接收資料集是二進位格式類型，或其他具有相同設定的格式類型時。  | Int64 值 (未指定單位) |
| rowsSkipped | 略過的不相容資料列數目。 您可以將設定為 true，以啟用略過不相容的資料列 `enableSkipIncompatibleRow` 。 | Int64 值 (未指定單位) |
| copyDuration | 複製執行的持續時間。 | Int32 值（以秒為單位） |
| throughput | 資料傳輸速率。 | 浮點數字，以 KBps 為單位 |
| sourcePeakConnections | 在複製活動執行期間，建立到來源資料存放區的並行連線尖峰數目。 | Int32 值（沒有單位） |
| sinkPeakConnections| 在複製活動執行期間，建立至接收資料存放區的並行連線尖峰數目。| Int32 值（沒有單位） |
| sqlDwPolyBase | 將資料複製到 SQL 資料倉儲時是否使用 PolyBase。 | Boolean |
| redshiftUnload | 從 Redshift 複製資料時，是否使用 UNLOAD。 | Boolean |
| hdfsDistcp | 從 HDFS 複製資料時是否使用 DistCp。 | Boolean |
| effectiveIntegrationRuntime | 用來為活動執行供電的整合執行時間（IR）或執行時間（格式為） `<IR name> (<region if it's Azure IR>)` 。 | 文字 (字串) |
| usedDataIntegrationUnits | 複製期間的有效資料整合單位。 | Int32 值 |
| usedParallelCopies | 複製期間有效的 parallelCopies。 | Int32 值 |
| redirectRowPath | 您在屬性中設定的 blob 儲存體中，略過不相容資料列的記錄檔路徑 `redirectIncompatibleRowSettings` 。 請參閱[容錯](copy-activity-overview.md#fault-tolerance)。 | 文字 (字串) |
| executionDetails | 複製活動所經歷之階段的詳細資料，以及對應的步驟、持續時間、設定等等。 我們不建議您剖析此區段，因為它可能會變更。 若要進一步瞭解它如何協助您瞭解和疑難排解複製效能，請參閱以[視覺化方式監視](#monitor-visually)一節。 | Array |
| perfRecommendation | 複製效能微調秘訣。 如需詳細資訊，請參閱[效能微調秘訣](copy-activity-performance-troubleshooting.md#performance-tuning-tips)。 | Array |

**範例︰**

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

\- [複製活動概觀](copy-activity-overview.md)

\- [複製活動效能](copy-activity-performance.md)