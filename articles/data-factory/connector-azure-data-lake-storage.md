---
title: 在 Azure Data Lake Storage Gen2 中複製和轉換資料
description: 了解如何使用 Azure Data Factory 在 Azure Data Lake Storage Gen2 中來回複製資料，以及在 Azure Data Lake Storage Gen2 中轉換資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: c0f7df8db79f549dfeca15e6411ae72cbe66d2bd
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346277"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure Data Factory 在 Azure Data Lake Storage Gen2 中複製和轉換資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 (ADLS Gen2) 是一組巨量資料分析的專屬功能，內建於 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)。 其可讓您使用檔案系統和物件儲存範例連接您的資料。

本文概述如何使用 Azure Data Factory 中的複製活動，在 Azure Data Lake Storage Gen2 中來回複製資料，以及如何使用資料流程在 Azure Data Lake Storage Gen2 中轉換資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

>[!TIP]
>請參閱[使用 Azure Data Factory 將資料從資料湖或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)，以深入了解資料湖或資料倉儲移轉案例。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure Data Lake Storage Gen2 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

對於複製活動，使用此連接器，您可以：

- 使用帳戶金鑰、服務主體或 Azure 資源的受控識別驗證，在 Azure Data Lake Storage Gen2 中往返複製資料。
- 依原樣複製檔案，或使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生檔案。
- [複製期間保留檔案中繼資料](#preserve-metadata-during-copy)。
- 從 Azure Data Lake Storage Gen1/Gen2 複製時，[保留 ACLl](#preserve-acls)。

## <a name="get-started"></a>開始使用

>[!TIP]
>如需有關如何使用 Data Lake Storage Gen2 連接器的逐步解說，請參閱[將資料載入 Data Lake Storage Gen2 中](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關資訊，這些屬性是用來定義 Data Lake Store Gen 2 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure Data Lake Storage Gen2 連接器支援下列驗證類型。 如需詳細資訊，請參閱對應章節：

- [帳戶金鑰驗證](#account-key-authentication)
- [服務主體驗證](#service-principal-authentication)
- [Azure 資源的受控識別驗證](#managed-identity)

>[!NOTE]
>- 如果您想要使用公用 Azure integration runtime，藉由利用 Azure 儲存體防火牆上啟用的 [ **允許信任的 Microsoft 服務存取此儲存體帳戶** ] 選項來連線 Data Lake Storage Gen2，您必須使用 [受控識別驗證](#managed-identity)。
>- 當您使用 PolyBase 或 COPY 語句將資料載入 Azure Synapse Analytics 中，如果您的來源或預備 Data Lake Storage Gen2 已設定 Azure 虛擬網路端點，您必須使用 Synapse 所要求的受控識別驗證。 如需更多設定必要條件，請參閱[受控識別驗證](#managed-identity)一節。

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有 `https://<accountname>.dfs.core.windows.net` 模式的 Data Lake Storage Gen2 所適用的端點。 | 是 |
| accountKey | Data Lake Storage Gen2 的帳戶金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，則可使用 Azure Integration Runtime 或自我裝載整合執行階段。 如果未指定此屬性，則會使用預設的 Azure Integration Runtime。 |否 |

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

1. 遵循[使用 Azure AD 租用戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)中的步驟，在 Azure Active Directory (Azure AD) 中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 將適當的權限授與服務主體。 請參閱[檔案和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)，了解 Data Lake Storage Gen2 中權限運作方式的範例。

    - **作為來源**：在 [儲存體總管] 中，至少授與對所有上游資料夾和檔案系統的 [執行] 權限，以及對接收資料夾的 [讀取] 權限。 或者，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料讀取者] 角色。
    - **作為接收**：在 [儲存體總管] 中，至少授與對所有上游資料夾和檔案系統的 [執行] 權限，以及對接收資料夾的 [寫入] 權限。 或者，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料參與者] 角色。

>[!NOTE]
>如果您使用 Data Factory UI 進行撰寫，而且未在 IAM 中使用「儲存體 Blob 資料讀取者/參與者」角色設定服務主體，則在進行測試連線或瀏覽/導覽資料夾時，請選擇 [測試與檔案路徑的連線] 或 [從指定的路徑瀏覽]，然後指定具有 [讀取 + 執行] 權限的路徑以繼續。

以下是連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有 `https://<accountname>.dfs.core.windows.net` 模式的 Data Lake Storage Gen2 所適用的端點。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalCredentialType | 要用於服務主體驗證的認證類型。 允許的值為 **ServicePrincipalKey** 和 **ServicePrincipalCert**。 | 是 |
| servicePrincipalCredential | 服務主體認證。 <br/> 當您使用 **ServicePrincipalKey** 做為認證類型時，請指定應用程式的金鑰。 將此欄位標示為 **SecureString** ，以安全地將它儲存在 Data Factory 中，或 [參考儲存在 Azure Key Vault 中的密碼](store-credentials-in-key-vault.md)。 <br/> 當您使用 **ServicePrincipalCert** 作為認證時，請參考 Azure Key Vault 中的憑證。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標示為 **SecureString** ，以安全地將它儲存在 Data Factory 中，或 [參考儲存在 Azure Key Vault 中的密碼](store-credentials-in-key-vault.md)。 <br/> 這個屬性仍支援對的 `servicePrincipalId`  +  `servicePrincipalKey` 。 當 ADF 新增服務主體憑證驗證時，服務主體驗證的新模型為 `servicePrincipalId`  +  `servicePrincipalCredentialType`  +  `servicePrincipalCredential` 。 | 否 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可加以擷取。 | 是 |
| azureCloudType | 針對服務主體驗證，請指定您的 Azure Active Directory 應用程式註冊所在的 Azure 雲端環境類型。 <br/> 允許的值為 **AzurePublic**、 **AzureChina**、 **AzureUsGovernment** 和 **AzureGermany**。 根據預設，會使用 data factory 的雲端環境。 | 否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，則可使用 Azure Integration Runtime 或自我裝載整合執行階段。 若未指定，則會使用預設 Azure Integration Runtime。 |否 |

**範例：使用服務主體金鑰驗證**

您也可以將服務主體金鑰儲存在 Azure Key Vault 中。

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
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

**範例：使用服務主體憑證驗證**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接將此受控識別用於 Data Lake Storage Gen2 驗證，類似於使用您自己的服務主體。 這可以讓這個指定的處理站存取及複製資料至 Data Lake Store，或從 Data Lake Storage Gen2 存取及複製資料。

若要使用 Azure 資源的受控識別驗證，請遵循下列步驟。

1. 複製與處理站一起產生的 **受控識別物件識別碼**，藉此 [擷取 Data Factory 受控識別資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 將適當的權限授予受控識別。 請參閱[檔案和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)，了解 Data Lake Storage Gen2 中權限運作方式的範例。

    - **作為來源**：在 [儲存體總管] 中，至少授與對所有上游資料夾和檔案系統的 [執行] 權限，以及對接收資料夾的 [讀取] 權限。 或者，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料讀取者] 角色。
    - **作為接收**：在 [儲存體總管] 中，至少授與對所有上游資料夾和檔案系統的 [執行] 權限，以及對接收資料夾的 [寫入] 權限。 或者，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料參與者] 角色。

>[!NOTE]
>如果您使用 Data Factory UI 進行撰寫，而且未在 IAM 中使用「儲存體 Blob 資料讀取者/參與者」角色設定受控識別，則在進行測試連線或瀏覽/導覽資料夾時，請選擇 [測試與檔案路徑的連線] 或 [從指定的路徑瀏覽]，然後指定具有 [讀取 + 執行] 權限的路徑以繼續。

>[!IMPORTANT]
>如果您使用 PolyBase 或 COPY 語句將資料從 Data Lake Storage Gen2 載入 Azure Synapse Analytics 中，當您使用受控識別驗證進行 Data Lake Storage Gen2 時，請確定您也遵循 [本指南](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)中的步驟1到3。 這些步驟會向 Azure AD 註冊您的伺服器，並將「儲存體 Blob 資料參與者」角色指派給您的伺服器。 Data Factory 會處理其餘部分。 如果您使用 Azure 虛擬網路端點來設定 Blob 儲存體，您也需要讓 [允許信任的 Microsoft 服務] 在 Synapse 所要求的 [Azure 儲存體帳戶 **防火牆和虛擬網路** 設定] 功能表下，**存取此儲存體帳戶**。

以下是連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有 `https://<accountname>.dfs.core.windows.net` 模式的 Data Lake Storage Gen2 所適用的端點。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，則可使用 Azure Integration Runtime 或自我裝載整合執行階段。 若未指定，則會使用預設 Azure Integration Runtime。 |否 |

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

如需定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型資料集內的 `location` 設定下，Data Lake Storage Gen2 支援下列屬性：

| 屬性   | 說明                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集中 `location` 下的 type 屬性必須設定為 **AzureBlobFSLocation**。 | 是      |
| fileSystem | Data Lake Storage Gen2 檔案系統名稱。                              | 否       |
| folderPath | 所指定檔案系統下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在活動來源設定中指定。 | 否       |
| fileName   | 所指定 fileSystem + folderPath 下方的檔案名稱。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在活動來源設定中指定。 | 否       |

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

如需可用來定義活動的區段和屬性完整清單，請參閱[複製活動組態](copy-activity-overview.md#configuration)和[管線和活動](concepts-pipelines-activities.md)。 本節提供 Data Lake Storage Gen2 來源和接收端所支援的屬性清單。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>以 Azure Data Lake Storage Gen2 作為來源類型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

您有數個選項可從 ADLS Gen2 複製資料：

- 請從資料集內的指定路徑複製。
- 針對資料夾路徑或檔案名的萬用字元篩選，請參閱 `wildcardFolderPath` 和 `wildcardFileName`。
- 將指定文字檔中定義的檔案複製為檔案集，請參閱 `fileListPath`。

在格式型複製來源內的 `storeSettings` 設定下，Data Lake Storage Gen2 支援下列屬性：

| 屬性                 | 說明                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **AzureBlobFSReadSettings**。 | 是                                           |
| **_找出要複製的檔案：_* _ |  |  |
| 選項 1：靜態路徑<br> | 從在資料集內指定的檔案系統或資料夾/檔案路徑複製。 如果您想要複製檔案系統/資料夾中的所有檔案，請另外將 `wildcardFileName` 指定為 `_`。 |  |
| 選項 2：萬用字元<br>- wildcardFolderPath | 在資料集內設定的指定檔案系統底下，具有萬用字元的資料夾路徑，用來篩選來源資料夾。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項 2：萬用字元<br>- wildcardFileName | 在指定 file system + folderPath/wildcardFolderPath 之下，含有萬用字元的檔案名稱，可用來篩選來源檔案。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際檔案名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 3：檔案清單<br>- fileListPath | 表示要複製指定的檔案集。 指向文字檔，其中包含您要複製的檔案清單，每行一個檔案，也就是在資料集中設定之路徑的相對路徑。<br/>使用此選項時，請勿指定資料集中的檔案名稱。 [檔案清單範例](#file-list-examples) (英文) 有更多範例可供參閱。 |否 |
| ***其他設定：** _ |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 <br>允許的值為 _ *true** (預設值) 和 **false**。<br>設定 `fileListPath` 時，不適用此屬性。 |否 |
| deleteFilesAfterCompletion | 指出是否要在成功移至目的地存放區之後，從來源存放區刪除二進位檔案。 檔案刪除是針對每個檔案，因此當複製活動失敗時，您會看到部分檔案已複製到目的地並從來源刪除，其他檔案仍在來源存放區上。 <br/>這個屬性只在二進位檔案複製案例中有效。 預設值： false。 |否 |
| modifiedDatetimeStart    | 檔案篩選會根據以下屬性：上次修改時間。 <br>若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。<br/>設定 `fileListPath` 時，不適用此屬性。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| enablePartitionDiscovery | 針對已分割的檔案，指定是否從檔案路徑剖析分割區，並將它們新增為其他來源資料行。<br/>允許的值為 **false** (預設) 和 **true**。 | 否                                            |
| partitionRootPath | 當資料分割探索已啟用時，請指定絕對根路徑，以便將分割的資料夾讀取為數據行。<br/><br/>如果未指定，則預設為<br/>-當您在資料集或來源上的檔案清單中使用檔案路徑時，資料分割根路徑是在資料集中設定的路徑。<br/>-當您使用萬用字元資料夾篩選時，資料分割根路徑是第一個萬用字元之前的子路徑。<br/><br/>例如，假設您將資料集中的路徑設定為 "root/folder/year = 2020/month = 08/day = 27"：<br/>-如果您將資料分割根路徑指定為 "root/folder/year = 2020"，則除了檔案內的資料行之外，複製活動會分別產生兩個數據行， `month` 以及 `day` 值為 "08" 和 "27" 的資料行。<br/>-如果未指定資料分割根路徑，將不會產生額外的資料行。 | 否                                            |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有當您想要限制同時連線到資料存放區的連線數目時才指定。 | 否                                            |

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

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

在格式型複製接收器內的 `storeSettings` 設定下，Data Lake Storage Gen2 支援下列屬性：

| 屬性                 | 說明                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **AzureBlobFSWriteSettings**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| blockSizeInMB | 指定用於將資料寫入 ADLS Gen2 的區塊大小 (以 MB 為單位)。 深入了解[區塊 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)。 <br/>允許的值 **介於 4 mb 和 100 mb 之間**。 <br/>根據預設，ADF 會根據您的來源存放區類型和資料，自動決定區塊大小。 若為非二進位複製到 ADLS Gen2，預設區塊大小為 100 MB，因此最多可容納 4.95 TB 的資料。 這在資料不大時可能並非最理想，尤其在使用「自我裝載整合執行階段」，而且網路連線不佳的情況下，將導致作業逾時或效能問題。 您可以明確指定區塊大小，同時確保 blockSizeInMB*50000 夠大足以儲存資料，否則複製活動執行會失敗。 | 否 |
| maxConcurrentConnections | 可同時連線到資料存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否       |

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

| folderPath | fileName | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以 **粗體** 顯示的檔案)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空白，使用預設值) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空白，使用預設值) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>檔案清單範例

本節說明使用複製活動來源中的檔案清單路徑時，會產生的行為。

假設您的來源資料夾結構如下，且想要複製粗體的檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | ADF 組態                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| filesystem<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;中繼資料<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **資料集中：**<br>- 檔案系統：`filesystem`<br>- 資料夾路徑：`FolderA`<br><br>**在複製活動來源內：**<br>- 檔案清單路徑：`filesystem/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向同一個資料存放區中的文字檔，內有您想複製的檔案清單，每行一個檔案，以資料集內所設定路徑的相對路徑表示。 |


### <a name="some-recursive-and-copybehavior-examples"></a>一些遞迴和 copyBehavior 範例

本節說明遞迴和 copyBehavior 值在不同組合的情況下，複製作業所產生的行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |

## <a name="preserve-metadata-during-copy"></a>複製期間保留中繼資料

當您將檔案從 Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 複製到 Azure Data Lake Storage Gen2/Azure Blob 時，除了資料以外，還可選擇保留檔案中繼資料。 深入了解[保留中繼資料](copy-activity-preserve-metadata.md#preserve-metadata)。

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> 保留來自 Data Lake Storage Gen1/Gen2 的 ACL

當您將檔案從 Azure Data Lake Storage Gen1/Gen2 複製到 Gen2 時，您可以選擇連同資料一起保存 POSIX 存取控制清單 (ACL)。 深入了解如何[將 ACL 從 Data Lake Storage Gen1/Gen2 保存至 Gen2](copy-activity-preserve-metadata.md#preserve-acls)。

>[!TIP]
>若要在一般情況下將資料從 Azure Data Lake Storage Gen1 複製到 Gen2，請參閱[使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2](load-azure-data-lake-storage-gen2-from-gen1.md)，取得逐步解說和最佳做法。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

當您在對應資料流程中轉換資料時，可以透過下列格式的 Azure Data Lake Storage Gen2 讀取和寫入檔案：
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Common Data Model (preview) ](format-common-data-model.md#mapping-data-flow-properties)
* [分隔符號文字](format-delimited-text.md#mapping-data-flow-properties)
* [三角洲](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

格式化特定的設定位於該格式的檔中。 如需詳細資訊，請參閱對應資料流程中的對應資料流程和[接收轉換](data-flow-sink.md)的[來源轉換](data-flow-source.md)。

### <a name="source-transformation"></a>來源轉換

在來源轉換中，您可以從 Azure Data Lake Storage Gen2 中的容器、資料夾或個別檔案讀取資料。 [來源選項] 索引標籤可讓您管理檔案的讀取方式。 

![來源選項](media/data-flow/sourceOptions1.png "來源選項")

**萬用字元路徑：** 在單一來源轉換中，使用萬用字元模式會指示 ADF 循環檢查每個相符的資料夾和檔案。 這在單一流程內處理多個檔案時很有效。 使用 + 符號來新增多個萬用字元比對模式，當滑鼠停留在現有萬用字元模式上時會出現此符號。

從來源容器中，選擇符合模式的一系列檔案。 在資料集內只能指定容器。 因此，萬用字元路徑也必須包含始於根資料夾的資料夾路徑。

萬用字元範例：

* ```*``` 代表任何一組字元
* ```**``` 代表遞迴目錄巢狀
* ```?``` 取代一個字元
* ```[]``` 比對括弧中的一個或多個字元

* ```/data/sales/**/*.csv``` 取得 /data/sales 下的所有 csv 檔案
* ```/data/sales/20??/**/``` 取得 20 世紀的所有檔案
* ```/data/sales/*/*/*.csv``` 取得 /data/sales 下兩層的 csv 檔案
* ```/data/sales/2004/*/12/[XY]1?.csv``` 取得 2004 年 12 月以 X 或 Y 開頭且字首是兩位數字的所有 csv 檔案

**分割區根路徑：** 如果您的檔案來源中有 ```key=value``` 格式的分割資料夾 (例如 year=2019)，則可以將該分割區資料夾樹狀的最上層，指派給資料流程資料流中的資料行名稱。

首先，請設定萬用字元來包含所有路徑，即分割資料夾加上您想讀取的分葉檔案。

![分割區來源檔案設定](media/data-flow/partfile2.png "分割區檔案設定")

使用 [分割區根路徑] 設定來定義資料夾結構的最上層。 當您透過資料預覽來檢視資料的內容時，將會看到 ADF 會新增每個資料夾層級中找到的已解析分割區。

![分割區根路徑](media/data-flow/partfile1.png "分割區根路徑預覽")

**檔案清單：** 這是檔案集。 建立文字檔，其中包含要處理的相對路徑檔案清單。 指向這個文字檔。

**要儲存檔案名稱的資料行：** 將來源檔案的名稱儲存在資料中的資料行。 在此輸入新的資料行名稱以儲存檔案名稱字串。

**完成後：** 選擇在資料流程後不操作來源檔案、刪除來源檔案、或移動來源檔案。 移動時的路徑是相對路徑。

若要在處理後將來源檔案移到另一個位置，請先選取 [移動] 當作檔案操作。 然後，設定「來源」目錄。 如果路徑中未使用任何萬用字元，則「來源」設定與來源資料夾相同。

如果來源路徑含有萬用字元，則語法如下：

```/data/sales/20??/**/*.csv```

您可以將「來源」指定為

```/data/sales```

將「目的地」指定為

```/backup/priorSales```

在此案例中，/data/sales 中的所有檔案都會移至 /backup/priorSales。

> [!NOTE]
> 只有當您從管線執行 (管線偵錯或執行) 啟動資料流程，且該管線執行在管線中使用「執行資料流程」活動時，檔案操作才會執行。 在資料流程偵錯模式下「不會」執行檔案操作。

**依上次修改時間篩選：** 您可以指定檔案上次修改的日期範圍，以篩選您處理的檔案。 所有日期時間都是 UTC 格式。 

### <a name="sink-properties"></a>接收屬性

在「接收」轉換中，您可以寫入 Azure Data Lake Storage Gen2 中的容器或資料夾。 [設定] 索引標籤可讓您管理檔案的寫入方式。

![接收選項](media/data-flow/file-sink-settings.png "接收選項")

**清除資料夾：** 決定是否在寫入資料之前先清除目的地資料夾。

**檔案名稱選項：** 決定目的地檔案在目的地資料夾中的命名方式。 檔案名稱的選項如下：
   * **預設值**：可讓 Spark 根據 PART 預設值來命名檔案。
   * **模式**：輸入模式來列舉每個分割區的輸出檔案。 例如，**loans[n].csv** 會建立 loans1.csv、loans2.csv，依此類推。
   * **每個分割區**：針對每個分割區輸入一個檔案名稱。
   * **作為資料行中的資料**：將輸出檔案設定為資料行的值。 路徑相對於資料集容器，而不是目的地資料夾。 如果資料集含有資料夾路徑，則會被覆寫。
   * **輸出至單一檔案**：將分割的輸出檔案結合成單一的具名檔案。 路徑相對於資料集資料夾。 請注意，根據節點的大小而定，「合併」作業可能失敗。 對於大型資料集，不建議使用此選項。

**全部以引號括住：** 決定是否用引號括住所有的值

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

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設為 **AzureBlobFSFile**。 |是 |
| folderPath | Data Lake Storage Gen2 中資料夾的路徑。 若未指定，它會指向根。 <br/><br/>支援萬用字元篩選。 允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 如果實際資料夾名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。 <br/><br/>範例：filesystem/folder/。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 在指定 "folderPath" 之下檔案的名稱或萬用字元篩選。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>篩選時，允許的萬用字元為 `*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際檔案名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。<br/><br/>沒有為輸出資料集指定 fileName 且活動接收器中未指定 **preserveHierarchy** 時，複製活動會自動以下列模式產生檔案名稱："Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]"，例如，"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"。 如果您使用資料表名稱 (而非查詢) 從表格來源複製，則名稱模式會是 "[table name].[format].[compression if configured]"，例如 "MyTable.csv"。 |否 |
| modifiedDatetimeStart | 檔案篩選會根據「上次修改」屬性。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 當您想要對大量檔案進行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 此屬性可以是 NULL，這意謂著不會對資料集套用任何檔案屬性篩選。 當 `modifiedDatetimeStart` 有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，表示系統將會選取上次修改時間屬性大於或等於此日期時間值的檔案。 當 `modifiedDatetimeEnd` 有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則表示系統將會選取上次修改時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案篩選會根據「上次修改」屬性。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 當您想要對大量檔案進行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 此屬性可以是 NULL，這意謂著不會對資料集套用任何檔案屬性篩選。 當 `modifiedDatetimeStart` 有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，表示系統將會選取上次修改時間屬性大於或等於此日期時間值的檔案。 當 `modifiedDatetimeEnd` 有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則表示系統將會選取上次修改時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format] 下的 [type] 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[ORC 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製指定名稱的單一檔案，請以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的檔案子集，請以 **folderPath** 指定資料夾部分 ，並以 **fileName** 指定萬用字元篩選。 

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

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設為 **AzureBlobFSSource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。<br/>允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 可同時連線到資料存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否 |

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

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的類型屬性必須設為 **AzureBlobFSSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 可同時連線到資料存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否 |

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