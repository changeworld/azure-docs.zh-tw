---
title: Azure 資料工廠中的 JSON 格式
description: 本主題介紹如何在 Azure 數據工廠中處理 JSON 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7b554ea5c2868559574979c58697fd31f8d2a2c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686272"
---
# <a name="json-format-in-azure-data-factory"></a>Azure 資料工廠中的 JSON 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您要**解析 JSON 檔或將資料寫入 JSON 格式時,** 請按照本文操作。 

JSON格式支援以下連接器:[亞馬遜S3,Azure](connector-amazon-simple-storage-service.md) [Blob,Azure數據存儲第1代](connector-azure-data-lake-store.md)[,Azure數據存儲第2代](connector-azure-data-lake-storage.md)[,Azure檔案存儲](connector-azure-file-storage.md),[檔案系統](connector-file-system.md)[,FTP,](connector-ftp.md)[Google雲存儲](connector-google-cloud-storage.md)[,HDFS,HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [Azure Blob](connector-azure-blob-storage.md) [HTTP](connector-http.md)

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 JSON 數據集支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Json**。 | 是      |
| location         | 檔的位置設置。 每個基於檔的連接器都有自己的位置類型和受支援的屬性。 `location` **請參考連接器文章 ->数据集属性部分的详细信息**。 | 是      |
| encodingName     | 用於讀取/寫入測試檔的編碼類型。 <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13","ISO-8859-15","WINDOWS-874","WINDOWS-1250","WINDOWS-1251","WINDOWS-1252","視窗-1253","視窗-1254","視窗-1255","視窗-1256","視窗-1257","視窗-1258"。| 否       |
| compression | 用於配置檔壓縮的屬性群組。 如果要在活動執行期間執行壓縮/解壓縮,請配置此部分。 | 否 |
| type | 用於讀取/寫入 JSON 檔的壓縮編解碼器。 <br>允許的值是 bzip2、gzip、deflate、ZipDeflate、snappy**ZipDeflate**或**lz4**。 **bzip2** **gzip** **deflate** **snappy** 保存檔時使用。 默認值不會壓縮。<br>**注意**目前複製活動不支援"快速"&"lz4",並且映射數據流不支援"ZipDeflate"。<br>**注意**使用複製活動來解壓縮 ZipDeflate 檔案並寫入檔案的接收器資料儲存時,檔案將擷取到`<path specified in dataset>/<folder named as source zip file>/`資料夾: 。 | 否。  |
| 層級 | 壓縮比。 <br>允許的值是**最佳**值或**最快**值。<br>- **最快:** 壓縮操作應儘快完成,即使生成的檔未以最佳方式壓縮也是如此。<br>- **最佳**: 壓縮操作應以最佳方式壓縮,即使操作需要更長的時間才能完成。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

下面是 Azure Blob 儲存的 JSON 資料集的範例:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
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
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 JSON 源和接收器支援的屬性清單。

### <a name="json-as-source"></a>JSON 作為源

複製活動***\*\*源***部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**JSONSource**。 | 是      |
| 商店設置 | 有關如何從數據存儲讀取數據的一組屬性。 每個基於檔的連接器在`storeSettings`下 都有自己的受支援的讀取設置。 **請參考連接器文章 -> 複製活動屬性部份的詳細資訊**。 | 否       |

### <a name="json-as-sink"></a>JSON 當作接收器

複製活動***\*接收\*器***部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源類型屬性必須設定為**JSONSink**。 | 是      |
| 格式設定 | 一組屬性。 請參閱下面的**JSON 寫入設置**表。 | 否       |
| 商店設置 | 有關如何將數據寫入數據存儲的一組屬性。 每個基於檔的連接器在`storeSettings`下 都有自己的支援的寫入設置。 **請參考連接器文章 -> 複製活動屬性部份的詳細資訊**。 | 否       |

支援的**JSON 寫入設定**下`formatSettings`:

| 屬性      | 描述                                                  | 必要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | 格式類型設定必須設定為**JsonWriteSettings**。 | 是                                                   |
| filePattern |表示每個 JSON 檔案中儲存的資料模式。 允許的值為︰**setOfObjects** 和 **arrayOfObjects**。 **預設值**為**物件物件集**。 關於這些模式的詳細資訊，請參閱 [JSON 檔案模式](#json-file-patterns)一節。 |否 |

### <a name="json-file-patterns"></a>JSON 檔案模式

複製活動可以自動檢測和分析 JSON 檔的以下模式。 

- **類型 I：setOfObjects**

    每個檔案都會包含單一物件，或以行分隔/串連的多個物件。 
    在複製活動接收器中選擇此選項時,複製活動將生成一個 JSON 檔,每行每個物件(行分隔)。

    * **單一物件 JSON 範例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **以行分隔的 JSON 範例**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **串連的 JSON 範例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **類型 II：arrayOfObjects**

    每個檔案都會包含物件的陣列。

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

JSON 檔案類型可用作映射數據流的接收器和源。

### <a name="creating-json-structures-in-a-derived-column"></a>在衍生的列中建立 JSON 結構

您可以通過派生的列運算式產生器向資料流添加複雜列。 在派生列轉換中,添加新列,並通過單擊藍色框打開表達式生成器。 要使列變得複雜,可以手動輸入 JSON 結構或使用 UX 以交互方式添加子列。

#### <a name="using-the-expression-builder-ux"></a>使用表示式產生器 UX

在輸出架構側窗格中,將滑鼠懸停在列上,然後單擊加號圖示。 選擇 **「添加子列**」以使該列成為複雜類型。

![新增子欄](media/data-flow/addsubcolumn.png "新增子欄")

可以以同樣的方式添加其他列和子列。 對於每個非複雜欄位,可以在右側的運算式編輯器中添加運算式。

![複雜欄](media/data-flow/complexcolumn.png "複雜欄")

#### <a name="entering-the-json-structure-manually"></a>手動輸入 JSON 結構

要手動添加 JSON 結構,請添加新列並在編輯器中輸入表達式。 表示式遵循以下一般格式:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

如果為名為「複雜列」的列輸入此表示式,則它將以以下 JSON 一詞寫入接收器:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完整的階層定義範例手動文稿
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>來源格式選項

使用 JSON 數據集作為數據流中的來源,可以設置五個附加設置。 可在 **「源選項**」選項卡中的 **「JSON 設定**手風琴」下找到這些設置。  

![JSON 設定](media/data-flow/json-settings.png "JSON 設定")

#### <a name="default"></a>預設

默認情況下,JSON 數據以以下格式讀取。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>單一文件

如果選擇了**單個文檔**,則映射數據流將讀取每個檔中的一個 JSON 文檔。 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
> [!NOTE]
> 如果數據流在預覽 JSON 數據時引發錯誤,指出"corrupt_record",則數據可能包含 JSON 檔中的單個文檔。 設置「單個文檔」應清除該錯誤。

#### <a name="unquoted-column-names"></a>未參考的欄名稱

如果選擇了**未引用的列名稱**,則映射數據流將讀取未由引號包圍的 JSON 列。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>有評論

如果 JSON 數據具有 C 或 C++樣式註解,請選擇 **「有註釋**」。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>單次報價

如果 JSON 欄位和值使用單引號而不是雙引號,請選擇 **「單引號**」。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>反斜杠逃跑

如果背斜杠用於轉義 JSON 數據中的字元,請選擇 **「單引號**」。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>後續步驟

- [複製活動概述](copy-activity-overview.md)
- [對應資料串流](concepts-data-flow-overview.md)
- [尋找活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
