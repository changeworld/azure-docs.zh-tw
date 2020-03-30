---
title: 複製和轉換 Azure 資料存儲第 2 代中的資料
description: 瞭解如何將資料複製到 Azure 資料湖存儲 Gen2 中，以及通過使用 Azure 資料工廠轉換 Azure 資料湖存儲 Gen2 中的資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 3c7ff0061a57d1a1a7525ec03b4f77c117415ca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155846"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure 資料工廠複製和轉換 Azure 資料湖存儲第 2 代中的資料

Azure 資料存儲第 2 代 （ADLS Gen2） 是一組專用於[Azure Blob 存儲](../storage/blobs/storage-blobs-introduction.md)中內置的大資料分析功能。 您可以使用檔案系統和物件存儲範例與資料進行介面。

本文概述了如何使用 Azure 資料工廠中的複製活動從 Azure 資料存儲第 2 代複製資料，以及使用資料流程轉換 Azure 資料湖存儲 Gen2 中的資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Azure 資料存儲庫第 2 代連接器支援以下活動：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [映射資料流程](concepts-data-flow-overview.md)
- [查找活動](control-flow-lookup-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

對於複製活動，使用此連接器，您可以：

- 通過使用 Azure 資源身份驗證的帳戶金鑰、服務主體或託管標識，將資料從/複製到 Azure 資料存儲庫存儲 Gen2。
- 按方式複製檔或分析或生成具有[受支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)的檔。
- [在複製期間保留檔中繼資料](#preserve-metadata-during-copy)。
- 從 Azure 資料存儲第 1 代/Gen2 複製時[保留 ACL。](#preserve-acls)

>[!IMPORTANT]
>如果啟用 **"允許受信任的 Microsoft 服務在**Azure 存儲防火牆設置上訪問此存儲帳戶"選項，並且希望使用 Azure 集成運行時連接到資料湖存儲 Gen2，則必須對 ADLS Gen2 使用[託管標識身份驗證](#managed-identity)。

>[!TIP]
>如果啟用階層命名空間，則當前 Blob 和資料存儲存儲 Gen2 API 之間沒有操作的互通性。 如果使用消息"指定的檔案系統不存在"來輸入錯誤"ErrorCode_FilesystemNotFound"，則由通過 Blob API 而不是其他地方的資料存儲湖存儲 Gen2 API 創建的指定接收器檔案系統引起。 要解決此問題，請指定名稱不存在的新檔案系統作為 Blob 容器的名稱。 然後，資料工廠在資料複製期間自動創建該檔案系統。

## <a name="get-started"></a>開始使用

>[!TIP]
>有關如何使用資料湖存儲 Gen2 連接器的演練，請參閱[將資料載入到 Azure 資料存儲第 2 代](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于資料湖存儲 Gen2 的資料工廠實體的屬性的資訊。

## <a name="linked-service-properties"></a>連結服務屬性

Azure 資料湖存儲 Gen2 連接器支援以下身份驗證類型。 有關詳細資訊，請參閱相應的部分：

- [帳戶金鑰驗證](#account-key-authentication)
- [服務主體身份驗證](#service-principal-authentication)
- [Azure 資源驗證的受控識別](#managed-identity)

>[!NOTE]
>使用 PolyBase 將資料載入到 SQL 資料倉儲時，如果來源資料存儲湖存儲 Gen2 配置了虛擬網路終結點，則必須按照 PolyBase 的要求使用託管標識身份驗證。 有關更多配置先決條件，請參閱[託管標識身份驗證](#managed-identity)部分。

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 資料湖存儲第 2 代的終結點，`https://<accountname>.dfs.core.windows.net`其模式為 。 | 是 |
| accountKey | 資料存儲湖存儲第 2 代的帳戶金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用於連接到資料存儲的[集成運行時](concepts-integration-runtime.md)。 如果資料存儲在私人網路絡中，則可以使用 Azure 集成運行時或自託管集成運行時。 如果未指定此屬性，則使用預設的 Azure 集成運行時。 |否 |

>[!NOTE]
>使用帳戶金鑰身份驗證時，不支援輔助 ADLS 檔案系統終結點。 您可以使用其他身份驗證類型。

**例子：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>服務主體驗證

要使用服務主體身份驗證，請按照以下步驟操作。

1. 按照 Azure [AD 租戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)的步驟，在 Azure 活動目錄 （Azure AD） 中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 授予服務主體適當的許可權。 查看有關許可權在"資料存儲 Gen2"中如何工作的示例，這些操作來自[檔和目錄上的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **作為源**：在存儲資源管理器中，至少授予所有上游資料夾和檔案系統**的執行**許可權，以及檔案複製的**讀取**許可權。 或者，在存取控制 （IAM） 中，至少授予**存儲 Blob 資料讀取器**角色。
    - **作為接收器**：在存儲資源管理器中，至少授予所有上游資料夾和檔案系統**的執行**許可權，以及接收器資料夾的**寫入**許可權。 或者，在存取控制 （IAM） 中，至少授予**存儲 Blob 資料參與者**角色。

>[!NOTE]
>如果使用資料工廠 UI 創作服務主體，並且服務主體未在 IAM 中設置"存儲 Blob 資料讀取器/參與者"角色，則在執行測試連接或流覽/導航資料夾時，請選擇"測試連接到檔路徑"或"從指定路徑流覽"，並指定具有 **"讀取 + 執行**"許可權的路徑以繼續。

連結服務支援這些屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 資料湖存儲第 2 代的終結點，`https://<accountname>.dfs.core.windows.net`其模式為 。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為將其`SecureString`安全地存儲在資料工廠中。 或者，您可以[引用存儲在 Azure 金鑰保存庫中的秘密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 通過將滑鼠懸停在 Azure 門戶的右上角來檢索它。 | 是 |
| connectVia | 用於連接到資料存儲的[集成運行時](concepts-integration-runtime.md)。 如果資料存儲在私人網路絡中，則可以使用 Azure 集成運行時或自託管集成運行時。 如果未指定，則使用預設的 Azure 集成運行時。 |否 |

**例子：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 資源身份驗證的託管標識

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接使用此託管標識進行資料湖存儲 Gen2 身份驗證，類似于使用您自己的服務主體。 它允許此指定工廠訪問資料並從您的資料存儲 Gen2 進行複製。

要使用託管標識進行 Azure 資源身份驗證，請按照以下步驟操作。

1. 通過複製與工廠一起生成的**託管標識物件識別碼**的值，[檢索資料工廠託管標識資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 授予託管標識適當的許可權。 有關資料湖存儲 Gen2 中許可權如何工作的示例，這些操作來自[檔和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

    - **作為源**：在存儲資源管理器中，至少授予所有上游資料夾和檔案系統**的執行**許可權，以及檔案複製的**讀取**許可權。 或者，在存取控制 （IAM） 中，至少授予**存儲 Blob 資料讀取器**角色。
    - **作為接收器**：在存儲資源管理器中，至少授予所有上游資料夾和檔案系統**的執行**許可權，以及接收器資料夾的**寫入**許可權。 或者，在存取控制 （IAM） 中，至少授予**存儲 Blob 資料參與者**角色。

>[!NOTE]
>如果使用資料工廠 UI 創作，並且託管標識未在 IAM 中使用"存儲 Blob 資料讀取器/參與者"角色設置，則在執行測試連接或流覽/導航資料夾時，請選擇"測試連接到檔路徑"或"從指定路徑流覽"，並指定具有 **"讀取 + 執行**"許可權的路徑以繼續。

>[!IMPORTANT]
>如果使用 PolyBase 將資料從資料存儲第 2 代載入到 SQL 資料倉儲中，則當對資料存儲第 2 代使用託管標識身份驗證時，請確保也按照[本指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步驟 1 和 2 註冊 SQL 資料庫伺服器（Azure 活動目錄 （Azure AD）， 2） 將存儲 Blob 資料參與者角色分配給 SQL 資料庫伺服器;其餘的由資料工廠處理。 如果資料湖存儲 Gen2 配置了 Azure 虛擬網路終結點，要使用 PolyBase 從它載入資料，則必須按照 PolyBase 的要求使用託管標識身份驗證。

連結服務支援這些屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 資料湖存儲第 2 代的終結點，`https://<accountname>.dfs.core.windows.net`其模式為 。 | 是 |
| connectVia | 用於連接到資料存儲的[集成運行時](concepts-integration-runtime.md)。 如果資料存儲在私人網路絡中，則可以使用 Azure 集成運行時或自託管集成運行時。 如果未指定，則使用預設的 Azure 集成運行時。 |否 |

**例子：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

有關可用於定義資料集的節和屬性的完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基於格式的資料集中的設置下`location`，Data Lake 存儲 Gen2 支援以下屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集下`location`的類型屬性必須設置為**AzureBlobFSLocation**。 | 是      |
| 檔 | 資料存儲湖存儲 Gen2 檔案系統名稱。                              | 否       |
| folderPath | 給定檔案系統下資料夾的路徑。 如果要使用萬用字元篩選資料夾，請跳過此設置並在活動源設置中指定它。 | 否       |
| fileName   | 給定檔案系統 + 資料夾路徑下的檔案名。 如果要使用萬用字元篩選檔，請跳過此設置並在活動源設置中指定它。 | 否       |

**例子：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
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

有關可用於定義活動的節和屬性的完整清單，請參閱[複製活動配置](copy-activity-overview.md#configuration)以及[管道和活動](concepts-pipelines-activities.md)。 本節提供 Data Lake Storage Gen2 來源和接收端所支援的屬性清單。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure 資料湖存儲 Gen2 作為源類型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基於格式的副本源中的設置下`storeSettings`，Data Lake 存儲 Gen2 支援以下屬性：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下`storeSettings`的類型屬性必須設置為**AzureBlobFSReadSettings**。 | 是                                           |
| 遞迴                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設置為 true 並且接收器是檔案為儲存基礎時，不會在接收器上複製或創建空資料夾或子資料夾。 允許的值**為 true（** 預設值）和**false**。 | 否                                            |
| 萬用字元資料夾路徑       | 在資料集中配置的給定檔案系統下具有萬用字元的資料夾路徑，以篩選源資料夾。 <br>允許的通配`*`符是（匹配零個或多個字元）`?`和（匹配零個或多個字元）。 用於`^`轉義，如果實際資料夾名稱有萬用字元或此逸出字元裡面。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 萬用字元檔案名稱         | 給定檔案系統下具有萬用字元的檔案名 + 資料夾Path/萬用字元FolderFolderPath以篩選原始檔案。 <br>允許的通配`*`符是（匹配零個或多個字元）`?`和（匹配零個或多個字元）。 用於`^`轉義，如果實際資料夾名稱有萬用字元或此逸出字元裡面。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | `fileName`如果資料集中未指定，則為"是" |
| modifiedDatetimeStart    | 基於屬性"上次修改"的檔篩選器。 如果檔上次修改的時間在 和`modifiedDatetimeStart``modifiedDatetimeEnd`之間的時間範圍內，則選擇這些檔。 時間以"2018-12-01T05：00：00：00Z"的形式應用於 UTC 時區。 <br> 屬性可以是 Null，這意味著沒有檔案屬性篩選器應用於資料集。 當`modifiedDatetimeStart`具有日期時間值但`modifiedDatetimeEnd`為 Null 時，則表示將選擇其上次修改屬性大於或等於日期時間值的檔。 當`modifiedDatetimeEnd`具有日期時間值但`modifiedDatetimeStart`為 Null 時，則表示將選擇其上次修改屬性小於日期時間值的檔。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到資料存儲時，才指定。 | 否                                            |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>以 Azure Data Lake Storage Gen2 作為接收類型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基於格式的副本接收器的設置下`storeSettings`，Data Lake 存儲 Gen2 支援以下屬性：

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下`storeSettings`的類型屬性必須設置為**AzureBlobFSWrite設置**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- 保留層次結構（預設）：</b>保留目的檔案夾中的檔層次結構。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- 合併檔</b>：將源資料夾中的所有檔合併到一個檔。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| 塊大小因MB | 指定用於將資料寫入 ADLS Gen2 的 MB 塊大小。 [瞭解有關塊 Blob 的更多](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)。 <br/>允許的值**介於 4 到 100 MB 之間**。 <br/>預設情況下，ADF 會根據您的源存儲類型和資料自動確定塊大小。 對於非二進位複製到 ADLS Gen2，預設塊大小為 100 MB，以便最多適合 4.95 TB 資料。 當資料不大時，它可能是最佳的，尤其是在使用自託管集成運行時時，網路不佳導致操作超時或性能問題。 您可以顯式指定塊大小，同時確保塊SizeInMB_50000足夠大以存儲資料，否則複製活動運行將失敗。 | 否 |
| maxConcurrentConnections | 要同時連接到資料存儲的連接數。 僅當要將併發連接限制到資料存儲時，才指定。 | 否       |

**例子：**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSettings",
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
| `Folder*` | （空，使用預設值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （空，使用預設值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**檔3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**檔3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>一些遞迴和 copyBehavior 範例

本節介紹遞迴和複製行為值的不同組合的複製操作的結果行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>未選取包含 File3、File4 和 File5 的子資料夾1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>未選取包含 File3、File4 和 File5 的子資料夾1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>未選取包含 File3、File4 和 File5 的子資料夾1。 |

## <a name="preserve-metadata-during-copy"></a>在複製期間保留中繼資料

將檔從 Amazon S3/Azure Blob/Azure 資料存儲第 2 代複製到 Azure 資料湖存儲 Gen2/Azure Blob 時，可以選擇保留檔中繼資料和資料。 從[保留中繼資料](copy-activity-preserve-metadata.md#preserve-metadata)中瞭解更多資訊。

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>保留資料湖存儲第 1 代/第 2 代中的 ACL

將檔從 Azure 資料存儲庫存儲第 1 代/第 2 代複製到 Gen2 時，可以選擇保留 POSIX 存取控制清單 （ACL） 以及資料。 從保留[ACL 從資料存儲庫第 1 代/第 2 代 到第 2 代](copy-activity-preserve-metadata.md#preserve-acls)。

>[!TIP]
>要將資料從 Azure 資料存儲 Gen1 複製到 Gen2，請參閱[將資料從 Azure 資料存儲第 1 代複製到 Azure 資料工廠](load-azure-data-lake-storage-gen2-from-gen1.md)的第 2 代資料，瞭解演練和最佳實踐。

## <a name="mapping-data-flow-properties"></a>映射資料流程屬性

在映射資料流程中轉換資料時，可以從 Azure 資料存儲 Gen2 中讀取和寫入 JSON、Avro、分隔文本或 Parquet 格式的檔。 有關詳細資訊，請參閱映射資料流程功能中的[源轉換](data-flow-source.md)和[接收器轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>源轉換

在源轉換中，可以從 Azure 資料湖存儲 Gen2 中的容器、資料夾或單個檔進行讀取。 "**源"選項**選項卡允許您管理檔讀取的方式。 

![源選項](media/data-flow/sourceOptions1.png "源選項")

**萬用字元路徑：** 使用萬用字元模式將指示 ADF 在單個源轉換中逐一查看每個匹配的資料夾和檔。 這是處理單個流中的多個檔的有效方法。 使用將滑鼠懸停在現有萬用字元模式上時出現的 + 符號添加多個萬用字元匹配模式。

從源容器中選擇一系列與模式匹配的檔。 只能在資料集中指定容器。 因此，萬用字元路徑還必須包括根資料夾中的資料夾路徑。

萬用字元示例：

* ```*```表示任意一組字元
* ```**```表示遞迴目錄嵌套
* ```?```替換一個字元
* ```[]```匹配括弧中更多字元之一

* ```/data/sales/**/*.csv```獲取 /資料/銷售下的所有 csv 檔
* ```/data/sales/20??/**/```獲取 20 世紀的所有檔
* ```/data/sales/*/*/*.csv```獲取 csv 檔下 /資料/銷售兩個級別
* ```/data/sales/2004/*/12/[XY]1?.csv```在 2004 年 12 月獲取所有 csv 檔，以 X 或 Y 開頭，以兩位數位開頭

**分區根路徑：** 如果檔源中的資料夾已採用```key=value```格式（例如，year_2019）進行分區，則可以將該分區資料夾樹的頂層分配給資料流程資料流程中的列名稱。

首先，設置萬用字元以包括分區資料夾的所有路徑以及您希望讀取的葉檔。

![分區原始檔案設置](media/data-flow/partfile2.png "分區檔設置")

使用分區根路徑設置定義資料夾結構的頂層。 當您通過資料預覽查看資料內容時，您將看到 ADF 將添加在每個資料夾級別中找到的已解析分區。

![分區根路徑](media/data-flow/partfile1.png "分區根路徑預覽")

**檔案清單：** 這是一個檔集。 創建包含要處理的相對路徑檔的清單的文字檔。 指向此文字檔。

**要存儲檔案名的列：** 將原始檔案的名稱存儲在資料中的列中。 在此處輸入新的列名以存儲檔案名字串。

**完成後：** 選擇在資料流程運行後對原始檔案不執行任何操作、刪除原始檔案或移動原始檔案。 移動的路徑是相對的。

要將原始檔案移到處理後的另一個位置，首先選擇檔操作的"移動"。 然後，設置"from"目錄。 如果您沒有為路徑使用任何萬用字元，則"from"設置將與源資料夾位於同一資料夾。

如果您有具有萬用字元的源路徑，則語法如下所示：

```/data/sales/20??/**/*.csv```

您可以將"從"指定為

```/data/sales```

和"到"作為

```/backup/priorSales```

在這種情況下，在 /data/sales 下獲取的所有檔將移動到 /備份/優先銷售。

> [!NOTE]
> 僅當從管道運行（管道調試或執行運行）啟動資料流程時，檔操作才會運行，該流使用管道中的執行資料流程活動。 檔操作*不在*資料流程偵錯模式下運行。

**按上次修改進行篩選：** 您可以通過指定上次修改檔的日期範圍來篩選處理的檔。 所有日期時間均以 UTC 表示。 

### <a name="sink-properties"></a>接收器屬性

在接收器轉換中，可以寫入 Azure 資料湖存儲 Gen2 中的容器或資料夾。 通過 **"設置"** 選項卡，您可以管理檔的寫入方式。

![接收器選項](media/data-flow/file-sink-settings.png "接收器選項")

**清除資料夾：** 確定在寫入資料之前是否清除目的檔案夾。

**檔案名選項：** 確定目的檔案在目的檔案夾中的命名方式。 檔案名選項為：
   * **預設值**：允許 Spark 根據 PART 預設值命名檔。
   * **模式**：輸入一種模式，以枚舉每個分區的輸出檔案。 例如，**貸款\n\csv**將創建貸款1.csv，貸款2.csv，等等。
   * **每個分區**：每個分區輸入一個檔案名。
   * **作為列中的資料**：將輸出檔案設置為列的值。 路徑相對於資料集容器，而不是目的檔案夾。 如果資料集中包含資料夾路徑，則該路徑將被覆蓋。
   * **輸出到單個檔**：將分區的輸出檔案合併到單個命名檔中。 路徑與資料集資料夾相關。 請注意，te 合併作業可能會根據節點大小失敗。 不建議對大型資料集使用此選項。

**報價所有：** 確定是否將報價中的所有值括起來

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
| type | 資料集的類型屬性必須設為 **AzureBlobFSFile**。 |是 |
| folderPath | 資料湖存儲 Gen2 中資料夾的路徑。 若未指定，它會指向根。 <br/><br/>支援萬用字元篩選器。 允許的通配`*`符是（匹配零個或多個字元）`?`和（匹配零個或多個字元）。 如果`^`實際資料夾名稱具有萬用字元或此逸出字元在裡面，則用於轉義。 <br/><br/>示例：檔案系統/資料夾/。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 指定"資料夾路徑"下檔的名稱或萬用字元篩選器。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>對於篩選器，允許的萬用字元`*`為（匹配零個或多個字元）和`?`（匹配零個或多個字元）。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>用於`^`轉義，如果實際檔案名有萬用字元或此逸出字元在裡面。<br/><br/>當未為輸出資料集指定檔案名並在活動接收器中未指定**保留層次結構**時，複製活動將自動生成具有以下模式的檔案名："*資料"。活動運行 ID GUID]。[GUID 如果扁平層次結構]。[格式（如果已配置）。"[壓縮如果配置]*"，例如，"資料.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"。 如果使用表名而不是查詢從表格源進行複製，則名稱模式為 *"[表名]"。格式*。[壓縮（如果配置）"，"* 例如，"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 基於屬性"上次修改"的檔篩選器。 如果檔上次修改的時間在 和`modifiedDatetimeStart``modifiedDatetimeEnd`之間的時間範圍內，則選擇這些檔。 時間以"2018-12-01T05：00：00：00Z"的形式應用於 UTC 時區。 <br/><br/> 當您想要使用大量檔進行檔篩選器時，啟用此設置會影響資料移動的總體性能。 <br/><br/> 屬性可以是 Null，這意味著沒有檔案屬性篩選器應用於資料集。 當`modifiedDatetimeStart`具有日期時間值但`modifiedDatetimeEnd`為 Null 時，表示將選擇其上次修改屬性大於或等於日期時間值的檔。 當`modifiedDatetimeEnd`具有日期時間值但`modifiedDatetimeStart`為 Null 時，表示選擇上次修改屬性小於日期時間值的檔。| 否 |
| modifiedDatetimeEnd | 基於屬性"上次修改"的檔篩選器。 如果檔上次修改的時間在 和`modifiedDatetimeStart``modifiedDatetimeEnd`之間的時間範圍內，則選擇這些檔。 時間以"2018-12-01T05：00：00：00Z"的形式應用於 UTC 時區。 <br/><br/> 當您想要使用大量檔進行檔篩選器時，啟用此設置會影響資料移動的總體性能。 <br/><br/> 屬性可以是 Null，這意味著沒有檔案屬性篩選器應用於資料集。 當`modifiedDatetimeStart`具有日期時間值但`modifiedDatetimeEnd`為 Null 時，表示將選擇其上次修改屬性大於或等於日期時間值的檔。 當`modifiedDatetimeEnd`具有日期時間值但`modifiedDatetimeStart`為 Null 時，表示選擇上次修改屬性小於日期時間值的檔。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要以特定格式來剖析或產生檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format]**** 下的 [type]**** 屬性設定為下列其中一個值。 有關詳細資訊，請參閱[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format) [、JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)[、Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)[、ORC 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和[Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip****、Deflate****、BZip2**** 及 ZipDeflate****。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>要複製具有給定名稱的單個檔，請指定資料夾部分的**資料夾 Path**和具有檔案名**的檔案名**。<br>要在資料夾下複製檔子集，請使用資料夾部件指定**資料夾 Path，** 使用萬用字元篩選器**指定檔案名**。 

**例子：**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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
| type | 複製活動來源的類型屬性必須設為 **AzureBlobFSSource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設置為 true 並且接收器是檔案為儲存基礎時，不會在接收器上複製或創建空資料夾或子資料夾。<br/>允許的值**為 true（** 預設值）和**false**。 | 否 |
| maxConcurrentConnections | 要同時連接到資料存儲的連接數。 僅當要將併發連接限制到資料存儲時，才指定。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
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
| type | 複製活動接收的類型屬性必須設為 **AzureBlobFSSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- 保留層次結構（預設）：</b>保留目的檔案夾中的檔層次結構。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- 合併檔</b>：將源資料夾中的所有檔合併到一個檔。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 要同時連接到資料存儲的連接數。 僅當要將併發連接限制到資料存儲時，才指定。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟

如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
