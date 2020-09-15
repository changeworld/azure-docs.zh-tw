---
title: Azure Data Factory 中的 Parquet 格式
description: 本主題說明如何在 Azure Data Factory 中處理 Parquet 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 91455e4797324f28f911dd8a928410517a951728
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531741"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure Data Factory 中的 Parquet 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要 **剖析 Parquet 檔案，或將資料寫入 Parquet 格式**時，請遵循這篇文章。 

下列連接器支援 Parquet 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、 [檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Parquet 資料集所支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的 type 屬性必須設為 **Parquet**。 | 是      |
| location         | 檔案 (s) 的位置設定。 每個以檔案為基礎的連接器都有自己的位置類型和支援的屬性 `location` 。 **請參閱連接器文章中的詳細資料-> 資料集屬性一節**。 | 是      |
| compressionCodec | 寫入 Parquet 檔案時要使用的壓縮編解碼器。 從 Parquet 檔讀取時，Data factory 會自動根據檔案中繼資料來判斷壓縮編解碼器。<br>支援的類型為 "**none**"、"**gzip**"、"**snappy**" (預設) 和 "**lzo**"。 注意：當讀取/寫入 Parquet 檔時，目前的複製活動不支援 LZO。 | 否       |

> [!NOTE]
> Parquet 檔案不支援資料行名稱中的空白字元。

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

[複製活動*** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **ParquetSource**。 | 是      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

### <a name="parquet-as-sink"></a>Parquet 為接收

複製活動*** \* 接收 \* ***區段支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **ParquetSink**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下方的 **Parquet 寫入設定** 表格。 |    否      |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在下都有自己支援的寫入設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

支援的 **Parquet 寫入設定** `formatSettings` ：

| 屬性      | 描述                                                  | 必要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的類型必須設定為 **ParquetWriteSettings**。 | 是                                                   |
| maxRowsPerFile | 將資料寫入資料夾時，您可以選擇寫入多個檔案，並指定每個檔案的最大資料列數。  | 否 |
| fileNamePrefix | 適用于 `maxRowsPerFile` 設定時。<br> 將資料寫入多個檔案時，請指定檔案名前置詞，而導致此模式： `<fileNamePrefix>_00000.<fileExtension>` 。 如果未指定，則會自動產生檔案名前置詞。 當來源是以檔案為基礎的存放區或分割區 [選項的資料存放區](copy-activity-performance-features.md)時，此屬性不適用。  | 否 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，您可以在下列資料存放區中讀取和寫入 parquet 格式： [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>來源屬性

下表列出 parquet 來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是 `parquet` | 是 | `parquet` | format |
| 萬用字元路徑 | 將會處理所有符合萬用字元路徑的檔案。 覆寫資料集中設定的資料夾和檔案路徑。 | 否 | String[] | wildcardPaths |
| 分割區根路徑 | 針對已分割的檔案資料，您可以輸入磁碟分割根路徑，以便將分割的資料夾讀取為數據行 | 否 | String | partitionRootPath |
| 檔案清單 | 您的來源是否指向列出要處理之檔案的文字檔 | 否 | `true` 或 `false` | fileList |
| 儲存檔案名稱的資料行 | 使用來原始檔案名和路徑建立新的資料行 | 否 | String | rowUrlColumn |
| 完成後 | 在處理之後刪除或移動檔案。 從容器根目錄開始的檔案路徑 | 否 | Delete： `true` 或 `false` <br> 移動： `[<from>, <to>]` | purgeFiles <br> moveFiles |
| 依上次修改篩選 | 選擇根據上次修改檔案的時間進行篩選 | 否 | 時間戳記 | modifiedAfter <br> modifiedBefore |

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

下表列出 parquet 來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是 `parquet` | 是 | `parquet` | format |
| 清除資料夾 | 如果在寫入之前清除目的資料夾 | 否 | `true` 或 `false` | truncate |
| [檔案名] 選項 | 寫入之資料的命名格式。 依預設，每個資料分割的一個檔案格式為 `part-#####-tid-<guid>` | 否 | 模式：字串 <br> 每個分割區：字串 [] <br> 做為資料行中的資料：字串 <br> 輸出至單一檔案： `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>接收器範例

下圖是在對應資料流程中 parquet 接收設定的範例。

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

目前不支援 Parquet 複雜的資料類型 (例如 MAP、LIST、STRUCT) 。

## <a name="using-self-hosted-integration-runtime"></a>使用自我裝載的 Integration Runtime

> [!IMPORTANT]
> 若為由自我裝載 Integration Runtime （ **例如，在**內部部署與雲端資料存放區之間）所授權的複製，您必須在 IR 機器上安裝 **64 位的 JRE 8 (JAVA Runtime Environment) 或 OpenJDK** 和 **Microsoft Visual C++ 2010** 可轉散發套件。 請參閱下列段落以取得詳細資料。

若為使用 Parquet 檔案序列化/還原序列化在自我裝載 IR 上執行的複製，ADF 會先檢查 JRE 的登錄以找出 JAVA 執行時間， *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 如果找不到，則會先檢查系統變數 *`JAVA_HOME`* 以進行 OpenJDK。

- **若要使用 JRE**：64位 IR 需要64位 JRE。 您可以從[這裡](https://go.microsoft.com/fwlink/?LinkId=808605)找到該程式。
- **若要使用 OpenJDK**：自 IR 3.13 版起即支援。 請將 jvm.dll 與所有其他必要的 OpenJDK 組件一起封裝至自我裝載 IR 機器，然後相應地設定 JAVA_HOME 系統環境變數。
- **安裝 Visual C++ 2010 可**轉散發套件： Visual C++ 2010 可轉散發套件未與自我裝載 IR 安裝一起安裝。 您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=14632)找到該程式。

> [!TIP]
> 如果您使用自我裝載 Integration Runtime 將資料複製到 Parquet 格式 (或從該格式複製資料)，而且遇到錯誤顯示 [叫用 Java 時發生錯誤。訊息: **java.lang.OutOfMemoryError:Java heap space**]，您可以在裝載自我裝載 IR 的機器中新增環境變數 `_JAVA_OPTIONS`，以調整 JVM 的堆積大小下限/上限，使系統能執行這樣的複製，然後重新執行管線。

![在自我裝載 IR 上設定 JVM 堆積大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

範例：將變數 `_JAVA_OPTIONS` 的值設定為 `-Xms256m -Xmx16g`。 旗標 `Xms` 指定 JAVA 虛擬機器 (JVM) 的初始記憶體配置集區，而 `Xmx` 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 `Xms` 數量的記憶體，且最多可以使用 `Xmx` 數量的記憶體。 根據預設，ADF 使用最小 64 MB 和最大1G。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
