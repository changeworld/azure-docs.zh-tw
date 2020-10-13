---
title: Azure Data Factory 中的複製活動
description: 瞭解 Azure Data Factory 中的複製活動。 您可以使用它將資料從支援的來源資料存放區複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: 5eade0ad48dcdd1f0c18ef6e65e498a7b9c79c15
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951670"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure Data Factory 中的複製活動

> [!div class="op_single_selector" title1="選取您要使用的 Data Factory 版本："]
> * [第 1 版](v1/data-factory-data-movement-activities.md)
> * [目前的版本](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 Azure Data Factory 中，您可以使用複製活動，在內部部署和雲端中的資料存放區之間複製資料。 複製資料之後，您可以使用其他活動來進一步轉換和分析資料。 您也可以使用「複製活動」來發行商業智慧 (BI) 和應用程式取用的轉換和分析結果。

![複製活動的角色](media/copy-activity-overview/copy-activity.png)

複製活動會在 [整合運行](concepts-integration-runtime.md)時間上執行。 您可以針對不同的資料複製案例使用不同類型的整合執行時間：

* 當您在兩個可透過網際網路從任何 IP 公開存取的資料存放區之間複製資料時，您可以使用「複製活動」的 Azure integration runtime。 此整合執行時間為安全、可靠、可調整且 [可全域使用](concepts-integration-runtime.md#integration-runtime-location)。
* 當您將資料複製到內部部署的資料存放區，或從位於內部部署或具有存取控制 (（例如 Azure 虛擬網路) ）的資料存放區來回複製資料時，您需要設定自我裝載整合執行時間。

整合執行時間必須與每個來源和接收資料存放區相關聯。 如需複製活動如何判斷要使用哪一個整合執行時間的相關資訊，請參閱 [判斷要使用哪個 IR](concepts-integration-runtime.md#determining-which-ir-to-use)。

若要將資料從來源複製到接收，執行複製活動的服務會執行這些步驟：

1. 從來源資料存放區讀取資料。
2. 執行序列化/還原序列化、壓縮/解壓縮、資料行對應等等。 它會根據輸入資料集、輸出資料集和複製活動的設定來執行這些作業。
3. 將資料寫入接收/目的地資料存放區。

![複製活動概觀](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>支援的資料存放區和格式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>支援的檔案格式

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

您可以使用複製活動，在兩個以檔案為基礎的資料存放區之間依原樣複製檔案，在這種情況下，資料會在沒有任何序列化或還原序列化的情況下有效率地複製。 此外，您也可以剖析或產生特定格式的檔案，例如，您可以執行下列動作：

* 從 SQL Server 資料庫複製資料，並以 Parquet 格式寫入 Azure Data Lake Storage Gen2。
* 從內部部署檔案系統複製文字 (CSV) 格式的檔案，並以 Avro 格式寫入 Azure Blob 儲存體。
* 從內部部署檔案系統複製 zip 壓縮檔案，並將其解壓縮，然後將解壓縮的檔案寫入 Azure Data Lake Storage Gen2。
* 從 Azure Blob 儲存體中複製 Gzip 壓縮文字 (CSV) 格式的資料，並將其寫入 Azure SQL Database。
* 許多需要序列化/還原序列化或壓縮/解壓縮的活動。

## <a name="supported-regions"></a>支援區域

啟用複製活動的服務可在 [Azure integration runtime 位置](concepts-integration-runtime.md#integration-runtime-location)中所列的區域和地理位置全域取得。 全域可用的拓撲可確保進行有效率的資料移動，通常可避免發生跨區域躍點的情況。 查看各 [區域的產品](https://azure.microsoft.com/regions/#services) ，以檢查特定區域中 Data Factory 和資料移動的可用性。

## <a name="configuration"></a>組態

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

一般而言，若要在 Azure Data Factory 中使用複製活動，您需要：

1. **建立來源資料存放區和接收資料存放區的連結服務。** 您可以在本文的 [支援的資料存放區和格式](#supported-data-stores-and-formats) 一節中找到支援的連接器清單。 如需設定資訊和支援的屬性，請參閱連接器文章的「連結的服務屬性」一節。 
2. **建立來源和接收的資料集。** 如需設定資訊和支援的屬性，請參閱來源和接收連接器文章的「資料集屬性」章節。
3. **建立具有複製活動的管線。** 下一節提供範例。

### <a name="syntax"></a>語法

下列複製活動範本包含支援屬性的完整清單。 請指定適合您案例的屬性。

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>語法詳細資料

| 屬性 | 描述 | 必要？ |
|:--- |:--- |:--- |
| 型別 | 若為複製活動，請將設定為 `Copy` | 是 |
| 輸入 | 指定您所建立的資料集，以指向來源資料。 複製活動僅支援單一輸入。 | 是 |
| 輸出 | 指定您所建立指向接收資料的資料集。 複製活動僅支援單一輸出。 | 是 |
| typeProperties | 指定要設定複製活動的屬性。 | 是 |
| source | 指定用於抓取資料的複製來源類型和對應的屬性。<br/>如需詳細資訊，請參閱 [支援的資料存放區和格式](#supported-data-stores-and-formats)所列之連接器文章中的「複製活動屬性」一節。 | 是 |
| 接收 | 指定用於寫入資料的複製接收類型和對應的屬性。<br/>如需詳細資訊，請參閱 [支援的資料存放區和格式](#supported-data-stores-and-formats)所列之連接器文章中的「複製活動屬性」一節。 | 是 |
| 轉譯程式 | 指定從來源到接收的明確資料行對應。 當預設的複製行為不符合您的需求時，就會套用此屬性。<br/>如需詳細資訊，請參閱 [複製活動中的架構對應](copy-activity-schema-and-type-mapping.md)。 | 否 |
| dataIntegrationUnits | 指定量值，表示 [Azure integration runtime](concepts-integration-runtime.md) 用來複製資料的能力。 這些單位先前稱為雲端資料移動單位， (DMU) 。 <br/>如需詳細資訊，請參閱 [資料整合單位](copy-activity-performance-features.md#data-integration-units)。 | 否 |
| parallelCopies | 指定您希望複製活動在從來源讀取資料，並將資料寫入接收時使用的平行處理原則。<br/>如需詳細資訊，請參閱 [平行複製](copy-activity-performance-features.md#parallel-copy)。 | 否 |
| 保留 | 指定在資料複製期間是否保留中繼資料/Acl。 <br/>如需詳細資訊，請參閱 [保留中繼資料](copy-activity-preserve-metadata.md)。 |否 |
| enableStaging<br/>stagingSettings | 指定是否要在 Blob 儲存體中暫存過渡期資料，而不是直接將資料從來源複製到接收。<br/>如需實用案例和設定詳細資料的詳細資訊，請參閱 [分段複製](copy-activity-performance-features.md#staged-copy)。 | 否 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 選擇當您將資料從來源複製到接收時，如何處理不相容的資料列。<br/>如需詳細資訊，請參閱 [容錯](copy-activity-fault-tolerance.md)。 | 否 |

## <a name="monitoring"></a>監視

您可以透過視覺化和程式設計的方式，監視 Azure Data Factory 中的複製活動執行。 如需詳細資訊，請參閱 [監視複製活動](copy-activity-monitoring.md)。

## <a name="incremental-copy"></a>增量複製

Data Factory 可讓您以累加方式將差異資料從來源資料存放區複製到接收資料存放區。 如需詳細資訊，請參閱 [教學課程：以累加方式複製資料](tutorial-incremental-copy-overview.md)。

## <a name="performance-and-tuning"></a>效能和微調

[複製活動監視](copy-activity-monitoring.md)體驗會顯示每個活動執行的複製效能統計資料。 [複製活動的效能和擴充性指南](copy-activity-performance.md)會說明在 Azure Data Factory 的複製活動中，影響資料移動效能的主要因素。 它也會列出在測試期間觀察到的效能值，並討論如何優化複製活動的效能。

## <a name="resume-from-last-failed-run"></a>從上次失敗的執行繼續

當您以檔案為基礎的存放區之間的二進位格式複製大量的檔案，並選擇保留來源到接收的資料夾/檔案階層（例如，將資料從 Amazon S3 遷移至 Azure Data Lake Storage Gen2）時，複製活動支援從上次失敗的執行繼續。 它適用于下列以檔案為基礎的連接器： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、 [檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)和 [SFTP](connector-sftp.md)。

您可以利用下列兩種方式來利用複製活動繼續：

- **活動層級重試：** 您可以設定複製活動的重試計數。 在管線執行期間，如果此複製活動執行失敗，下一個自動重試將會從最後一個試用的失敗點開始。
- **從失敗的活動重新執行：** 管線執行完成之後，您也可以在 ADF UI 監視視圖或以程式設計方式觸發從失敗的活動重新執行。 如果失敗的活動是複製活動，管線將不會從此活動重新執行，但也會從先前執行的失敗點繼續。

    ![複製繼續](media/copy-activity-overview/resume-copy.png)

幾點注意事項：

- 在檔案層級繼續發生。 複製檔案時，如果複製活動失敗，則在下次執行時，將會重新複製這個特定的檔案。
- 若要讓繼續正常運作，請勿在重新執行的之間變更複製活動設定。
- 當您從 Amazon S3、Azure Blob、Azure Data Lake Storage Gen2 和 Google Cloud Storage 複製資料時，複製活動可以從任意數目的複製檔案繼續。 至於以檔案為基礎的其餘連接器作為來源，則目前的「複製活動」支援從有限數量的檔案繼續，通常是在數十千的範圍內，而且會根據檔案路徑的長度而有所不同;在重新執行期間，將會重新複製超過此數目的檔案。

針對其他案例，而非二進位檔案複製，則會從頭開始複製活動重新執行。

## <a name="preserve-metadata-along-with-data"></a>保留中繼資料以及資料

將資料從來源複製到接收時，在 data lake 遷移等案例中，您也可以選擇使用「複製活動」來保留中繼資料和 Acl 以及資料。 請參閱 [保留中繼資料](copy-activity-preserve-metadata.md) 以取得詳細資料。

## <a name="schema-and-data-type-mapping"></a>結構描述和資料類型對應

如需複製活動如何將來源資料對應至接收的詳細資訊，請參閱 [架構和資料類型對應](copy-activity-schema-and-type-mapping.md) 。

## <a name="add-additional-columns-during-copy"></a>在複製期間新增其他資料行

除了將資料從來源資料存放區複製到接收，您也可以將設定為加入其他資料行，以複製到接收。 例如：

- 從以檔案為基礎的來源進行複製時，請將相對檔案路徑儲存為額外的資料行，以追蹤資料的來源檔案。
- 將指定的來源資料行複製為另一個資料行。 
- 新增具有 ADF 運算式的資料行，以附加 ADF 系統變數（例如管線名稱/管線識別碼），或從上游活動的輸出儲存其他動態值。
- 新增具有靜態值的資料行，以符合您的下游耗用量需求。

您可以在 [複製活動來源] 索引標籤上找到下列設定。您也可以使用您定義的資料行名稱，以平常的方式對應複製活動 [架構對應](copy-activity-schema-and-type-mapping.md#schema-mapping) 中的其他資料行。 

![在複製活動中新增其他資料行](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>這項功能適用于最新的資料集模型。 如果您在 UI 中看不到這個選項，請嘗試建立新的資料集。

若要以程式設計方式進行設定，請 `additionalColumns` 在複製活動來源中新增屬性：

| 屬性 | 描述 | 必要 |
| --- | --- | --- |
| additionalColumns | 新增要複製到接收的其他資料行。<br><br>陣列下的每個物件都 `additionalColumns` 代表一個額外的資料行。 `name`會定義資料行名稱，並 `value` 指出該資料行的資料值。<br><br>允許的資料值為：<br>- **`$$FILEPATH`** -保留變數表示要將來源檔案的相對路徑儲存至資料集內所指定的資料夾路徑。 適用于以檔案為基礎的來源。<br>- **$ $COLUMN： <source_column_name>** -保留變數模式指出要將指定的來源資料行複製為另一個資料行<br>- **表達**<br>- **靜態值** | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>自動建立接收資料表

將資料複製到 SQL database/Azure Synapse Analytics 時，如果目的地資料表不存在，複製活動支援根據來源資料自動建立。 它的目標是協助您快速開始載入資料並評估 SQL database/Azure Synapse Analytics。 資料內嵌之後，您可以根據您的需求來檢查和調整接收資料表架構。

從任何來源將資料複製到下列接收資料存放區時，支援此功能。 您可以在 *ADF 撰寫 UI* 上找到選項– > *複製活動接收* – > *資料表選項* – > *自動建立資料表*，或透過 `tableOption` 複製活動接收承載中的屬性。

- [Azure SQL Database](connector-azure-sql-database.md)
- [Azure SQL Database 受控執行個體](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics (先前稱為 SQL 資料倉儲)](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![建立接收資料表](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>容錯

根據預設，複製活動會在來源資料列與接收資料列不相容時，停止複製資料並傳回失敗。 您可以設定複製活動來略過並記錄不相容的資料列，並只複製相容的資料，以使複製成功。 如需詳細資料，請參閱 [複製活動的容錯](copy-activity-fault-tolerance.md) 。

## <a name="next-steps"></a>後續步驟
請參閱下列快速入門、教學課程和範例：

- [將資料從一個位置複製到相同 Azure Blob 儲存體帳戶中的另一個位置](quickstart-create-data-factory-dot-net.md)
- [將資料從 Azure Blob 儲存體複製到 Azure SQL Database](tutorial-copy-data-dot-net.md)
- [從 SQL Server 資料庫將資料複製到 Azure](tutorial-hybrid-copy-powershell.md)
