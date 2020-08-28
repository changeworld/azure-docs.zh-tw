---
title: 使用 Azure Data Factory 從檔案系統來回複製資料
description: 了解如何使用 Azure Data Factory 將資料從檔案系統複製到支援的接收資料存放區，或是從支援的來源資料存放區複製到檔案系統。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jingwang
ms.openlocfilehash: 6b0ed1f843b5557e472f5e71538ae32a76b70fe9
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051197"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到檔案系統或從該處複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-onprem-file-system-connector.md)
> * [目前的版本](connector-file-system.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何從檔案系統來回複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此檔案系統連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，這個檔案系統連接器支援：

- 從本機電腦或網路檔案共用複製檔案，或將檔案複製到這兩處。 若要使用 Linux 檔案共用，請在您的 Linux 伺服器上安裝 [Samba](https://www.samba.org/)。
- 使用 **Windows** 驗證來複製檔案。
- 依原樣複製檔案，或使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析/產生檔案。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義檔案系統專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對檔案系統已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**FileServer**。 | 是 |
| 主機 | 指定想要複製之資料夾的根路徑。 針對字串中的特殊字元，請使用逸出字元 "\"。 如需範例，請參閱 [範例連結服務和資料集定義](#sample-linked-service-and-dataset-definitions) 。 | 是 |
| userid | 指定具有伺服器存取權之使用者的識別碼。 | 是 |
| 密碼 | 指定使用者 (userid) 的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

### <a name="sample-linked-service-and-dataset-definitions"></a>範例連結服務和資料集定義

| 狀況 | 已連結服務定義中的 "host" | 資料集定義中的 "folderPath" |
|:--- |:--- |:--- |
| Integration Runtime 電腦上的本機資料夾： <br/><br/>範例：D:\\\* 或 D:\folder\subfolder\\* |JSON 中：`D:\\`<br/>UI 上：`D:\` |JSON 中：`.\\` 或 `folder\\subfolder`<br>UI 上：`.\` 或 `folder\subfolder` |
| 遠端共用資料夾︰ <br/><br/>範例︰\\\\myserver\\share\\\* 或 \\\\myserver\\share\\folder\\subfolder\\\* |JSON 中：`\\\\myserver\\share`<br/>UI 上：`\\myserver\share` |JSON 中：`.\\` 或 `folder\\subfolder`<br/>UI 上：`.\` 或 `folder\subfolder` |

>[!NOTE]
>透過 UI 撰寫時，您不需要像透過 JSON 那樣輸入雙反斜線 (`\\`) 來逸出，只需指定單一反斜線。

**範例︰**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型資料集的 `location` 設定下，檔案系統支援下列屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 在資料集的 `location` 下，type 屬性必須設定為 **FileServerLocation**。 | 是      |
| folderPath | 資料夾的路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在活動來源設定中指定。 | 否       |
| fileName   | 所指定 folderPath 下的檔案名稱。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在活動來源設定中指定。 | 否       |

**範例︰**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供檔案系統來源和接收器所支援的屬性清單。

### <a name="file-system-as-source"></a>檔案系統作為來源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型複製來源的 `storeSettings` 設定下，檔案系統支援下列屬性：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **FileServerReadSettings**。 | 是                                           |
| 找出要複製的檔案： |  |  |
| 選項 1：靜態路徑<br> | 從資料集內指定的特定資料夾/檔案路徑複製。 如果您想要複製資料夾中的所有檔案，請另外將 `wildcardFileName` 指定為 `*`。 |  |
| 選項2：伺服器端篩選<br>-fileFilter  | 檔案伺服器端原生篩選器，可提供比選項3萬用字元篩選器更佳的效能。 使用 `*` 可比對零或多個字元，並 `?` 符合零或單一字元。 深入瞭解[本節](https://docs.microsoft.com/dotnet/api/system.io.directory.getfiles?view=netframework-4.7.2#System_IO_Directory_GetFiles_System_String_System_String_System_IO_SearchOption_)下的**備註**中的語法和注意事項。 | 否                                                          |
| 選項3：用戶端篩選<br>- wildcardFolderPath | 含有萬用字元的資料夾路徑，可用來篩選來源資料夾。 這類篩選會在 ADF 端進行，ADF 會列舉指定路徑底下的資料夾/檔案，然後套用萬用字元篩選。<br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項3：用戶端篩選<br>- wildcardFileName | 在指定 folderPath/wildcardFolderPath 之下，含有萬用字元的檔案名稱，可用來篩選來源檔案。 這類篩選會在 ADF 端進行，ADF 會列舉指定路徑下的檔案，然後套用萬用字元篩選準則。<br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。<br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 3：檔案清單<br>- fileListPath | 表示要複製指定的檔案集。 指向文字檔，其中包含您要複製的檔案清單，每行一個檔案，也就是在資料集中設定之路徑的相對路徑。<br/>使用此選項時，請勿指定資料集中的檔案名稱。 [檔案清單範例](#file-list-examples) (英文) 有更多範例可供參閱。 |否 |
| 其他設定： |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 <br>允許的值為 **true** (預設值) 和 **false**。<br>設定 `fileListPath` 時，不適用此屬性。 |否 |
| deleteFilesAfterCompletion | 指出是否要在成功移至目的地存放區之後，從來源存放區刪除二進位檔案。 檔案刪除是針對每個檔案，因此當複製活動失敗時，您會看到部分檔案已複製到目的地並從來源刪除，其他檔案仍在來源存放區上。 <br/>這個屬性只適用于二進位複製案例，其中資料來源存放區為 Blob、ADLS Gen1、ADLS Gen2、S3、Google Cloud Storage、檔案、Azure 檔案、SFTP 或 FTP。 預設值： false。 |否 |
| modifiedDatetimeStart    | 檔案篩選會根據以下屬性：上次修改時間。 <br>若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。<br/>設定 `fileListPath` 時，不適用此屬性。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| enablePartitionDiscovery | 針對已分割的檔案，指定是否從檔案路徑剖析分割區，並將它們新增為其他來源資料行。<br/>允許的值為 **false** (預設) 和 **true**。 | 否                                            |
| partitionRootPath | 當資料分割探索已啟用時，請指定絕對根路徑，以便將分割的資料夾讀取為數據行。<br/><br/>如果未指定，則預設為<br/>-當您在資料集或來源上的檔案清單中使用檔案路徑時，資料分割根路徑是在資料集中設定的路徑。<br/>-當您使用萬用字元資料夾篩選時，資料分割根路徑是第一個萬用字元之前的子路徑。<br/><br/>例如，假設您將資料集中的路徑設定為 "root/folder/year = 2020/month = 08/day = 27"：<br/>-如果您將資料分割根路徑指定為 "root/folder/year = 2020"，則除了檔案內的資料行之外，複製活動會分別產生兩個數據行， `month` 以及 `day` 值為 "08" 和 "27" 的資料行。<br/>-如果未指定資料分割根路徑，將不會產生額外的資料行。 | 否                                            |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有想要限制對資料存放區的並行連線數目時，才須指定此項。 | 否                                            |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FileServerReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="file-system-as-sink"></a>檔案系統作為接收器

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

在格式型複製接收的 `storeSettings` 設定下，檔案系統支援下列屬性：

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **FileServerWriteSettings**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 可同時連線到資料存放區的連線數目。 只有想要限制對資料存放區的並行連線數目時，才須指定此項。 | 否       |

**範例︰**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>資料夾和檔案篩選範例

本節描述含有萬用字元篩選之資料夾路徑和檔案名稱所產生的行為。

| folderPath | fileName | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以**粗體**顯示的檔案)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空白，使用預設值) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空白，使用預設值) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>檔案清單範例

本節說明使用複製活動來源中的檔案清單路徑時，會產生的行為。

假設您的來源資料夾結構如下，且想要複製粗體的檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | ADF 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;中繼資料<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集內：**<br>- 資料夾路徑：`root/FolderA`<br><br>**在複製活動來源內：**<br>- 檔案清單路徑：`root/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向同個資料存放區內的文字檔，包含所要複製的檔案清單，每行一個檔案，均有使用資料集內設定路徑的相對路徑。 |

### <a name="recursive-and-copybehavior-examples"></a>遞迴和 copyBehavior 範例

本節說明遞迴和 copyBehavior 值在不同組合的情況下，複製作業所產生的行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 內容會合併成一個檔案，並有自動產生的檔案名稱 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。 |

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要了解關於屬性的詳細資料，請參閱 [GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要了解關於此屬性的詳細資料，請參閱[刪除活動](delete-activity.md)

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>下列模型仍依原狀支援，以提供回溯相容性。 建議您往後使用以上各節所述的新模型，而製作 UI 的 ADF 已切換為產生新模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**FileShare** |是 |
| folderPath | 資料夾的路徑。 支援萬用字元篩選，允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br/><br/>範例：rootfolder/subfolder/，如需更多範例，請參閱[範例連結服務和資料集定義](#sample-linked-service-and-dataset-definitions)及[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 在指定 "folderPath" 之下檔案的**名稱或萬用字元篩選**。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>針對篩選，允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際檔案名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。<br/><br/>當未指定輸出資料集的檔案名，而且活動接收器未指定 **preserveHierarchy** 時，複製活動會自動以下列模式產生檔案名：「*資料。」活動執行識別碼 GUID]。[GUID （如果 FlattenHierarchy）]。[格式化（若已設定）]。[壓縮（如果已設定）]*，例如 "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz";如果您使用資料表名稱而非查詢從表格式來源複製，則名稱模式為 "*[table name]. [format]。[壓縮（如果已設定）]*，例如 "MyTable.csv"。 |否 |
| modifiedDatetimeStart | 檔案篩選會根據以下屬性：上次修改時間。 若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案中篩選檔案時，啟用這項設定將影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案篩選會根據以下屬性：上次修改時間。 若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案中篩選檔案時，啟用這項設定將影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在以檔案為基礎的存放區之間**依原樣複製檔案** (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 將格式下的 **type** 屬性設定為這些值其中之一。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：**GZip**、**Deflate**、**BZip2** 及 **ZipDeflate**。<br/>支援的層級為：**Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製指定名稱的單一檔案，請以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。

>[!NOTE]
>如果您使用 "fileFilter" 屬性於檔案篩選，雖然仍舊支援，不過會建議您之後使用加入 "fileName" 的新篩選功能。

**範例︰**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>舊版複製活動來源模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**FileSystemSource** |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當 recursive 設定為 true，而接收器為檔案型存放區時，系統不會在接收器複製/建立空資料夾/子資料夾。<br/>允許的值為：**true** (預設值)、**false** | 否 |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有想要限制對資料存放區的並行連線數目時，才須指定此項。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>舊版複製活動接收模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設定為：**FileSystemSink** |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設值)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 合併期間不會刪除重復的記錄。 若指定檔案名稱，則合併的檔案名稱就是指定的名稱，否則為自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有想要限制對資料存放區的並行連線數目時，才須指定此項。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
