---
title: Azure Data Factory 中的分隔文字格式
description: 本主題說明如何在 Azure Data Factory 中處理分隔的文字格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: ac6540dfd86430aab518b145ed391d1d6283219e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276572"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory 中的分隔文字格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要 **剖析分隔符號文字檔，或將資料寫入分隔的文字格式**時，請遵循此文章。 

下列連接器支援分隔的文字格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、Azure 檔案 [儲存體](connector-azure-file-storage.md)、 [檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供分隔的文字資料集所支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的 type 屬性必須設為 **DelimitedText**。 | 是      |
| location         | 檔案 (s) 的位置設定。 每個以檔案為基礎的連接器都有自己的位置類型和支援的屬性 `location` 。  | 是      |
| columnDelimiter  |  (s 的字元) 用來分隔檔案中的資料行。 <br>預設值為**逗號 `,` **。 當資料行分隔符號定義為空字串時（亦即沒有分隔符號），會將整行當作單一資料行來取得。<br>目前，只支援對應資料流程，而不支援將資料行分隔符號作為空白字串或多字元的複製活動。  | 否       |
| rowDelimiter     | 單一字元或 "\r\n" 用來分隔檔案中的資料列。 <br>預設值是讀取時的下列任何值 **： ["\r\n"、"\r"、"\n"]** 和 **"\n" 或 "\r\n"** （依對應的資料流程和複製活動）。 <br>當資料列分隔符號設定為沒有分隔符號 (空白字串) 時，也必須將資料行分隔符號設為空字串)  (空白字串，這表示將整個內容視為單一值。<br>目前，僅支援對應資料流程，但不支援將資料列分隔符號作為空白字串。 | 否       |
| quoteChar        | 如果資料行的值包含資料行分隔符號，則為其引號的單一字元。 <br>預設值為 **雙引號** `"` 。 <br>針對對應資料流程， `quoteChar` 不可以是空字串。 <br>若為複製活動，當 `quoteChar` 定義為空字串時，即表示沒有引號字元和資料行值未加上引號，而且 `escapeChar` 會用來將資料行分隔符號和本身換行。 | 否       |
| escapeChar       | 要在加上引號的值內換用引號的單一字元。<br>預設值為**反斜線 `\` **。 <br>針對對應資料流程， `escapeChar` 不可以是空字串。 <br/>若為複製活動，當 `escapeChar` 定義為空字串時， `quoteChar` 也必須將設為空字串，在此情況下，請確定所有資料行值都不包含分隔符號。 | 否       |
| firstRowAsHeader | 指定是否將第一個資料列視為具有資料行名稱的標頭行。<br>允許的值為 **true** 和 **false** (預設) 。<br>當第一個資料列做為標頭時，請注意 UI 資料預覽和查閱活動輸出會自動產生資料行名稱做為 Prop_ {n} (從 0) 開始，複製活動需要從來源到接收的 [明確對應](copy-activity-schema-and-type-mapping.md#explicit-mapping) ，並依 (序數從 1) 開始找出資料行，並從 1 Column_ 開始找出名稱為 ({n}) 的資料行。  | 否       |
| nullValue        | 指定 null 值的字串表示。 <br>預設值為 **空字串**。 | 否       |
| encodingName     | 用來讀取/寫入測試檔的編碼類型。 <br>允許的值如下：「UTF-8」、「UTF-16」、「UTF-16BE」、「32 UTF-8」、「UTF-32BE」、「美國-ASCII」、「UTF-7」、「BIG5」、「EUC-JP」、「EUC-KR」、」 GB2312 "、" GB18030 "、" JOHAB "、" SHIFT-JIS "、" CP875 "、" CP866 "、" IBM00858 "、" IBM037 "、" IBM273 "、" IBM437 "、" IBM500 "、" IBM737 "、" IBM775 "、" IBM850 "、" IBM852 "、" IBM855 "、" IBM857 "、" IBM860 "和" IBM861 "、" IBM863 "、" IBM864 "、" IBM865 "、" IBM869 "、" IBM870 "、" IBM01140 "、" IBM01141 "、" IBM01142 "、" IBM01143 "、" IBM01144 "、" IBM01145 "、" IBM01146 "、" IBM01147 "、" IBM01148 "、" IBM01149 "、" ISO-2022-JP "，" ISO-2022-KR "，" ISO-8859-1 "，" ISO-8859-2 "，" ISO-8859-3 "，" ISO-8859-4 "，" ISO-8859-5 "，" ISO-8859-6 "，" ISO-8859-7 "，" iso-8859-8 "，" ISO-8859-9 "，" iso-8859-13 "，"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。<br>注意：對應資料流程不支援 UTF-7 編碼。 | 否       |
| compressionCodec | 用來讀取/寫入文字檔的壓縮編解碼器。 <br>允許的值為 **bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **TarGzip**、 **snappy**或 **lz4**。 預設值不會壓縮。 <br>**注意：** 目前的複製活動不支援 "snappy" & "lz4"，而且對應資料流程不支援 "ZipDeflate"。 <br>**請注意**，使用複製活動來將**ZipDeflate** / **TarGzip**檔解壓縮 (s) 並寫入至檔案型接收資料存放區時，預設會將檔案解壓縮到資料夾： `<path specified in dataset>/<folder named as source compressed file>/` ， `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` 在[複製活動來源](#delimited-text-as-source)上使用，以控制是否要將壓縮檔案的名稱保留為資料夾結構 () 。 | 否       |
| compressionLevel | 壓縮比例。 <br>允許的值為 **最佳** 或 **最快速**。<br>- **最快：** 即使產生的檔案未以最佳方式壓縮，壓縮作業也應該儘快完成。<br>- **最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供分隔文字來源和接收所支援的屬性清單。

### <a name="delimited-text-as-source"></a>分隔文字作為來源 

[複製活動*** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性       | 描述                                                  | 必要 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 複製活動來源的 type 屬性必須設為 **DelimitedTextSource**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下方的 **分隔文字讀取設定** 表格。 |  否       |
| storeSettings  | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 | 否       |

支援的 **分隔文字讀取設定** `formatSettings` 如下：

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings 的類型必須設定為 **DelimitedTextReadSettings**。 | 是      |
| skipLineCount | 指出從輸入檔讀取資料時，要略過的**非空白**資料列數。 <br>如果同時指定 skipLineCount 和 firstRowAsHeader，則會先略過行，然後從輸入檔讀取標頭資訊。 | 否       |
| compressionProperties | 一組屬性，說明如何將指定壓縮編解碼器的資料解壓縮。 | 否       |
| preserveZipFileNameAsFolder<br> (*下 `compressionProperties` -> `type` 的 `ZipDeflateReadSettings` *)  |  適用于設定 **ZipDeflate** 壓縮的輸入資料集時。 指出複製期間是否要將來源 ZIP 檔案名保留為資料夾結構。<br>-當設定為 **true (預設) **時，Data Factory 會將解壓縮的檔案寫入 `<path specified in dataset>/<folder named as source zip file>/` 。<br>-當設定為 **false**時，Data Factory 會將解壓縮的檔案直接寫入至 `<path specified in dataset>` 。 請確定您在不同的來源 zip 檔案中沒有重複的檔案名，以避免發生賽車或非預期的行為。  | 否 |
| preserveCompressionFileNameAsFolder<br> (*下 `compressionProperties` -> `type` 的 `TarGZipReadSettings` *)   | 適用于設定 **TarGzip** 壓縮的輸入資料集時。 指出複製期間是否要將來源壓縮檔案名稱保留為資料夾結構。<br>-當設定為 **true (預設) **時，Data Factory 會將解壓縮檔案寫入至 `<path specified in dataset>/<folder named as source compressed file>/` 。 <br>-當設定為 **false**時，Data Factory 會將解壓縮的檔案直接寫入至 `<path specified in dataset>` 。 請確定您在不同的來源檔案中沒有重複的檔案名，以避免發生賽車或非預期的行為。 | 否 |

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

### <a name="delimited-text-as-sink"></a>分隔的文字作為接收器

複製活動*** \* 接收 \* ***區段支援下列屬性。

| 屬性       | 描述                                                  | 必要 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 複製活動來源的 type 屬性必須設為 **DelimitedTextSink**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下面的 **分隔文字寫入設定** 表格。 |    否      |
| storeSettings  | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在下都有自己支援的寫入設定 `storeSettings` 。  | 否       |

以下是支援的 **分隔文字寫入設定** `formatSettings` ：

| 屬性      | 描述                                                  | 必要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的類型必須設定為 **DelimitedTextWriteSettings**。 | 是                                                   |
| fileExtension | 用來命名輸出檔的副檔名，例如， `.csv` `.txt` 。 當在 `fileName` Output DelimitedText 資料集中未指定時，必須指定此資料集。 在輸出資料集中設定檔案名時，將會使用此名稱做為接收檔案名，而且會忽略副檔名設定。  | 如果未在輸出資料集中指定檔案名，則為 Yes |
| maxRowsPerFile | 將資料寫入資料夾時，您可以選擇寫入多個檔案，並指定每個檔案的最大資料列數。  | 否 |
| fileNamePrefix | 適用于 `maxRowsPerFile` 設定時。<br> 將資料寫入多個檔案時，請指定檔案名前置詞，而導致此模式： `<fileNamePrefix>_00000.<fileExtension>` 。 如果未指定，則會自動產生檔案名前置詞。 當來源是以檔案為基礎的存放區或分割區 [選項的資料存放區](copy-activity-performance-features.md)時，此屬性不適用。  | 否 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，您可以在下列資料存放區中讀取和寫入分隔的文字格式： [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>來源屬性

下表列出分隔文字來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 萬用字元路徑 | 將會處理所有符合萬用字元路徑的檔案。 覆寫資料集中設定的資料夾和檔案路徑。 | 否 | String[] | wildcardPaths |
| 分割區根路徑 | 針對已分割的檔案資料，您可以輸入磁碟分割根路徑，以便將分割的資料夾讀取為數據行 | 否 | 字串 | partitionRootPath |
| 檔案清單 | 您的來源是否指向列出要處理之檔案的文字檔 | 否 | `true` 或 `false` | fileList |
| 多行資料列 | 來源檔案是否包含跨多行的資料列。 多行值必須以引號括住。 | 否 `true` 或 `false` | multiLineRow |
| 儲存檔案名稱的資料行 | 使用來原始檔案名和路徑建立新的資料行 | 否 | 字串 | rowUrlColumn |
| 完成後 | 在處理之後刪除或移動檔案。 從容器根目錄開始的檔案路徑 | 否 | Delete： `true` 或 `false` <br> 移動： `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 依上次修改篩選 | 選擇根據上次修改檔案的時間進行篩選 | 否 | 時間戳記 | modifiedAfter <br> modifiedBefore |
| 不允許找到任何檔案 | 若為 true，如果找不到任何檔案，就不會擲回錯誤 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="source-example"></a>來源範例

下圖是對應資料流程中的分隔文字來源設定範例。

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

下表列出分隔的文字接收所支援的屬性。 您可以在 [ **設定** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 清除資料夾 | 如果在寫入之前清除目的資料夾 | 否 | `true` 或 `false` | truncate |
| [檔案名] 選項 | 寫入之資料的命名格式。 依預設，每個資料分割的一個檔案格式為 `part-#####-tid-<guid>` | 否 | 模式：字串 <br> 每個分割區：字串 [] <br> 做為資料行中的資料：字串 <br> 輸出至單一檔案： `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| 全部報價 | 以引號括住所有值 | 否 | `true` 或 `false` | quoteAll |

### <a name="sink-example"></a>接收器範例

下圖是對應資料流程中的分隔文字接收設定範例。

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
