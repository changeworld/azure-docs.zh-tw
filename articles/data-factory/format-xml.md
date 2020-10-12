---
title: Azure Data Factory 中的 XML 格式
description: 本主題說明如何在 Azure Data Factory 中處理 XML 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: e0fadf4ac8cea1c8804b17f5549a99bc360e2950
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334288"
---
# <a name="xml-format-in-azure-data-factory"></a>Azure Data Factory 中的 XML 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要 **剖析 XML**檔案時，請遵循這篇文章。 

下列連接器支援 XML 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、 [檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。 它支援作為來源，但不支援接收。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 XML 資料集所支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的 type 屬性必須設定為 **Xml**。 | 是      |
| location         | 檔案 (s) 的位置設定。 每個以檔案為基礎的連接器都有自己的位置類型和支援的屬性 `location` 。 **請參閱連接器文章中的詳細資料-> 資料集屬性一節**。 | 是      |
| encodingName     | 用來讀取/寫入測試檔的編碼類型。 <br>允許的值如下：「UTF-8」、「UTF-16」、「UTF-16BE」、「32 UTF-8」、「UTF-32BE」、「美國-ASCII」、「UTF-7」、「BIG5」、「EUC-JP」、「EUC-KR」、」 GB2312 "、" GB18030 "、" JOHAB "、" SHIFT-JIS "、" CP875 "、" CP866 "、" IBM00858 "、" IBM037 "、" IBM273 "、" IBM437 "、" IBM500 "、" IBM737 "、" IBM775 "、" IBM850 "、" IBM852 "、" IBM855 "、" IBM857 "、" IBM860 "和" IBM861 "、" IBM863 "、" IBM864 "、" IBM865 "、" IBM869 "、" IBM870 "、" IBM01140 "、" IBM01141 "、" IBM01142 "、" IBM01143 "、" IBM01144 "、" IBM01145 "、" IBM01146 "、" IBM01147 "、" IBM01148 "、" IBM01149 "、" ISO-2022-JP "，" ISO-2022-KR "，" ISO-8859-1 "，" ISO-8859-2 "，" ISO-8859-3 "，" ISO-8859-4 "，" ISO-8859-5 "，" ISO-8859-6 "，" ISO-8859-7 "，" iso-8859-8 "，" ISO-8859-9 "，" iso-8859-13 "，"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。| 否       |
| nullValue | 指定 null 值的字串表示。<br/>預設值為 **空字串**。 | 否 |
| compression | 用來設定檔案壓縮的屬性群組。 當您想要在活動執行期間執行壓縮/解壓縮時，請設定此區段。 | 否 |
| type<br>) *下 `compression` *的 ( | 用來讀取/寫入 XML 檔案的壓縮編解碼器。 <br>允許的值為 **bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **TarGzip**、 **snappy**或 **lz4**。 預設值不會壓縮。<br>**注意：** 目前的複製活動不支援 "snappy" & "lz4"，而且對應資料流程不支援 "ZipDeflate"。<br>**請注意**，使用複製活動來將**ZipDeflate** / **TarGzip**檔解壓縮 (s) 並寫入至檔案型接收資料存放區時，預設會將檔案解壓縮到資料夾： `<path specified in dataset>/<folder named as source compressed file>/` ， `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` 在[複製活動來源](#xml-as-source)上使用，以控制是否要將壓縮檔案的名稱保留為資料夾結構 () 。 | 否。  |
| 等級<br/>) *下 `compression` *的 ( | 壓縮比例。 <br>允許的值為 **最佳** 或 **最快速**。<br>- **最快：** 即使產生的檔案未以最佳方式壓縮，壓縮作業也應該儘快完成。<br>- **最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

以下是 Azure Blob 儲存體上的 XML 資料集範例：

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 XML 來源所支援的屬性清單。

瞭解如何從 [架構對應](copy-activity-schema-and-type-mapping.md)對應 XML 資料和接收資料存放區/格式。 預覽 XML 檔案時，資料會以 JSON 階層顯示，而您會使用 JSON 路徑指向欄位。

### <a name="xml-as-source"></a>XML 作為來源

[複製活動*** \* 來源 \* *** ] 區段支援下列屬性。 深入瞭解 [XML 連接器行為](#xml-connector-behavior)。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **XmlSource**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下方的 **XML 讀取設定** 表格。 | 否       |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

支援的 **XML 讀取設定** `formatSettings` ：

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings 的類型必須設定為 **XmlReadSettings**。 | 是      |
| validationMode | 指定是否要驗證 XML 架構。<br>允許的值為 **none** (預設值、無驗證) 、 **XSD** (使用 xsd) 進行驗證、 **dtd** (使用 dtd) 進行驗證。 | 否 |
| 命名空間 | 剖析 XML 檔案時是否啟用命名空間。 允許的值為：**true** (預設值)、**false**。 | 否 |
| namespacePrefixes | 命名空間 URI 與前置詞的對應，用來在剖析 xml 檔案時為欄位命名。<br/>如果 XML 檔案已啟用命名空間和命名空間，則根據預設，功能變數名稱會與 XML 檔中的名稱相同。<br>如果此對應中有定義命名空間 URI 的專案，則功能變數名稱為 `prefix:fieldName` 。 | 否 |
| detectDataType | 是否要偵測整數、雙精度浮點數和布林資料類型。 允許的值為：**true** (預設值)、**false**。| 否 |
| compressionProperties | 一組屬性，說明如何將指定壓縮編解碼器的資料解壓縮。 | 否       |
| preserveZipFileNameAsFolder<br> (*下 `compressionProperties` -> `type` 的 `ZipDeflateReadSettings` *)   | 適用于設定 **ZipDeflate** 壓縮的輸入資料集時。 指出複製期間是否要將來源 ZIP 檔案名保留為資料夾結構。<br>-當設定為 **true (預設) **時，Data Factory 會將解壓縮的檔案寫入 `<path specified in dataset>/<folder named as source zip file>/` 。<br>-當設定為 **false**時，Data Factory 會將解壓縮的檔案直接寫入至 `<path specified in dataset>` 。 請確定您在不同的來源 zip 檔案中沒有重複的檔案名，以避免發生賽車或非預期的行為。  | 否 |
| preserveCompressionFileNameAsFolder<br> (*下 `compressionProperties` -> `type` 的 `TarGZipReadSettings` *)  | 適用于設定 **TarGzip** 壓縮的輸入資料集時。 指出複製期間是否要將來源壓縮檔案名稱保留為資料夾結構。<br>-當設定為 **true (預設) **時，Data Factory 會將解壓縮檔案寫入至 `<path specified in dataset>/<folder named as source compressed file>/` 。 <br>-當設定為 **false**時，Data Factory 會將解壓縮的檔案直接寫入至 `<path specified in dataset>` 。 請確定您在不同的來源檔案中沒有重複的檔案名，以避免發生賽車或非預期的行為。 | 否 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，您可以在下列資料存放區中讀取和寫入 XML 格式： [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。 您可以使用 XML 資料集或使用 [內嵌資料集](data-flow-source.md#inline-datasets)來指向 xml 檔案。

### <a name="source-properties"></a>來源屬性

下表列出 XML 來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。深入瞭解 [XML 連接器行為](#xml-connector-behavior)。 使用內嵌資料集時，您將會看到其他檔案設定，這與 [ [資料集屬性](#dataset-properties) ] 區段中所述的屬性相同。 

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 萬用字元路徑 | 將會處理所有符合萬用字元路徑的檔案。 覆寫資料集中設定的資料夾和檔案路徑。 | 否 | String[] | wildcardPaths |
| 分割區根路徑 | 針對已分割的檔案資料，您可以輸入磁碟分割根路徑，以便將分割的資料夾讀取為數據行 | 否 | String | partitionRootPath |
| 檔案清單 | 您的來源是否指向列出要處理之檔案的文字檔 | 否 | `true` 或 `false` | fileList |
| 儲存檔案名稱的資料行 | 使用來原始檔案名和路徑建立新的資料行 | 否 | String | rowUrlColumn |
| 完成後 | 在處理之後刪除或移動檔案。 從容器根目錄開始的檔案路徑 | 否 | Delete： `true` 或 `false` <br> 移動： `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| 依上次修改篩選 | 選擇根據上次修改檔案的時間進行篩選 | 否 | 時間戳記 | modifiedAfter <br>modifiedBefore |
| 驗證模式 | 指定是否要驗證 XML 架構。 | 否 | `None` (預設，沒有驗證) <br>`xsd` (使用 XSD 進行驗證) <br>`dtd` (使用 DTD) 進行驗證。 | validationMode |
| 命名空間 | 剖析 XML 檔案時是否啟用命名空間。 | 否 | `true` (預設) 或 `false` | 命名空間 |
| 命名空間前置詞配對 | 命名空間 URI 與前置詞的對應，用來在剖析 xml 檔案時為欄位命名。<br/>如果 XML 檔案已啟用命名空間和命名空間，則根據預設，功能變數名稱會與 XML 檔中的名稱相同。<br>如果此對應中有定義命名空間 URI 的專案，則功能變數名稱為 `prefix:fieldName` 。 | 否 | 具有模式的陣列`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| 不允許找到任何檔案 | 若為 true，如果找不到任何檔案，就不會擲回錯誤 | 否 | `true` 或 `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>XML 來源腳本範例

下列腳本是使用資料集模式對應資料流程的 XML 來源設定範例。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

下列腳本是使用內嵌資料集模式之 XML 來源設定的範例。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>XML 連接器行為

使用 XML 做為來源時，請注意下列事項。

- XML 屬性：

    - 專案的屬性會剖析為階層中元素的子欄位。
    - 屬性欄位的名稱會遵循模式 `@attributeName` 。

- XML 架構驗證：

    - 您可以選擇不要驗證架構，或使用 XSD 或 DTD 來驗證架構。
    - 使用 XSD 或 DTD 驗證 XML 檔案時，必須透過相對路徑在 XML 檔案內參考 XSD/DTD。

- 命名空間處理：

    - 使用資料流程時，可以停用命名空間，在此情況下，定義命名空間的屬性會剖析為一般屬性。
    - 啟用命名空間時，元素和屬性的名稱會遵循模式       `namespaceUri,elementName` 和 `namespaceUri,@attributeName` 預設值。 您可以針對來源中的每個命名空間 URI 定義命名空間前置詞，在這種情況下，元素和屬性的名稱會遵循模式 `definedPrefix:elementName` 或 `definedPrefix:@attributeName` 。

- 值資料行：

    - 如果 XML 專案同時具有簡單的文字值和屬性/子項目，則會使用內建的功能變數名稱將簡單的文字值剖析為「值資料行」的值 `_value_` 。 而且，它也會繼承元素的命名空間（如果適用）。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
