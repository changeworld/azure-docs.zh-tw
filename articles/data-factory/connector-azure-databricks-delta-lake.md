---
title: 將資料複製到 Azure Databricks Delta Lake 或從中複製資料
description: 瞭解如何使用 Azure Data Factory 管線中的複製活動，將資料複製到 Azure Databricks Delta Lake 或從中複製資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/28/2020
ms.openlocfilehash: 4ff1a793b3e8c4fe642aa304f1aa59bd8edefb8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91405612"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure Databricks Delta Lake 或從中複製資料

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料複製到 Azure Databricks Delta Lake 或從中複製資料。 它是 [以 Azure Data Factory 文章中的「複製活動](copy-activity-overview.md) 」為基礎，其中提供「複製活動」的一般總覽。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure Databricks Delta Lake connector：

- 使用[支援的來源/接收矩陣](copy-activity-overview.md)資料表[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

一般來說，Azure Data Factory 支援 Delta Lake，並具備下列功能來滿足您的各種需求。

- 複製活動支援 Azure Databricks Delta Lake connector，以將資料從任何支援的來源資料存放區複製到 Azure Databricks Delta lake 資料表，以及從 delta lake 資料表複製到任何支援的接收資料存放區。 它會利用您的 Databricks 叢集來執行資料移動，請參閱「 [必要條件」一節](#prerequisites)中的詳細資料。
- [對應資料流程](concepts-data-flow-overview.md) 支援 Azure 儲存體上的一般 [差異格式](format-delta.md) 作為來源和接收，以讀取和寫入無程式碼 ETL 的差異檔案，並在受控 Azure Integration Runtime 上執行。
- [Databricks 活動](transform-data-databricks-notebook.md) 支援在 delta lake 之上協調以程式碼為中心的 ETL 或機器學習工作負載。

## <a name="prerequisites"></a>必要條件

若要使用此 Azure Databricks Delta Lake connector，您需要在 Azure Databricks 中設定叢集。

- 若要將資料複製到 delta lake，複製活動會叫用 Azure Databricks 叢集以從 Azure 儲存體讀取資料，也就是您的原始來源或臨時區域，Data Factory 首先透過內建的分段複製來寫入來源資料。 深入瞭解從 [Delta lake 作為來源](#delta-lake-as-source)。
- 同樣地，若要從 delta lake 複製資料，複製活動會叫用 Azure Databricks 叢集，以將資料寫入至 Azure 儲存體，也就是您的原始接收或臨時區域，Data Factory 透過內建的分段複製，繼續將資料寫入到最後的接收。 深入瞭解如何 [以 Delta lake 作為接收器](#delta-lake-as-sink)。

Databricks 叢集必須能夠存取 Azure Blob 或 Azure Data Lake Storage Gen2 帳戶，也就是用於來源/接收/暫存的儲存體容器/檔案系統，以及您想要在其中寫入 Delta Lake 資料表的容器/檔案系統。

- 若要使用 **Azure Data Lake Storage Gen2**，您可以在 Apache Spark 設定中設定 Databricks 叢集上的 **服務主體** 或 **儲存體帳戶存取金鑰** 。 遵循 [直接使用服務主體存取](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) 的步驟，或 [直接使用儲存體帳戶存取金鑰來存取](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key)。

- 若要使用 **Azure Blob 儲存體**，您可以在 Databricks 叢集上設定 **儲存體帳戶存取金鑰** 或 **SAS 權杖** ，作為 Apache Spark 設定的一部分。 遵循 [使用 RDD API 存取 Azure Blob 儲存體](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api)中的步驟。

在複製活動執行期間，如果您設定的叢集已終止，Data Factory 自動啟動它。 如果您使用 Data Factory 撰寫 UI 來撰寫管線，針對資料預覽等作業，您需要有即時叢集，Data Factory 不會代表您啟動叢集。

#### <a name="specify-the-cluster-configuration"></a>指定叢集設定

1. 在 [叢集 **模式]** 下拉式清單中，選取 [ **標準**]。

2. 在 [ **Databricks Runtime 版本** ] 下拉式清單中，選取 Databricks 執行階段版本。

3. 將下列屬性新增至[Spark](https://docs.microsoft.com/azure/databricks/clusters/configure#spark-config)設定，以開啟[自動優化](https://docs.microsoft.com/azure/databricks/delta/optimizations/auto-optimize)：

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. 根據您的整合和調整需求，設定您的叢集。

如需叢集設定的詳細資訊，請參閱 [設定](https://docs.microsoft.com/azure/databricks/clusters/configure)叢集。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性會定義 Azure Databricks Delta Lake connector 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure Databricks Delta Lake 已連結服務支援的屬性。

| 屬性    | 描述                                                  | 必要 |
| :---------- | :----------------------------------------------------------- | :------- |
| type        | Type 屬性必須設為 **AzureDatabricksDeltaLake**。 | 是      |
| 網域      | 指定 Azure Databricks 工作區 URL，例如 `https://adb-xxxxxxxxx.xx.azuredatabricks.net` 。 |          |
| clusterId   | 指定現有叢集的叢集識別碼。 它應該是已建立的互動式叢集。 <br>您可以在 Databricks 工作區 -> [叢集]-> [互動式叢集名稱]-> [設定]-> [標記] 找到互動式叢集的叢集識別碼。 [深入了解](https://docs.microsoft.com/azure/databricks/clusters/configure#cluster-tags)。 |          |
| accessToken | Data Factory 需要有存取權杖才能向 Azure Databricks 進行驗證。 存取權杖必須由 Databricks 工作區產生。 您可以在 [這裡](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#generate-token)找到更詳細的尋找存取權杖的步驟。 |          |
| connectVia  | 用來連接到資料存放區的 [整合運行](concepts-integration-runtime.md) 時間。 如果您的資料存放區位於私人網路) ，您可以使用 Azure integration runtime 或自我裝載整合執行時間 (。 如果未指定，則會使用預設的 Azure integration runtime。 | 否       |

**範例︰**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 

以下是 Azure Databricks Delta Lake 資料集支援的屬性。

| 屬性  | 描述                                                  | 必要                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 資料集的 type 屬性必須設為 **AzureDatabricksDeltaLakeDataset**。 | 是                         |
| [資料庫] | 資料庫的名稱。 |否，來源，是接收的  |
| 資料表 | 差異資料表的名稱。 |否，來源，是接收的  |

**範例︰**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure Databricks Delta Lake 來源和接收所支援的屬性清單。

### <a name="delta-lake-as-source"></a>Delta lake 作為來源

若要從 Azure Databricks Delta Lake 複製資料，[複製活動 **來源** ] 區段支援下列屬性。

| 屬性                     | 描述                                                  | 必要 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 複製活動來源的 type 屬性必須設為 **AzureDatabricksDeltaLakeSource**。 | 是      |
| 查詢          | 指定要讀取資料的 SQL 查詢。 針對「時間移動」控制項，請遵循下列模式：<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | 否       |
| exportSettings | 用來從 delta 資料表中取出資料的 Advanced 設定。 | 否       |
| ***在 `exportSettings` 下列情況下：*** |  |  |
| type | 匯出命令的類型，設定為 **AzureDatabricksDeltaLakeExportCommand**。 | 是 |
| dateFormat | 使用日期格式將日期類型格式化為字串。 自訂日期格式會遵循 [日期時間模式](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)的格式。 如果未指定，則會使用預設值 `yyyy-MM-dd` 。 | 否 |
| timestampFormat | 將 timestamp 類型格式化為具有時間戳記格式的字串。 自訂日期格式會遵循 [日期時間模式](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)的格式。 如果未指定，則會使用預設值 `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` 。 | 否 |

#### <a name="direct-copy-from-delta-lake"></a>從 delta lake 直接複製

如果您的接收資料存放區和格式符合本節所述的準則，您可以使用複製活動，直接從 Azure Databricks Delta 資料表複製到接收。 Data Factory 會檢查設定，並在不符合下列準則時失敗複製活動執行：

- **接收連結服務**是[Azure Blob 儲存體](connector-azure-blob-storage.md)或[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)。 帳號憑證應該在 Azure Databricks 叢集設定中預先設定，請從 [必要條件](#prerequisites)中深入瞭解。

- **接收資料格式**是**Parquet**、分隔的**文字**，或是具有下列設定的**Avro** ，並指向資料夾而非檔案。

    - 針對 **Parquet** 格式，壓縮編解碼器為 **none**、 **snappy**或 **gzip**。
    - 若為 **分隔的文字** 格式：
        - `rowDelimiter` 為任何單一字元。
        - `compression` 可以是 **none**、 **bzip2**、 **gzip**。
        - `encodingName` 不支援 UTF-7。
    - 若為 **Avro** 格式，壓縮編解碼器為 **none**、 **deflate**或 **snappy**。

- 在複製活動來源中， `additionalColumns` 未指定。
- 如果將資料複製到複製活動接收中的分隔文字，則必須 `fileExtension` 是 ".csv"。
- 在複製活動對應中，未啟用類型轉換。

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>從 delta lake 進行分段複製

當您的接收資料存放區或格式不符合直接複製準則（如最後一節所述）時，請使用暫時的 Azure 儲存體實例來啟用內建的分段複製。 分段複製功能也能提供更好的輸送量。 Data Factory 將資料從 Azure Databricks Delta Lake 匯出至暫存儲存體，然後將資料複製到接收，最後從暫存儲存體清除暫存資料。 如需使用暫存複製資料的詳細資訊，請參閱 [分段複製](copy-activity-performance-features.md#staged-copy) 。

若要使用此功能，請建立 [Azure Blob 儲存體連結服務](connector-azure-blob-storage.md#linked-service-properties) 或 [Azure Data Lake Storage Gen2 連結服務](connector-azure-data-lake-storage.md#linked-service-properties) ，將儲存體帳戶稱為過渡暫存。 然後指定 `enableStaging` `stagingSettings` 複製活動中的和屬性。

>[!NOTE]
>您應在 Azure Databricks 叢集設定中預先設定暫存儲存體帳號憑證，以深入瞭解 [必要條件](#prerequisites)。

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Delta lake 作為接收

若要將資料複製到 Azure Databricks Delta Lake，複製活動 **接收器** 區段支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| :------------ | :----------------------------------------------------------- | :------- |
| type          | 複製活動接收的 type 屬性，設定為 **AzureDatabricksDeltaLakeSink**。 | 是      |
| preCopyScript | 針對要在每次執行中的 Databricks delta 資料表寫入資料之前執行的複製活動，指定 SQL 查詢。 您可以使用這個屬性來清除預先載入的資料，或加入截斷資料表或清除語句。 | 否       |
| importSettings | 用來將資料寫入 delta 資料表的 Advanced 設定。 | 否 |
| ***在 `importSettings` 下列情況下：*** |                                                              |  |
| type | 匯入命令的類型，設定為 **AzureDatabricksDeltaLakeImportCommand**。 | 是 |
| dateFormat | 使用日期格式將字串格式化為日期類型。 自訂日期格式會遵循 [日期時間模式](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)的格式。 如果未指定，則會使用預設值 `yyyy-MM-dd` 。 | 否 |
| timestampFormat | 使用時間戳格式將字串格式化為 timestamp 類型。 自訂日期格式會遵循 [日期時間模式](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)的格式。 如果未指定，則會使用預設值 `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` 。 | 否 |

#### <a name="direct-copy-to-delta-lake"></a>直接複製到 delta lake

如果您的來源資料存放區和格式符合本節所述的準則，您可以使用複製活動，直接從來源複製到 Azure Databricks Delta Lake。 Azure Data Factory 會檢查設定，並在不符合下列準則時失敗複製活動執行：

- **來源連結服務**是[Azure Blob 儲存體](connector-azure-blob-storage.md)或[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)。 帳號憑證應該在 Azure Databricks 叢集設定中預先設定，請從 [必要條件](#prerequisites)中深入瞭解。

- **源資料格式**是**Parquet**、分隔的**文字**，或是具有下列設定的**Avro** ，並指向資料夾而非檔案。

    - 針對 **Parquet** 格式，壓縮編解碼器為 **none**、 **snappy**或 **gzip**。
    - 若為 **分隔的文字** 格式：
        - `rowDelimiter` 為預設值，或任何單一字元。
        - `compression` 可以是 **none**、 **bzip2**、 **gzip**。
        - `encodingName` 不支援 UTF-7。
    - 若為 **Avro** 格式，壓縮編解碼器為 **none**、 **deflate**或 **snappy**。

- 在複製活動來源中： 

    - `wildcardFileName` 只包含萬用字元 `*` ，但未 `?` `wildcardFolderName` 指定。
    - 未指定 `prefix`、`modifiedDateTimeStart`、`modifiedDateTimeEnd` 和 `enablePartitionDiscovery`。
    - `additionalColumns` 未指定。

- 在複製活動對應中，未啟用類型轉換。

**範例︰**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>暫存複製到 delta lake

當您的來源資料存放區或格式不符合直接複製準則（如最後一節所述）時，請使用暫時的 Azure 儲存體實例來啟用內建的分段複製。 分段複製功能也能提供更好的輸送量。 Data Factory 會自動轉換資料以符合暫存儲存體中的資料格式需求，然後從該處將資料載入 delta lake。 最後，它會清除儲存體中的暫存資料。 如需使用暫存複製資料的詳細資訊，請參閱 [分段複製](copy-activity-performance-features.md#staged-copy) 。

若要使用此功能，請建立 [Azure Blob 儲存體連結服務](connector-azure-blob-storage.md#linked-service-properties) 或 [Azure Data Lake Storage Gen2 連結服務](connector-azure-data-lake-storage.md#linked-service-properties) ，將儲存體帳戶稱為過渡暫存。 然後指定 `enableStaging` `stagingSettings` 複製活動中的和屬性。

>[!NOTE]
>您應在 Azure Databricks 叢集設定中預先設定暫存儲存體帳號憑證，以深入瞭解 [必要條件](#prerequisites)。

**範例︰**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>監視

Azure Data Factory 提供與其他連接器相同的 [複製活動監視體驗](copy-activity-monitoring.md) 。 此外，由於您的 Azure Databricks 叢集上正在執行 delta lake 的載入資料，因此您可以進一步 [查看詳細](https://docs.microsoft.com/azure/databricks/clusters/clusters-manage#--view-cluster-logs) 的叢集記錄和 [監視效能](https://docs.microsoft.com/azure/databricks/clusters/clusters-manage#--monitor-performance)。

## <a name="lookup-activity-properties"></a>查閱活動屬性

如需屬性的詳細資訊，請參閱 [查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

如需 Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱 [支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
