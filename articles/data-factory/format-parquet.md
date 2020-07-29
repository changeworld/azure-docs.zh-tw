---
title: Azure Data Factory 中的 Parquet 格式
description: 本主題說明如何處理 Azure Data Factory 中的 Parquet 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: jingwang
ms.openlocfilehash: 9ad0ccdabd0320d8821d0760ca9802db37049149
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84611017"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure Data Factory 中的 Parquet 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要**剖析 Parquet 檔案，或將資料寫入 Parquet 格式**時，請遵循這篇文章。 

下列連接器支援 Parquet 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Parquet 資料集所支援的屬性清單。

| 屬性         | 說明                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Parquet**。 | Yes      |
| location         | 檔案的位置設定。 每個以檔案為基礎的連接器在下都有自己的位置類型和支援的屬性 `location` 。 **請參閱連接器文章-> 資料集屬性一節中的詳細資訊**。 | Yes      |
| compressionCodec | 寫入 Parquet 檔案時要使用的壓縮編解碼器。 從 Parquet 檔案讀取時，Data factory 會自動根據檔案中繼資料來決定壓縮編解碼器。<br>支援的類型為 "**none**"、"**gzip**"、"**snappy**" （預設值）和 "**lzo**"。 注意：在讀取/寫入 Parquet 檔案時，目前複製活動不支援 LZO。 | No       |

> [!NOTE]
> Parquet 檔不支援資料行名稱中的空白字元。

以下是 Azure Blob 儲存體上的 Parquet 資料集範例：

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Parquet 來源和接收所支援的屬性清單。

### <a name="parquet-as-source"></a>Parquet 作為來源

複製活動的 [ *** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**ParquetSource**。 | Yes      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | No       |

### <a name="parquet-as-sink"></a>Parquet 作為接收

複製活動的 [ *** \* 接收 \* *** ] 區段支援下列屬性。

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**ParquetSink**。 | Yes      |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在下都有它自己的支援寫入設定 `storeSettings` 。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | No       |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，您可以讀取和寫入下列資料存放區中的 parquet 格式： [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>來源屬性

下表列出 parquet 來源所支援的屬性。 您可以在 [**來源選項**] 索引標籤中編輯這些屬性。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是`parquet` | 是 | `parquet` | format |
| 萬用字元路徑 | 所有符合萬用字元路徑的檔案都會被處理。 覆寫資料集中所設定的資料夾和檔案路徑。 | 不可以 | String[] | wildcardPaths |
| 分割區根路徑 | 對於已分割的檔案資料，您可以輸入資料分割根路徑，以便將分割的資料夾當做資料行來讀取 | 不可以 | String | partitionRootPath |
| 檔案清單 | 您的來源是否指向列出要處理之檔案的文字檔 | 不可以 | `true` 或 `false` | fileList |
| 要儲存檔案名稱的資料行 | 建立具有來原始檔案名和路徑的新資料行 | 不可以 | String | rowUrlColumn |
| 完成後 | 在處理之後刪除或移動檔案。 檔案路徑從容器根目錄開始 | 不可以 | Delete： `true` 或`false` <br> 進入`[<from>, <to>]` | purgeFiles <br> moveFiles |
| 依上次修改時間篩選 | 選擇根據上次修改檔案的時間來篩選檔案 | 不可以 | 時間戳記 | modifiedAfter <br> modifiedBefore |

### <a name="source-example"></a>來源範例

下圖是對應資料流程中 parquet 來源設定的範例。

![Parquet 來源](media/data-flow/parquet-source.png)

相關聯的資料流程腳本如下：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'parquet') ~> ParquetSource
```

### <a name="sink-properties"></a>接收屬性

下表列出 parquet 來源所支援的屬性。 您可以在 [**來源選項**] 索引標籤中編輯這些屬性。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是`parquet` | 是 | `parquet` | format |
| 清除資料夾 | 如果在寫入之前清除目的資料夾 | 不可以 | `true` 或 `false` | truncate |
| 檔案名選項 | 寫入之資料的命名格式。 根據預設，每個資料分割的一個檔案格式`part-#####-tid-<guid>` | 不可以 | 模式：字串 <br> 每個資料分割： String [] <br> 當做資料行中的資料：字串 <br> 輸出至單一檔案：`['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>接收範例

下圖是對應資料流程中的 parquet 接收設定範例。

![Parquet 接收器](media/data-flow/parquet-sink.png)

相關聯的資料流程腳本如下：

```
ParquetSource sink(
    format: 'parquet',
    filePattern:'output[n].parquet',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> ParquetSink
```

## <a name="data-type-support"></a>資料類型支援

目前不支援 Parquet 複雜的資料類型（例如 MAP、LIST、STRUCT）。

## <a name="using-self-hosted-integration-runtime"></a>使用自我裝載的 Integration Runtime

> [!IMPORTANT]
> 針對由自我裝載 Integration Runtime （例如在內部部署和雲端資料存放區之間）提供的複製，如果您不是依預設複製 Parquet**檔案，則**需要在 IR 機器上安裝**64 位 JRE 8 （JAVA Runtime Environment）或 OpenJDK**和**Microsoft Visual C++ 2010**可轉散發套件。 請參閱下列段落，其中包含更多詳細資料。

針對在自我裝載 IR 上搭配 Parquet 檔案序列化/還原序列化來執行的複製，ADF 會找出 JAVA 執行時間，方法是先檢查登錄 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 是否有 JRE，如果找不到，則接著檢查系統變數 *`JAVA_HOME`* 是否有 OpenJDK。

- **若要使用 JRE**：64位 IR 需要64位 JRE。 您可以從[這裡](https://go.microsoft.com/fwlink/?LinkId=808605)找到該程式。
- **若要使用 OpenJDK**：從 IR 版本3.13 開始支援。 請將 jvm.dll 與所有其他必要的 OpenJDK 組件一起封裝至自我裝載 IR 機器，然後相應地設定 JAVA_HOME 系統環境變數。
- **若要安裝 Visual C++ 2010 可**轉散發套件： Visual C++ 2010 可轉散發套件不會隨自我裝載 IR 安裝一起安裝。 您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=14632)找到該程式。

> [!TIP]
> 如果您使用自我裝載 Integration Runtime 將資料複製到 Parquet 格式 (或從該格式複製資料)，而且遇到錯誤顯示 [叫用 Java 時發生錯誤。訊息: **java.lang.OutOfMemoryError:Java heap space**]，您可以在裝載自我裝載 IR 的機器中新增環境變數 `_JAVA_OPTIONS`，以調整 JVM 的堆積大小下限/上限，使系統能執行這樣的複製，然後重新執行管線。

![在自我裝載 IR 上設定 JVM 堆積大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

範例：將變數 `_JAVA_OPTIONS` 的值設定為 `-Xms256m -Xmx16g`。 旗標 `Xms` 指定 JAVA 虛擬機器 (JVM) 的初始記憶體配置集區，而 `Xmx` 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 `Xms` 數量的記憶體，且最多可以使用 `Xmx` 數量的記憶體。 根據預設，ADF 會使用最小的 64 MB 和最大1G。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
