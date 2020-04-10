---
title: Azure 資料工廠中分隔的文字格式
description: 本主題介紹如何處理 Azure 數據工廠中分隔的文本格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 47d26ad452b8494e591ee919076e5ade8bf19cd7
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011390"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure 資料工廠中分隔的文字格式

如果要**解析分隔的文本檔或將資料寫入分隔的文本格式**,請按照本文操作。 

支援以下連接器的分隔文字格式[:Amazon S3、Azure](connector-amazon-simple-storage-service.md) [Blob、Azure](connector-azure-blob-storage.md)[資料儲存湖儲存第 1 代](connector-azure-data-lake-store.md)[、Azure 資料儲存湖儲存第 2 代](connector-azure-data-lake-storage.md)[、Azure 檔案儲存](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)[、FTP、Google](connector-ftp.md)[雲端儲存](connector-google-cloud-storage.md)[、HDFS、HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [HTTP](connector-http.md)

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供分隔文本數據集支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設置為 **「分隔文字**」。 | 是      |
| location         | 檔的位置設置。 每個基於檔的連接器都有自己的位置類型和受支援的屬性。 `location`  | 是      |
| columnDelimiter  | 用於分隔檔案中列的字元。 <br>預設值為**逗`,`號**。 當列分隔符定義為空字串,這意味著沒有分隔符時,整行將視為單個列。<br>目前,列分隔符作為空字串或多字元僅支援映射數據流,而不支援複製活動。  | 否       |
| rowDelimiter     | 用於分隔檔中行的單個字元或"\r\n"。 <br>預設值是讀取上的以下任意值 **:"\r\n"、"\r"、"\n"*** 和 **"\n"或"\r\n",** 分別通過映射數據流和複製活動進行寫入。 <br>當行分隔符設定為無分隔符(空字串)時,列分隔符也必須設置為無分隔符(空字串),這意味著將整個內容視為單個值。<br>目前,僅支援將排分隔符作為空字串用於映射數據流,而不支援複製活動。 | 否       |
| quoteChar        | 單個字元引用列值(如果它包含列分隔符)。 <br>預設值為**雙引號**`"`。 <br>對於映射資料串流`quoteChar`, 不能為空字串。 <br>對於 Copy`quoteChar`活動 ,當定義為空字串時,這意味著沒有引號字元和列值不引用`escapeChar`,並且用於轉義列分隔符和自身。 | 否       |
| escapeChar       | 要在報價值內轉義引號的單個字元。<br>預設值為**反斜`\`槓**。 <br>對於映射資料串流`escapeChar`, 不能為空字串。 <br/>對於 Copy`escapeChar`活動 ,當定義為空字串`quoteChar`時, 也必須將 設定為空字串,在這種情況下,請確保所有列值不包含分隔符。 | 否       |
| firstRowAsHeader | 指定是否將第一行視為具有列名稱的標頭行。<br>允許的值**為真****和假**(預設值)。 | 否       |
| nullValue        | 指定 null 值的字串表示形式。 <br>預設值為**空字串**。 | 否       |
| encodingName     | 用於讀取/寫入測試檔的編碼類型。 <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13","ISO-8859-15","WINDOWS-874","WINDOWS-1250","WINDOWS-1251","WINDOWS-1252","視窗-1253","視窗-1254","視窗-1255","視窗-1256","視窗-1257","視窗-1258"。<br>請注意,映射數據流不支援 UTF-7 編碼。 | 否       |
| 壓縮碼 | 用於讀取/寫入文字檔的壓縮編解碼器。 <br>允許的值是 bzip2、gzip、deflate、ZipDeflate、snappy**ZipDeflate**或**lz4**。 **bzip2** **gzip** **deflate** **snappy** 默認值不會壓縮。 <br>**注意**目前複製活動不支援"快速"&"lz4",並且映射數據流不支援"ZipDeflate"。 <br>**注意**使用複製活動來解壓縮 ZipDeflate 檔案並寫入檔案的接收器資料儲存時,檔案將擷取到`<path specified in dataset>/<folder named as source zip file>/`資料夾: 。 | 否       |
| 壓縮層級 | 壓縮比。 <br>允許的值是**最佳**值或**最快**值。<br>- **最快:** 壓縮操作應儘快完成,即使生成的檔未以最佳方式壓縮也是如此。<br>- **最佳**: 壓縮操作應以最佳方式壓縮,即使操作需要更長的時間才能完成。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

下面是 Azure Blob 儲存上分隔的文字資料集的範例:

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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供分隔文本源和接收器支援的屬性清單。

### <a name="delimited-text-as-source"></a>將文字分隔為來源 

複製活動***\*\*源***部分支援以下屬性。

| 屬性       | 描述                                                  | 必要 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 複製活動來源類型屬性必須設定為 **「分隔文字來源**」。 | 是      |
| 格式設定 | 一組屬性。 請參閱下面的 **「分隔文字讀取設定」** 表。 | 否       |
| 商店設置  | 有關如何從數據存儲讀取數據的一組屬性。 每個基於檔的連接器在`storeSettings`下 都有自己的受支援的讀取設置。 | 否       |

支援的**分隔文字讀取設定**`formatSettings`在 :

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 格式類型設定必須設定為**分隔文字閱讀設定**。 | 是      |
| skipLineCount | 指出從輸入檔讀取資料時，要略過的**非空白**資料列數。 <br>如果同時指定 skipLineCount 和 firstRowAsHeader，則會先略過行，然後從輸入檔讀取標頭資訊。 | 否       |

### <a name="delimited-text-as-sink"></a>將文字分隔為接收器

複製活動***\*接收\*器***部分支援以下屬性。

| 屬性       | 描述                                                  | 必要 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 複製活動來源類型屬性必須設定為 **「分隔文字沉浸」。** | 是      |
| 格式設定 | 一組屬性。 請參閱下面的**分隔文字寫入設定**表。 |          |
| 商店設置  | 有關如何將數據寫入數據存儲的一組屬性。 每個基於檔的連接器在`storeSettings`下 都有自己的支援的寫入設置。  | 否       |

`formatSettings`支援**在下分隔的文字寫入設定**:

| 屬性      | 描述                                                  | 必要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | 格式類型設定必須設定**為分隔文字寫入設定**。 | 是                                                   |
| 檔案延伸 | 檔案檔案檔名, 例如`.csv`檔案的檔案副`.txt`檔名 。 必須在輸出分隔文字資料集中`fileName`未指定 時指定 。 在輸出資料集中配置檔名時,它將用作接收器檔名,文件副檔名設置將被忽略。  | 輸出資料集中未指定檔案名時為 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在映射資料流中從[源轉換](data-flow-source.md)和[接收器轉換](data-flow-sink.md)中瞭解詳細資訊。

## <a name="next-steps"></a>後續步驟

- [複製活動概述](copy-activity-overview.md)
- [對應資料串流](concepts-data-flow-overview.md)
- [尋找活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
