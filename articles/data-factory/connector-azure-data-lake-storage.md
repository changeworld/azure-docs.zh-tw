---
title: 複製和轉換 Azure Data Lake Storage Gen2 中的資料
description: 瞭解如何在 Azure Data Lake Storage Gen2 中複製資料，以及如何使用 Azure Data Factory 在 Azure Data Lake Storage Gen2 中轉換資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: 2f147890887d5eb9dd1b2681bd09c662c14c74ff
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431073"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure Data Factory 複製和轉換 Azure Data Lake Storage Gen2 中的資料

Azure Data Lake Storage Gen2 （ADLS Gen2）是內建于[Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)中的大型資料分析專用的一組功能。 您可以使用檔案系統和物件儲存範例，將它與您的資料互動。

本文概述如何使用 Azure Data Factory 中的複製活動，從 Azure Data Lake Storage Gen2 複製資料，以及使用資料流程來轉換 Azure Data Lake Storage Gen2 中的資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure Data Lake Storage Gen2 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

針對複製活動，使用此連接器，您可以：

- 使用帳戶金鑰、服務主體或 Azure 資源的受控識別驗證，將資料從/複製到 Azure Data Lake Storage Gen2。
- 依檔案複製檔案，或使用[支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生檔案。
- [複製期間保留檔案中繼資料](#preserve-metadata-during-copy)。
- 從 Azure Data Lake Storage Gen1 複製時[保留 acl](#preserve-metadata-during-copy) 。

>[!IMPORTANT]
>如果您在 Azure 儲存體防火牆設定上啟用 [**允許受信任的 Microsoft 服務存取此儲存體帳戶**] 選項，而且想要使用 Azure 整合執行時間來連線到您的 Data Lake Storage Gen2，您必須針對 ADLS Gen2 使用[受控識別驗證](#managed-identity)。

>[!TIP]
>如果您啟用階層式命名空間，則 Blob 與 Data Lake Storage Gen2 Api 之間的作業目前沒有互通性。 如果您遇到「ErrorCode = FilesystemNotFound」錯誤，並顯示「指定的檔案系統不存在」訊息，則是由 Blob API 建立的指定接收檔案系統所造成，而不是在別處 Data Lake Storage Gen2 API。 若要修正此問題，請使用不存在於 Blob 容器名稱的名稱來指定新的檔案系統。 然後 Data Factory 在資料複製期間自動建立該檔案系統。

## <a name="get-started"></a>開始使用

>[!TIP]
>如需如何使用 Data Lake Storage Gen2 連接器的逐步解說，請參閱將[資料載入 Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關資訊，這些屬性是用來定義 Data Lake Storage Gen2 特有的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure Data Lake Storage Gen2 連接器支援下列驗證類型。 如需詳細資訊，請參閱對應的章節：

- [帳戶金鑰驗證](#account-key-authentication)
- [服務主體驗證](#service-principal-authentication)
- [Azure 資源的受控識別驗證](#managed-identity)

>[!NOTE]
>使用 PolyBase 將資料載入 SQL 資料倉儲時，如果您的來源 Data Lake Storage Gen2 已設定虛擬網路端點，則必須依照 PolyBase 的要求，使用受控識別驗證。 如需更多設定必要條件，請參閱[受控識別驗證](#managed-identity)一節。

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有 `https://<accountname>.dfs.core.windows.net`模式之 Data Lake Storage Gen2 的端點。 | 是 |
| accountKey | Data Lake Storage Gen2 的帳戶金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載整合執行時間。 如果未指定此屬性，則會使用預設的 Azure integration runtime。 |否 |

>[!NOTE]
>使用帳戶金鑰驗證時，不支援次要 ADLS 檔案系統端點。 您可以使用其他驗證類型。

**範例︰**

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

若要使用服務主體驗證，請遵循下列步驟。

1. 遵循[向 Azure AD 租使用者註冊您的應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)中的步驟，在 Azure Active Directory （Azure AD）中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 授與服務主體適當的許可權。 請參閱如何從檔案[和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)Data Lake Storage Gen2 中的許可權運作方式的範例

    - **作為來源**：在儲存體總管中，請至少授與所有上游資料夾和檔案系統的 [**執行**] 許可權，以及要複製之檔案的 [**讀取**] 許可權。 或者，在 [存取控制（IAM）] 中，至少授與 [**儲存體 Blob 資料讀取**者] 角色。
    - **作為接收**：在儲存體總管中，請至少授與所有上游資料夾和檔案系統的 [**執行**] 許可權，以及 [接收] 資料夾的 [**寫入**] 許可權。 或者，在 [存取控制（IAM）] 中，至少授與 [**儲存體 Blob 資料參與者**] 角色。

>[!NOTE]
>如果您使用 Data Factory UI 來撰寫，而且未在 IAM 中使用「儲存體 Blob 資料讀取器/參與者」角色設定服務主體，則在執行測試連接或流覽/流覽資料夾時，請選擇 [測試與檔案路徑的連接] 或 [從指定的路徑流覽]，並指定具有 [**讀取 + 執行**] 許可權的路徑以繼續。

連結服務支援這些屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有 `https://<accountname>.dfs.core.windows.net`模式之 Data Lake Storage Gen2 的端點。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 `SecureString`，將它安全地儲存在 Data Factory 中。 或者，您可以[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站的右上角來取出。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載整合執行時間。 如果未指定，則會使用預設的 Azure integration runtime。 |否 |

**範例︰**

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

### <a name="managed-identity"></a> Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接將此受控識別用於 Data Lake Storage Gen2 驗證，類似于使用您自己的服務主體。 它可讓這個指定的處理站在您的 Data Lake Storage Gen2 中存取和複製資料。

若要使用受控識別進行 Azure 資源驗證，請遵循下列步驟。

1. 藉由複製與您的處理站一起產生的**受控識別物件識別碼**值，來抓取[Data Factory 的受控識別資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 授與受控識別適當的許可權。 如需許可權的運作方式範例，請參閱檔案[和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)Data Lake Storage Gen2。

    - **作為來源**：在儲存體總管中，請至少授與所有上游資料夾和檔案系統的 [**執行**] 許可權，以及要複製之檔案的 [**讀取**] 許可權。 或者，在 [存取控制（IAM）] 中，至少授與 [**儲存體 Blob 資料讀取**者] 角色。
    - **作為接收**：在儲存體總管中，請至少授與所有上游資料夾和檔案系統的 [**執行**] 許可權，以及 [接收] 資料夾的 [**寫入**] 許可權。 或者，在 [存取控制（IAM）] 中，至少授與 [**儲存體 Blob 資料參與者**] 角色。

>[!NOTE]
>如果您使用 Data Factory UI 來撰寫，且未在 IAM 中使用「儲存體 Blob 資料讀取器/參與者」角色設定受控識別，則在執行測試連接或流覽/流覽資料夾時，請選擇 [測試與檔案路徑的連接] 或 [從指定的路徑流覽]，並指定具有 [**讀取 + 執行**] 許可權的路徑以繼續。

>[!IMPORTANT]
>如果您使用 PolyBase 將資料從 Data Lake Storage Gen2 載入 SQL 資料倉儲，使用 Data Lake Storage Gen2 的受控識別驗證時，請確定您也遵循[本指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步驟1和2到1）向 Azure Active Directory （Azure AD）註冊您的 SQL Database 伺服器，然後2）將儲存體 Blob 資料參與者角色指派給您的 SQL Database 伺服器;其餘部分則由 Data Factory 處理。 如果您的 Data Lake Storage Gen2 已設定 Azure 虛擬網路端點，若要使用 PolyBase 來載入資料，您必須使用 PolyBase 所需的受控識別驗證。

連結服務支援這些屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有 `https://<accountname>.dfs.core.windows.net`模式之 Data Lake Storage Gen2 的端點。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載整合執行時間。 如果未指定，則會使用預設的 Azure integration runtime。 |否 |

**範例︰**

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

下列屬性支援以格式為基礎之資料集內 `location` 設定下的 Data Lake Storage Gen2：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Dataset 中 `location` 下的 type 屬性必須設定為**AzureBlobFSLocation**。 | 是      |
| 內 | Data Lake Storage Gen2 檔案系統名稱。                              | 否       |
| folderPath | 指定檔案系統下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源設定] 中指定它。 | 否       |
| fileName   | 指定檔案系統 + folderPath 的檔案名。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在 [活動來源設定] 中指定它。 | 否       |

**範例︰**

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

如需可用來定義活動的區段和屬性完整清單，請參閱[複製活動](copy-activity-overview.md#configuration)設定和[管線和活動](concepts-pipelines-activities.md)。 本節提供 Data Lake Storage Gen2 來源和接收端所支援的屬性清單。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 作為來源類型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

下列屬性支援以格式為基礎之複製來源中 `storeSettings` 設定下的 Data Lake Storage Gen2：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為**AzureBlobFSReadSettings**。 | 是                                           |
| 遞迴                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。 允許的值為 **true** (預設值) 和 **false**。 | 否                                            |
| wildcardFolderPath       | 在指定的檔案系統上，設定要用來篩選源資料集的目的檔案夾路徑，其中包含萬用字元。 <br>允許的萬用字元 `*` （比對零或多個字元）和 `?` （符合零或單一字元）。 如果您的實際資料夾名稱包含萬用字元或內的此 escape 字元，請使用 `^` 來進行 escape。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| wildcardFileName         | 指定檔案系統 + folderPath/wildcardFolderPath 下具有萬用字元的檔案名，用以篩選來源檔案。 <br>允許的萬用字元 `*` （比對零或多個字元）和 `?` （符合零或單一字元）。 如果您的實際資料夾名稱包含萬用字元或內的此 escape 字元，請使用 `^` 來進行 escape。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 如果未在 dataset 中指定 `fileName` 則為 [是] |
| modifiedDatetimeStart    | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。 當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 同時連接到儲存體存放區的連線數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否                                            |

**範例︰**

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

下列屬性支援以格式為基礎之複製接收中 `storeSettings` 設定下的 Data Lake Storage Gen2：

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為**AzureBlobFSWriteSettings**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設值)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| blockSizeInMB | 指定用來將資料寫入 ADLS Gen2 的區塊大小（以 MB 為單位）。 深入瞭解[區塊 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)。 <br/>允許的值**介於4到 100 MB 之間**。 <br/>根據預設，ADF 會依據您的來源存放區類型和資料，自動決定區塊大小。 若為非二進位複製到 ADLS Gen2 中，預設區塊大小為 100 MB，因此最多可容納 4.95 TB 的資料。 當您的資料不大時，特別是當您使用具有不佳網路的自我裝載 Integration Runtime 導致作業超時或效能問題時，這可能不是最佳做法。 您可以明確指定區塊大小，同時確保 blockSizeInMB * 50000 夠大以儲存資料，否則複製活動執行將會失敗。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否       |

**範例︰**

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

| folderPath | fileName | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以**粗體**顯示的檔案)|
|:--- |:--- |:--- |:--- |
| `Folder*` | （空白，使用預設值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （空白，使用預設值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>一些遞迴和 copyBehavior 範例

本節說明遞迴和 copyBehavior 值的不同組合之複製作業所產生的行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>未挑選 File3、File4 和 File5 的含有。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>未挑選 File3、File4 和 File5 的含有。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>未挑選 File3、File4 和 File5 的含有。 |

## <a name="preserve-metadata-during-copy"></a>複製期間保留中繼資料

當您將檔案從 Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 複製到 Azure Data Lake Storage Gen2/Azure Blob 時，您可以選擇保留檔案中繼資料和資料。 深入瞭解[保留中繼資料](copy-activity-preserve-metadata.md#preserve-metadata)。

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留 Data Lake Storage Gen1 的 Acl

>[!TIP]
>若要在一般情況下將資料從 Azure Data Lake Storage Gen1 複製到 Gen2，請參閱[使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2](load-azure-data-lake-storage-gen2-from-gen1.md) ，以取得逐步解說和最佳作法。

當您將檔案從 Azure Data Lake Storage Gen1 複製到 Gen2 時，您可以選擇保留 POSIX 存取控制清單（Acl）以及資料。 若要深入瞭解，請從[Data Lake Storage Gen1 將 Acl 保留到 Gen2](copy-activity-preserve-metadata.md#preserve-acls)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中轉換資料時，您可以從 Azure Data Lake Storage Gen2 以 JSON、Avro、分隔文字或 Parquet 格式讀取和寫入檔案。 如需詳細資訊，請參閱對應資料流程功能中的[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>來源轉換

在「來源」轉換中，您可以在 Azure Data Lake Storage Gen2 中讀取容器、資料夾或個別檔案。 [**來源選項**] 索引標籤可讓您管理檔案的讀取方式。 

![來源選項](media/data-flow/sourceOptions1.png "來源選項")

**萬用字元路徑：** 使用萬用字元模式會指示 ADF 在單一來源轉換中，迴圈處理每個相符的資料夾和檔案。 這是在單一流程內處理多個檔案的有效方式。 當滑鼠停留在現有的萬用字元模式上時，使用顯示的 + 符號新增多個萬用字元符合模式。

從您的來源容器中，選擇符合模式的一系列檔案。 只有容器可以在資料集內指定。 因此，您的萬用字元路徑也必須包含根資料夾中的資料夾路徑。

萬用字元範例：

* ```*``` 代表任何一組字元
* ```**``` 代表遞迴目錄的嵌套
* ```?``` 取代一個字元
* ```[]``` 符合括弧中的一個或多個字元

* ```/data/sales/**/*.csv``` 取得/data/sales 下的所有 csv 檔案
* ```/data/sales/20??/**``` 取得20世紀的所有檔案
* ```/data/sales/2004/*/12/[XY]1?.csv``` 從 X 或 Y 前面加上兩位數的數位，取得2004中的所有 csv 檔案

資料**分割根路徑：** 如果您的檔案來源中有 ```key=value``` 格式的資料分割資料夾（例如 year = 2019），則可以將該分割區資料夾樹狀結構的最上層指派給資料流程資料流程中的資料行名稱。

首先，設定萬用字元以包含所有分割資料夾的路徑，加上您想要讀取的分葉檔案。

![資料分割來源檔案設定](media/data-flow/partfile2.png "資料分割檔案設定")

使用 [資料分割根路徑] 設定來定義資料夾結構的最上層。 當您透過資料預覽來查看資料的內容時，您會看到 ADF 會新增在您的每個資料夾層級中找到的已解析磁碟分割。

![分割區根路徑](media/data-flow/partfile1.png "資料分割根路徑預覽")

檔案**清單：** 這是檔案集。 建立文字檔，其中包含要處理的相對路徑檔案清單。 指向這個文字檔。

**要儲存檔案名稱的資料行：** 將原始程式檔的名稱儲存在資料行中。 在此輸入新的資料行名稱，以儲存檔案名稱字串。

**完成後：** 選擇在資料流程執行後，不要對來源檔案執行任何動作、刪除來源檔案，或移動來源檔案。 移動的路徑是相對的。

若要將原始程式檔移到另一個位置後置處理，請先選取 [移動] 進行檔案操作。 然後，設定 "from" 目錄。 如果您的路徑未使用任何萬用字元，則「來源」設定會與源資料夾位於相同的資料夾。

如果您有具有萬用字元的來源路徑，您的語法看起來會像下面這樣：

```/data/sales/20??/**/*.csv```

您可以指定 "from" 作為

```/data/sales```

和 "to" as

```/backup/priorSales```

在此情況下，所有源自/data/sales 的檔案都會移至/backup/priorSales。

> [!NOTE]
> 只有當您從管線執行（管線偵錯工具或執行回合）啟動資料流程，而該資料流程使用管線中的「執行資料流程」活動時，檔案作業才會執行。 檔案作業*不會*在資料流程的「資料流程」（debug）模式中執行。

**依上次修改時間篩選：** 您可以藉由指定上次修改的日期範圍來篩選所處理的檔案。 所有日期時間都是 UTC 格式。 

### <a name="sink-properties"></a>接收屬性

在「接收」轉換中，您可以寫入 Azure Data Lake Storage Gen2 中的容器或資料夾。 [**設定**] 索引標籤可讓您管理檔案的寫入方式。

![接收選項](media/data-flow/file-sink-settings.png "接收選項")

**清除資料夾：** 決定是否在寫入資料之前清除目的資料夾。

**檔案名選項：** 決定目的地檔案在目的地資料夾中的命名方式。 [檔案名] 選項如下：
   * **預設值**：允許 SPARK 根據部分預設值來命名檔案。
   * **模式**：輸入可列舉每個分割區輸出檔案的模式。 例如，**貸款 [n] .csv**會建立 loans1 .csv、loans2 等等。
   * **每個資料分割**：每個分割區輸入一個檔案名。
   * 當做**資料行中的資料**：將輸出檔案設為數據行的值。 路徑相對於資料集容器，而不是目的地資料夾。
   * **輸出至單一**檔案：將分割的輸出檔案結合成單一的命名檔案。 相對於資料集資料夾的路徑。 請注意，「合併」作業可能會根據節點大小而失敗。 對於大型資料集，不建議使用此選項。

**全部報價：** 決定是否將所有值用引號括住

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要瞭解有關屬性的詳細資料，請檢查[GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要瞭解屬性的詳細資料，請檢查[刪除活動](delete-activity.md)

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>下列模型仍然受支援，以提供回溯相容性。 建議您使用上述各節中所提及的新模型，然後 ADF 撰寫 UI 已切換為產生新的模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設為 **AzureBlobFSFile**。 |是 |
| folderPath | Data Lake Storage Gen2 中的資料夾路徑。 若未指定，它會指向根。 <br/><br/>支援萬用字元篩選準則。 允許的萬用字元 `*` （比對零或多個字元）和 `?` （符合零或單一字元）。 如果您的實際資料夾名稱包含萬用字元或此 escape 字元位於內部，請使用 `^` 來進行換用。 <br/><br/>範例： filesystem/folder/。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 指定 "folderPath" 下之檔案的名稱或萬用字元篩選。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>對於篩選，允許的萬用字元是 `*` （符合零或多個字元）和 `?` （符合零或單一字元）。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果您的實際檔案名包含萬用字元或此 escape 字元位於內部，請使用 `^` 來進行換用。<br/><br/>當未指定輸出資料集的 fileName，且在活動接收中未指定**preserveHierarchy**時，複製活動會自動以下列模式產生檔案名： "*Data. [活動執行識別碼 GUID]。[如果 FlattenHierarchy，則為 GUID]。[格式化（若已設定）]。[已設定壓縮]* ，例如 "Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a .txt. gz"。 如果您使用資料表名稱（而非查詢）從表格式來源複製，則名稱模式會是 " *[資料表名稱]. [格式]。[已設定壓縮]* ，例如 "MyTable. csv"。 |否 |
| modifiedDatetimeStart | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br/><br/> 當您想要以大量檔案執行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。 當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br/><br/> 當您想要以大量檔案執行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。 當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要以特定格式來剖析或產生檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format] 下的 [type] 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、 [JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、 [Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、 [ORC 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和[Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製指定名稱的單一檔案，請以資料夾部分指定**folderPath** ，並以檔案名指定**fileName** 。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定**folderPath** ，並以萬用字元篩選來指定**檔案名**。 

**範例︰**

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

### <a name="legacy-copy-activity-source-model"></a>舊版複製活動來源模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設為 **AzureBlobFSSource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。<br/>允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

**範例︰**

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

### <a name="legacy-copy-activity-sink-model"></a>舊版複製活動接收模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的類型屬性必須設為 **AzureBlobFSSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設值)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

**範例︰**

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
