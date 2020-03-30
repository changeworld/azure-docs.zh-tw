---
title: 使用 Azure 資料工廠將資料從/複製到檔案系統
description: 了解如何使用 Azure Data Factory 將資料從檔案系統複製到支援的接收資料存放區，或是從支援的來源資料存放區複製到檔案系統。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 90bab7b49f82c3c9c8954c603e8d3bda6e2dcc89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082937"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到檔案系統或從該處複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-onprem-file-system-connector.md)
> * [當前版本](connector-file-system.md)

本文概述了如何將資料複製到檔案系統和從檔案系統複製。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此檔案系統連接器支援以下活動：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，這個檔案系統連接器支援：

- 從本機電腦或網路檔案共用複製檔案，或將檔案複製到這兩處。 若要使用 Linux 檔案共用，請在您的 Linux 伺服器上安裝 [Samba](https://www.samba.org/)。
- 使用 **Windows** 驗證來複製檔案。
- 按現在的形式複製檔或使用[受支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)分析/生成檔。

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
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 從[先決條件](#prerequisites)部分瞭解更多資訊。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

### <a name="sample-linked-service-and-dataset-definitions"></a>範例連結服務和資料集定義

| 狀況 | 已連結服務定義中的 "host" | 資料集定義中的 "folderPath" |
|:--- |:--- |:--- |
| Integration Runtime 電腦上的本機資料夾： <br/><br/>範例：D:\\\* 或 D:\folder\subfolder\\\* |JSON 中：`D:\\`<br/>UI 上：`D:\` |JSON 中：`.\\` 或 `folder\\subfolder`<br>UI 上：`.\` 或 `folder\subfolder` |
| 遠端共用資料夾︰ <br/><br/>範例︰\\\\myserver\\share\\\* 或 \\\\myserver\\share\\folder\\subfolder\\\* |JSON 中：`\\\\myserver\\share`<br/>UI 上：`\\myserver\share` |JSON 中：`.\\` 或 `folder\\subfolder`<br/>UI 上：`.\` 或 `folder\subfolder` |

>[!NOTE]
>透過 UI 撰寫時，您不需要像透過 JSON 那樣輸入雙反斜線 (`\\`) 來逸出，只需指定單一反斜線。

**例子：**

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

在基於格式的資料集的設置下`location`，檔案系統支援以下屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集下`location`的類型屬性必須設置為**FileServerLocation**。 | 是      |
| folderPath | 資料夾的路徑。 如果要使用萬用字元篩選資料夾，請跳過此設置並在活動源設置中指定。 | 否       |
| fileName   | 給定資料夾 Path 下的檔案名。 如果要使用萬用字元篩選檔，請跳過此設置並在活動源設置中指定。 | 否       |

**例子：**

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

在基於格式的副本源中的設置下`storeSettings`，檔案系統支援以下屬性：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下`storeSettings`的類型屬性必須設置為**FileServerReadSettings**。 | 是                                           |
| 遞迴                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 允許的值**為 true（** 預設值）和**false**。 | 否                                            |
| 萬用字元資料夾路徑       | 具有萬用字元的資料夾路徑，用於篩選源資料夾。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 萬用字元檔案名稱         | 給定資料夾 Path/萬用字元下具有萬用字元的檔案名用於篩選原始檔案。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 [請注意，這將推翻您的檔案名設置] | 如果`fileName`資料集中未指定，則為 |
| modifiedDatetimeStart    | 基於屬性的檔篩選器：上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到資料存儲時，才指定。 | 否                                            |

**例子：**

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基於格式的副本接收器中的設置下`storeSettings`，檔案系統支援以下屬性：

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下`storeSettings`的類型屬性必須設置為**檔案伺服器Write設置**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- 保留層次結構（預設）：</b>保留目的檔案夾中的檔層次結構。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- 合併檔</b>：將源資料夾中的所有檔合併到一個檔。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 要同時連接到資料存儲的連接數。 僅當要將併發連接限制到資料存儲時，才指定。 | 否       |

**例子：**

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

| folderPath | fileName | 遞迴 | 源資料夾結構和篩選結果（檢索**粗體**檔）|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空白，使用預設值) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空白，使用預設值) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**檔3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**檔3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="recursive-and-copybehavior-examples"></a>遞迴和 copyBehavior 範例

本節說明遞迴和 copyBehavior 值在不同組合的情況下，複製作業所產生的行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 有自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的內容會合併成一個檔案，並有自動產生的檔案名稱 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 有自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 有自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並有自動產生的檔案名稱。 File1 有自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4 和 File5 的 Subfolder1。 |

## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>獲取中繼資料活動屬性

要瞭解有關屬性的詳細資訊，請選中[獲取中繼資料活動](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>刪除活動屬性

要瞭解有關屬性的詳細資訊，請選中["刪除活動](delete-activity.md)"

## <a name="legacy-models"></a>舊模型

>[!NOTE]
>以下型號仍支援以下型號，以便進行向後相容性。 建議您今後使用上述各節中提及的新模型，並且 ADF 創作 UI 已切換到生成新模型。

### <a name="legacy-dataset-model"></a>舊資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**FileShare** |是 |
| folderPath | 資料夾的路徑。 支援萬用字元篩選，允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br/><br/>範例：rootfolder/subfolder/，如需更多範例，請參閱[範例連結服務和資料集定義](#sample-linked-service-and-dataset-definitions)及[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 在指定 "folderPath" 之下檔案的**名稱或萬用字元篩選**。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>針對篩選，允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際檔案名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。<br/><br/>當未為輸出資料集指定檔案名並在活動接收器中未指定**保留層次結構**時，複製活動將自動生成具有以下模式的檔案名："*資料"。活動運行 ID GUID]。[GUID 如果扁平層次結構]。[格式（如果已配置）。"[壓縮如果配置]"，* 例如"資料.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz";如果使用表名而不是查詢從表格源進行複製，則名稱模式為 *"[表名]"。格式*。[如果配置壓縮]"，* 例如"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 基於屬性的檔篩選器：上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您希望從大量檔執行檔篩選器時，啟用此設置會影響資料移動的整體性能。 <br/><br/> 屬性可以是 Null，這意味著不會將任何檔案屬性篩選器應用於資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 基於屬性的檔篩選器：上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您希望從大量檔執行檔篩選器時，啟用此設置會影響資料移動的整體性能。 <br/><br/> 屬性可以是 Null，這意味著不會將任何檔案屬性篩選器應用於資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果要在檔案為儲存基礎（二進位副本）之間**按"按"方式複製檔**，請跳過輸入資料集和輸出資料集定義中的格式部分。<br/><br/>如果您想要以特定格式來剖析或產生檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 將 [format] 下的 [type]**** 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型是：GZip、Deflate、BZip2 和**ZipDeflate**。 **GZip** **Deflate** **BZip2**<br/>支援的級別是：**最佳**和**最快**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製指定名稱的單一檔案，請以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。

>[!NOTE]
>如果您使用 "fileFilter" 屬性於檔案篩選，雖然仍舊支援，不過會建議您之後使用加入 "fileName" 的新篩選功能。

**例子：**

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

### <a name="legacy-copy-activity-source-model"></a>舊複製活動源模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**FileSystemSource** |是 |
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當 recursive 設定為 true，而接收器為檔案型存放區時，系統不會在接收器複製/建立空資料夾/子資料夾。<br/>允許的值是 **：true（** 預設 **），false** | 否 |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到資料存儲時，才指定。 | 否 |

**例子：**

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

### <a name="legacy-copy-activity-sink-model"></a>舊複製活動接收器模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設定為：**FileSystemSink** |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設值)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 合併期間不執行記錄重復資料消除。 如果指定了檔案名，則合併的檔案名將是指定的名稱;如果指定了檔案名，則合併的檔案名將為"檔案名"。"檔案名"。否則，將是自動生成的檔案名。 | 否 |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到資料存儲時，才指定。 | 否 |

**例子：**

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
