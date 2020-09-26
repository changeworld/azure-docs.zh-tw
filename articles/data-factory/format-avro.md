---
title: Azure Data Factory 中的 Avro 格式
description: 本主題說明如何在 Azure Data Factory 中處理 Avro 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 7d61121b4c80b7b89ec29ade4ab1bfab91a660d9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334339"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure Data Factory 中的 Avro 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要 **剖析 avro 檔案或將資料寫入 avro 格式**時，請遵循這篇文章。 

下列連接器支援 Avro 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、 [檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Avro 資料集所支援的屬性清單。

| 屬性         | 說明                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的 type 屬性必須設定為 **Avro**。 | 是      |
| location         | 檔案 (s) 的位置設定。 每個以檔案為基礎的連接器都有自己的位置類型和支援的屬性 `location` 。 **請參閱連接器文章中的詳細資料-> 資料集屬性一節**。 | 是      |
| avroCompressionCodec | 寫入 Avro 檔案時要使用的壓縮編解碼器。 從 Avro 檔案讀取時，Data Factory 會自動根據檔案中繼資料來判斷壓縮編解碼器。<br>支援的類型為 "**none**" (預設) 、"**deflate**"、"**snappy**"。 注意：當讀取/寫入 Avro 檔案時，目前的複製活動不支援 Snappy。 | 否       |

> [!NOTE]
> Avro 檔案不支援資料行名稱中的空白字元。

以下是 Azure Blob 儲存體上 Avro 資料集的範例：

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Avro 來源和接收所支援的屬性清單。

### <a name="avro-as-source"></a>Avro 作為來源

[複製活動*** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **AvroSource**。 | 是      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

### <a name="avro-as-sink"></a>Avro 作為接收

複製活動*** \* 接收 \* ***區段支援下列屬性。

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **AvroSink**。 | 是      |
| formatSettings          | 屬性的群組。 請參閱下方的 **Avro write settings** 表格。| 否      |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在下都有自己支援的寫入設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

支援的 **Avro 寫入設定** `formatSettings` ：

| 屬性      | 說明                                                  | 必要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的類型必須設定為 **AvroWriteSettings**。 | 是                                                   |
| maxRowsPerFile | 將資料寫入資料夾時，您可以選擇寫入多個檔案，並指定每個檔案的最大資料列數。  | 否 |
| fileNamePrefix | 適用于 `maxRowsPerFile` 設定時。<br> 將資料寫入多個檔案時，請指定檔案名前置詞，而導致此模式： `<fileNamePrefix>_00000.<fileExtension>` 。 如果未指定，則會自動產生檔案名前置詞。 當來源是以檔案為基礎的存放區或分割區 [選項的資料存放區](copy-activity-performance-features.md)時，此屬性不適用。  | 否 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，您可以在下列資料存放區中讀取和寫入 avro 格式： [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>來源屬性

下表列出 avro 來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 萬用字元路徑 | 將會處理所有符合萬用字元路徑的檔案。 覆寫資料集中設定的資料夾和檔案路徑。 | 否 | String[] | wildcardPaths |
| 分割區根路徑 | 針對已分割的檔案資料，您可以輸入磁碟分割根路徑，以便將分割的資料夾讀取為數據行 | 否 | String | partitionRootPath |
| 檔案清單 | 您的來源是否指向列出要處理之檔案的文字檔 | 否 | `true` 或 `false` | fileList |
| 儲存檔案名稱的資料行 | 使用來原始檔案名和路徑建立新的資料行 | 否 | String | rowUrlColumn |
| 完成後 | 在處理之後刪除或移動檔案。 從容器根目錄開始的檔案路徑 | 否 | Delete： `true` 或 `false` <br> 移動： `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 依上次修改篩選 | 選擇根據上次修改檔案的時間進行篩選 | 否 | 時間戳記 | modifiedAfter <br> modifiedBefore |
| 不允許找到任何檔案 | 若為 true，如果找不到任何檔案，就不會擲回錯誤 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>接收屬性

下表列出 avro 接收所支援的屬性。 您可以在 [ **設定** ] 索引標籤中編輯這些屬性。

| 名稱 | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 清除資料夾 | 如果在寫入之前清除目的資料夾 | 否 | `true` 或 `false` | truncate |
| [檔案名] 選項 | 寫入之資料的命名格式。 依預設，每個資料分割的一個檔案格式為 `part-#####-tid-<guid>` | 否 | 模式：字串 <br> 每個分割區：字串 [] <br> 做為資料行中的資料：字串 <br> 輸出至單一檔案： `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| 全部報價 | 以引號括住所有值 | 否 | `true` 或 `false` | quoteAll |

## <a name="data-type-support"></a>資料類型支援

### <a name="copy-activity"></a>複製活動
在複製活動中， (記錄、列舉、陣列、對應、等位和固定) 不支援 Avro [複雜資料類型](https://avro.apache.org/docs/current/spec.html#schema_complex) 。

### <a name="data-flows"></a>資料流程
當您在資料流程中使用 Avro 檔案時，您可以讀取和寫入複雜的資料類型，但請務必先清除資料集的實體架構。 在 [資料流程] 中，您可以設定邏輯投射並衍生複雜結構的資料行，然後將這些欄位自動對應至 Avro 檔案。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
