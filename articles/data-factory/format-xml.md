---
title: Azure Data Factory 中的 XML 格式
description: 本主題描述如何在 Azure Data Factory 中處理 XML 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: 49c44b17247f14b8826df7652dc9eb025953b748
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097467"
---
# <a name="xml-format-in-azure-data-factory"></a>Azure Data Factory 中的 XML 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要**剖析 XML**檔案時，請遵循這篇文章。 

下列連接器支援 XML 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。 它支援作為來源，但不是接收。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 XML 資料集所支援的屬性清單。

| 屬性         | 說明                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Xml**。 | 是      |
| location         | 檔案的位置設定。 每個以檔案為基礎的連接器在下都有自己的位置類型和支援的屬性 `location` 。 **請參閱連接器文章-> 資料集屬性一節中的詳細資訊**。 | 是      |
| encodingName     | 用來讀取/寫入測試檔案的編碼類型。 <br>允許的值如下： "UTF-8"、"UTF-16"、"UTF-UTF-16BE"、"UTF-32"、"32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"，"GB18030"，"JOHAB"，"SHIFT-JIS"，"CP875"，"CP866"，"IBM00858"，"IBM037"，"IBM273"，"IBM437"，"IBM500"，"IBM737"，"IBM775"，"IBM850"，"IBM852"，"IBM855"，"IBM857"，"IBM860"，"IBM861"，"IBM863"，"IBM864"，"IBM865"，"IBM869"，"IBM870"，"IBM01140"，"IBM01141"，"IBM01142"，"IBM01143"，"IBM01144"，"IBM01145"，"IBM01146"，"IBM01147"，"IBM01148"，"IBM01149"，"ISO-2022-JP"，"ISO-2022-KR"，"ISO-8859-1"，"ISO-8859-2"，"ISO-8859-3"，"ISO-8859-4"，"ISO-8859-5"，"ISO-8859-6"，"ISO-8859-7"，"iso-8859-8"，"iso-8859-9"，"ISO-8859-13"、「ISO-8859-15」、「WINDOWS-874」、「WINDOWS-1250」、「WINDOWS-1251」、「WINDOWS-1252」、「WINDOWS-1253」、「WINDOWS-1254」、「WINDOWS-1255」、「WINDOWS-1256」、「WINDOWS-1257」、「WINDOWS-1258」。| 否       |
| nullValue | 指定 null 值的字串表示。<br/>預設值為**空字串**。 | 否 |
| compression | 用來設定檔案壓縮的屬性群組。 當您想要在活動執行期間執行壓縮/解壓縮時，請設定此區段。 | 否 |
| 類型<br>（*低於 `compression` *） | 用來讀取/寫入 XML 檔案的壓縮編解碼器。 <br>允許的值為**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **snappy**或**lz4**。 表示在儲存檔案時使用。 預設值為 [未壓縮]。<br>**注意：** 目前的複製活動不支援 "snappy" & "lz4"，而且對應資料流程不支援 "ZipDeflate"。<br>**請注意**，使用複製活動來解壓縮**ZipDeflate**檔案並寫入以檔案為基礎的接收資料存放區時，預設會將檔案解壓縮至資料夾： `<path specified in dataset>/<folder named as source zip file>/` ， `preserveZipFileNameAsFolder` 在[複製活動來源](#xml-as-source)上使用，以控制是否要將 ZIP 檔案名保留為資料夾結構。 | 不可以。  |
| 等級<br/>（*低於 `compression` *） | 壓縮比例。 <br>允許的值為**最佳**或**最快**。<br>- **最快速：** 即使產生的檔案未以最佳方式壓縮，壓縮作業也應該儘快完成。<br>- **最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

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

瞭解如何從[架構對應](copy-activity-schema-and-type-mapping.md)對應 XML 資料和接收資料存放區/格式。 預覽 XML 檔案時，資料會與 JSON 階層一起顯示，而您會使用 JSON 路徑來指向這些欄位。

### <a name="xml-as-source"></a>XML 作為來源

複製活動的 [ *** \* 來源 \* *** ] 區段支援下列屬性。 深入瞭解[XML 連接器行為](#xml-connector-behavior)。

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**XmlSource**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下方的**XML 讀取設定**表格。 | 否       |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | 否       |

支援的**XML 讀取設定** `formatSettings` ：

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings 的類型必須設定為**XmlReadSettings**。 | 是      |
| validationMode | 指定是否要驗證 XML 架構。<br>允許的值為**none** （預設值，無驗證）、 **XSD** （使用 xsd 進行驗證）、 **dtd** （使用 dtd 進行驗證）。 | 否 |
| namespacePrefixes | 前置詞對應的命名空間 URI，在剖析 xml 檔案時用來命名欄位。<br/>如果 XML 檔案已啟用命名空間和命名空間，則根據預設，功能變數名稱與 XML 檔中的名稱相同。<br>如果此對應中的命名空間 URI 有定義的專案，則功能變數名稱為 `prefix:fieldName` 。 | 否 |
| compressionProperties | 一組屬性，說明如何將指定壓縮編解碼器的資料解壓縮。 | 否       |
| preserveZipFileNameAsFolder<br>（*低於 `compressionProperties` *） | 適用于使用**ZipDeflate**壓縮設定輸入資料集時。 指出是否要在複製期間保留來源 ZIP 檔案名做為資料夾結構。 當設定為 true （預設值）時，Data Factory 會將解壓縮的檔案寫入 `<path specified in dataset>/<folder named as source zip file>/` ; 當設定為 false 時，Data Factory 會將解壓縮的檔案直接寫入至 `<path specified in dataset>` 。  | 否 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，您可以在下列資料存放區中讀取和寫入 XML 格式： [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。 您可以使用 XML 資料集或使用[內嵌資料集](data-flow-source.md#inline-datasets)來指向 XML 檔案。

### <a name="source-properties"></a>來源屬性

下表列出 XML 來源所支援的屬性。 您可以在 [**來源選項**] 索引標籤中編輯這些屬性。深入瞭解[XML 連接器行為](#xml-connector-behavior)。 使用內嵌資料集時，您會看到與[資料集屬性](#dataset-properties)一節中所述屬性相同的其他檔案設定。 

| 名稱 | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 萬用字元路徑 | 所有符合萬用字元路徑的檔案都會被處理。 覆寫資料集中所設定的資料夾和檔案路徑。 | 否 | String[] | wildcardPaths |
| 分割區根路徑 | 對於已分割的檔案資料，您可以輸入資料分割根路徑，以便將分割的資料夾當做資料行來讀取 | 否 | String | partitionRootPath |
| 檔案清單 | 您的來源是否指向列出要處理之檔案的文字檔 | 否 | `true` 或 `false` | fileList |
| 要儲存檔案名稱的資料行 | 建立具有來原始檔案名和路徑的新資料行 | 否 | String | rowUrlColumn |
| 完成後 | 在處理之後刪除或移動檔案。 檔案路徑從容器根目錄開始 | 否 | Delete： `true` 或`false` <br> 進入`['<from>', '<to>']` | purgeFiles <br>moveFiles |
| 依上次修改時間篩選 | 選擇根據上次修改檔案的時間來篩選檔案 | 否 | Timestamp | modifiedAfter <br>modifiedBefore |
| 驗證模式 | 指定是否要驗證 XML 架構。 | 否 | `None`（預設值，無驗證）<br>`xsd`（使用 XSD 進行驗證）<br>`dtd`（使用 DTD 進行驗證）。 | validationMode |
| 命名空間 | 剖析 XML 檔案時是否要啟用命名空間。 | 否 | `true`（預設）或`false` | 命名空間 |
| 命名空間前置詞配對 | 前置詞對應的命名空間 URI，在剖析 xml 檔案時用來命名欄位。<br/>如果 XML 檔案已啟用命名空間和命名空間，則根據預設，功能變數名稱與 XML 檔中的名稱相同。<br>如果此對應中的命名空間 URI 有定義的專案，則功能變數名稱為 `prefix:fieldName` 。 | 否 | 具有模式的陣列`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |

### <a name="xml-source-script-example"></a>XML 來源腳本範例

下列腳本是使用資料集模式對應資料流程中的 XML 來源設定範例。

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

    - 元素的屬性會剖析為階層中專案的子欄位。
    - 屬性欄位的名稱會遵循模式 `@attributeName` 。

- XML 架構驗證：

    - 您可以選擇不要驗證架構，或使用 XSD 或 DTD 來驗證架構。
    - 使用 XSD 或 DTD 來驗證 XML 檔案時，XSD/DTD 必須透過相對路徑在 XML 檔案內參考。

- 命名空間處理：

    - 使用資料流程時可以停用命名空間，在此情況下，定義命名空間的屬性將會剖析為一般屬性。
    - 當命名空間啟用時，元素和屬性的名稱會遵循模式 `namespaceUri,elementName` 和 `namespaceUri,@attributeName` 預設值。 您可以在來源中定義每個命名空間 URI 的命名空間前置詞，在此情況下，專案和屬性的名稱會遵循模式 `definedPrefix:elementName` 或 `definedPrefix:@attributeName` 改為。

- 值資料行：

    - 如果 XML 元素同時具有簡單的文字值和屬性/子項目，則會將簡單的文字值剖析為具有內建功能變數名稱之「值資料行」的值 `_value_` 。 而且，它也會繼承專案的命名空間（如果適用的話）。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
