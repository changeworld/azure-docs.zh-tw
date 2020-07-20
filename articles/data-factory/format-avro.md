---
title: Azure Data Factory 中的 Avro 格式
description: 本主題說明如何在 Azure Data Factory 中處理 Avro 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: jingwang
ms.openlocfilehash: 32af8c1b19d57fdba58ce27700e5d1e7a34f9c64
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84604978"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure Data Factory 中的 Avro 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要**剖析 avro 檔案，或將資料寫入 avro 格式**時，請遵循這篇文章。 

下列連接器支援 Avro 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Avro 資料集所支援的屬性清單。

| 屬性         | 說明                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Avro**。 | Yes      |
| location         | 檔案的位置設定。 每個以檔案為基礎的連接器在下都有自己的位置類型和支援的屬性 `location` 。 **請參閱連接器文章-> 資料集屬性一節中的詳細資訊**。 | Yes      |
| avroCompressionCodec | 寫入 Avro 檔案時所要使用的壓縮編解碼器。 從 Avro 檔案讀取時，Data Factory 會根據檔案中繼資料自動決定壓縮編解碼器。<br>支援的類型為 "**none**" （預設值）、"**deflate**"、"**snappy**"。 請注意，當讀取/寫入 Avro 檔案時，複製活動目前不支援 Snappy。 | No       |

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

複製活動的 [ *** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**AvroSource**。 | Yes      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | No       |

### <a name="avro-as-sink"></a>Avro 作為接收器

複製活動的 [ *** \* 接收 \* *** ] 區段支援下列屬性。

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**AvroSink**。 | Yes      |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在下都有它自己的支援寫入設定 `storeSettings` 。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | No       |


## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，您可以在下列資料存放區中讀取和寫入 avro 格式： [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>來源屬性

下表列出 avro 來源所支援的屬性。 您可以在 [**來源選項**] 索引標籤中編輯這些屬性。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 萬用字元路徑 | 所有符合萬用字元路徑的檔案都會被處理。 覆寫資料集中所設定的資料夾和檔案路徑。 | 不可以 | String[] | wildcardPaths |
| 分割區根路徑 | 對於已分割的檔案資料，您可以輸入資料分割根路徑，以便將分割的資料夾當做資料行來讀取 | 不可以 | String | partitionRootPath |
| 檔案清單 | 您的來源是否指向列出要處理之檔案的文字檔 | 不可以 | `true` 或 `false` | fileList |
| 要儲存檔案名稱的資料行 | 建立具有來原始檔案名和路徑的新資料行 | 不可以 | String | rowUrlColumn |
| 完成後 | 在處理之後刪除或移動檔案。 檔案路徑從容器根目錄開始 | 不可以 | Delete： `true` 或`false` <br> 進入`['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 依上次修改時間篩選 | 選擇根據上次修改檔案的時間來篩選檔案 | 不可以 | 時間戳記 | modifiedAfter <br> modifiedBefore |

### <a name="sink-properties"></a>接收屬性

下表列出 avro 接收器所支援的屬性。 您可以在 [**設定**] 索引標籤中編輯這些屬性。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 清除資料夾 | 如果在寫入之前清除目的資料夾 | 不可以 | `true` 或 `false` | truncate |
| 檔案名選項 | 寫入之資料的命名格式。 根據預設，每個資料分割的一個檔案格式`part-#####-tid-<guid>` | 不可以 | 模式：字串 <br> 每個資料分割： String [] <br> 當做資料行中的資料：字串 <br> 輸出至單一檔案：`['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| 全部報價 | 以引號括住所有的值 | 不可以 | `true` 或 `false` | quoteAll |

## <a name="data-type-support"></a>資料類型支援

### <a name="copy-activity"></a>複製活動
在複製活動中不支援 Avro[複雜資料類型](https://avro.apache.org/docs/current/spec.html#schema_complex)（記錄、列舉、陣列、對應、等位和固定）。

### <a name="data-flows"></a>資料流程
在資料流程中使用 Avro 檔案時，您可以讀取和寫入複雜的資料類型，但請務必先從資料集清除實體架構。 在 [資料流程] 中，您可以設定邏輯投射並衍生複雜結構的資料行，然後將這些欄位自動對應到 Avro 檔案。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
