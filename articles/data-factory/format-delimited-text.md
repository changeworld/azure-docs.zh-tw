---
title: Azure Data Factory 中的分隔文字格式
description: 本主題描述如何處理 Azure Data Factory 中的分隔文字格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: jingwang
ms.openlocfilehash: 8ca3d7475472c6980be85299046624bdcf8cae11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85254451"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory 中的分隔文字格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要**剖析分隔符號文字檔，或將資料寫入分隔的文字格式**時，請遵循這篇文章。 

下列連接器支援分隔的文字格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、Azure 檔案[儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供分隔的文字資料集所支援的屬性清單。

| 屬性         | 說明                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**DelimitedText**。 | Yes      |
| location         | 檔案的位置設定。 每個以檔案為基礎的連接器在下都有自己的位置類型和支援的屬性 `location` 。  | Yes      |
| columnDelimiter  | 用來分隔檔案中資料行的字元。 <br>預設值為**逗號 `,` **。 當資料行分隔符號定義為空字串（表示沒有分隔符號）時，就會將整條程式碼當做單一資料行。<br>目前，只有對應資料流程（而非複製活動）支援資料行分隔符號做為空字串或多字元。  | No       |
| rowDelimiter     | 單一字元或 "\r\n"，用來分隔檔案中的資料列。 <br>預設值是讀取時的下列任何值 **： ["\r\n"，"\r"，"\n"]**，而 " **\n" 或 "\r\n" 在寫入**時分別對應資料流程和複製活動。 <br>當資料列分隔符號設定為 [沒有分隔符號（空字串）] 時，資料行分隔符號也必須設定為 [沒有分隔符號（空字串）]，這表示要將整個內容視為單一值。<br>目前，只有對應資料流程（而非複製活動）支援資料列分隔符號做為空字串。 | No       |
| quoteChar        | 如果包含資料行分隔符號，則為要加上引號的單一字元。 <br>預設值為**雙引號** `"` 。 <br>針對對應資料流程， `quoteChar` 不可以是空字串。 <br>若為複製活動，當 `quoteChar` 定義為空字串時，表示沒有引號字元，而且資料行的值未加上引號，而 `escapeChar` 是用來將資料行分隔符號和本身轉義。 | No       |
| escapeChar       | 用來將引號括在引號值內的單一字元。<br>預設值為**反斜線 `\` **。 <br>針對對應資料流程， `escapeChar` 不可以是空字串。 <br/>若為複製活動，當 `escapeChar` 定義為空字串時， `quoteChar` 也必須將設定為空字串，在此情況下，請確定所有資料行值都不包含分隔符號。 | No       |
| firstRowAsHeader | 指定是否要將第一個資料列視為具有資料行名稱的標頭行。<br>允許的值為**true**和**false** （預設值）。<br>當第一個資料列的標頭為 false 時，請注意 UI 資料預覽和查閱活動輸出會自動以 Prop_ {n} （從0開始）產生資料行名稱，複製活動需要從來源到接收的[明確對應](copy-activity-schema-and-type-mapping.md#explicit-mapping)，並依序數（從1開始）找出資料行，並將名稱為 Column_ {n} （從1開始）。  | No       |
| nullValue        | 指定 null 值的字串表示。 <br>預設值為**空字串**。 | No       |
| encodingName     | 用來讀取/寫入測試檔案的編碼類型。 <br>允許的值如下： "UTF-8"、"UTF-16"、"UTF-UTF-16BE"、"UTF-32"、"32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"，"GB18030"，"JOHAB"，"SHIFT-JIS"，"CP875"，"CP866"，"IBM00858"，"IBM037"，"IBM273"，"IBM437"，"IBM500"，"IBM737"，"IBM775"，"IBM850"，"IBM852"，"IBM855"，"IBM857"，"IBM860"，"IBM861"，"IBM863"，"IBM864"，"IBM865"，"IBM869"，"IBM870"，"IBM01140"，"IBM01141"，"IBM01142"，"IBM01143"，"IBM01144"，"IBM01145"，"IBM01146"，"IBM01147"，"IBM01148"，"IBM01149"，"ISO-2022-JP"，"ISO-2022-KR"，"ISO-8859-1"，"ISO-8859-2"，"ISO-8859-3"，"ISO-8859-4"，"ISO-8859-5"，"ISO-8859-6"，"ISO-8859-7"，"iso-8859-8"，"iso-8859-9"，"ISO-8859-13"、「ISO-8859-15」、「WINDOWS-874」、「WINDOWS-1250」、「WINDOWS-1251」、「WINDOWS-1252」、「WINDOWS-1253」、「WINDOWS-1254」、「WINDOWS-1255」、「WINDOWS-1256」、「WINDOWS-1257」、「WINDOWS-1258」。<br>注意：對應的資料流程不支援 UTF-7 編碼。 | No       |
| compressionCodec | 用來讀取/寫入文字檔的壓縮編解碼器。 <br>允許的值為**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **snappy**或**lz4**。 預設值為 [未壓縮]。 <br>**注意：** 目前的複製活動不支援 "snappy" & "lz4"，而且對應資料流程不支援 "ZipDeflate"。 <br>**請注意**，使用複製活動來解壓縮**ZipDeflate**檔案並寫入以檔案為基礎的接收資料存放區時，預設會將檔案解壓縮至資料夾： `<path specified in dataset>/<folder named as source zip file>/` ， `preserveZipFileNameAsFolder` 在[複製活動來源](#delimited-text-as-source)上使用，以控制是否要將 ZIP 檔案名保留為資料夾結構。 | No       |
| compressionLevel | 壓縮比例。 <br>允許的值為**最佳**或**最快**。<br>- **最快速：** 即使產生的檔案未以最佳方式壓縮，壓縮作業也應該儘快完成。<br>- **最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | No       |

以下是 Azure Blob 儲存體上的分隔文字資料集範例：

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供分隔的文字來源和接收所支援的屬性清單。

### <a name="delimited-text-as-source"></a>分隔的文字做為來源 

複製活動的 [ *** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性       | 說明                                                  | 必要 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 複製活動來源的類型屬性必須設定為**DelimitedTextSource**。 | Yes      |
| formatSettings | 屬性的群組。 請參閱下方的**分隔文字讀取設定**表格。 | No       |
| storeSettings  | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 | No       |

支援的**分隔文字讀取設定** `formatSettings` 如下：

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings 的類型必須設定為**DelimitedTextReadSettings**。 | Yes      |
| skipLineCount | 指出從輸入檔讀取資料時，要略過的**非空白**資料列數。 <br>如果同時指定 skipLineCount 和 firstRowAsHeader，則會先略過行，然後從輸入檔讀取標頭資訊。 | No       |
| compressionProperties | 一組屬性，說明如何將指定壓縮編解碼器的資料解壓縮。 | No       |
| preserveZipFileNameAsFolder<br>（*低於 `compressionProperties` *） | 適用于使用**ZipDeflate**壓縮設定輸入資料集時。 指出是否要在複製期間保留來源 ZIP 檔案名做為資料夾結構。 當設定為 true （預設值）時，Data Factory 會將解壓縮的檔案寫入 `<path specified in dataset>/<folder named as source zip file>/` ; 當設定為 false 時，Data Factory 會將解壓縮的檔案直接寫入至 `<path specified in dataset>` 。  | No |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>分隔的文字做為接收器

複製活動的 [ *** \* 接收 \* *** ] 區段支援下列屬性。

| 屬性       | 說明                                                  | 必要 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 複製活動來源的類型屬性必須設定為**DelimitedTextSink**。 | Yes      |
| formatSettings | 屬性的群組。 請參閱下方的**分隔文字寫入設定**表格。 |          |
| storeSettings  | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在下都有它自己的支援寫入設定 `storeSettings` 。  | No       |

支援的**分隔文字寫入設定** `formatSettings` 如下：

| 屬性      | 說明                                                  | 必要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的類型必須設定為**DelimitedTextWriteSettings**。 | Yes                                                   |
| fileExtension | 用來命名輸出檔的副檔名，例如 `.csv` `.txt` 。 當 `fileName` 輸出 DelimitedText 資料集中未指定時，必須指定它。 在輸出資料集中設定檔案名時，將會使用該名稱做為接收檔案名，而且將會忽略副檔名設定。  | 當輸出資料集中未指定檔案名時為是 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，您可以在下列資料存放區中讀取和寫入分隔的文字格式： [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>來源屬性

下表列出分隔文字來源所支援的屬性。 您可以在 [**來源選項**] 索引標籤中編輯這些屬性。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 萬用字元路徑 | 所有符合萬用字元路徑的檔案都會被處理。 覆寫資料集中所設定的資料夾和檔案路徑。 | 不可以 | String[] | wildcardPaths |
| 分割區根路徑 | 對於已分割的檔案資料，您可以輸入資料分割根路徑，以便將分割的資料夾當做資料行來讀取 | 不可以 | String | partitionRootPath |
| 檔案清單 | 您的來源是否指向列出要處理之檔案的文字檔 | 不可以 | `true` 或 `false` | fileList |
| 多行資料列 | 來源檔案包含跨越多行的資料列。 多行值必須以引號括住。 | 否 `true` 或`false` | multiLineRow |
| 要儲存檔案名稱的資料行 | 建立具有來原始檔案名和路徑的新資料行 | 不可以 | String | rowUrlColumn |
| 完成後 | 在處理之後刪除或移動檔案。 檔案路徑從容器根目錄開始 | 不可以 | Delete： `true` 或`false` <br> 進入`['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 依上次修改時間篩選 | 選擇根據上次修改檔案的時間來篩選檔案 | 不可以 | 時間戳記 | modifiedAfter <br> modifiedBefore |

### <a name="source-example"></a>來源範例

下圖是對應資料流程中分隔文字來源設定的範例。

![DelimitedText 來源](media/data-flow/delimited-text-source.png)

相關聯的資料流程腳本如下：

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

### <a name="sink-properties"></a>接收屬性

下表列出分隔的文字接收器所支援的屬性。 您可以在 [**設定**] 索引標籤中編輯這些屬性。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 清除資料夾 | 如果在寫入之前清除目的資料夾 | 不可以 | `true` 或 `false` | truncate |
| 檔案名選項 | 寫入之資料的命名格式。 根據預設，每個資料分割的一個檔案格式`part-#####-tid-<guid>` | 不可以 | 模式：字串 <br> 每個資料分割： String [] <br> 當做資料行中的資料：字串 <br> 輸出至單一檔案：`['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| 全部報價 | 以引號括住所有的值 | 不可以 | `true` 或 `false` | quoteAll |

### <a name="sink-example"></a>接收範例

下圖是對應資料流程中分隔文字接收設定的範例。

![DelimitedText 接收器](media/data-flow/delimited-text-sink.png)

相關聯的資料流程腳本如下：

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
