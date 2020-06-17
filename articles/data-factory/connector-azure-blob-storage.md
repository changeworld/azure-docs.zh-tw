---
title: 在 Azure Blob 儲存體中複製和轉換資料
description: 了解如何使用 Data Factory 對 Blob 儲存體來回複製資料，以及在 Blob 儲存體中轉換資料。
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ad24f8db2f8f212ebcccf661241016b438ec730c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655399"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>使用 Azure Data Factory 在 Azure Blob 儲存體中複製和轉換資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-azure-blob-connector.md)
> * [目前的版本](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，對 Azure Blob 儲存體來回複製資料，以及如何使用資料流程在 Azure Blob 儲存體中轉換資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

>[!TIP]
>請參閱[使用 Azure Data Factory 將資料從資料湖或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)，以深入了解資料湖或資料倉儲移轉案例。

## <a name="supported-capabilities"></a>支援的功能

此 Azure Blob 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

對於「複製」活動，此 Blob 儲存體連接器支援：

- 將 Blob 複製到一般用途 Azure 儲存體帳戶和經常性存取/非經常性存取 Blob 儲存體，或從這兩處複製 Blob。 
- 使用「帳戶金鑰」、「服務共用存取簽章」、「服務主體」或「Azure 資源的受控識別」驗證來複製 Blob。
- 從區塊、附加或分頁 Blob 複製 Blob，以及將資料只複製到區塊 Blob。
- 依原樣複製 Blob，或是使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生 Blob。
- [複製期間保留檔案中繼資料](#preserve-metadata-during-copy)。

>[!IMPORTANT]
>如果您在 Azure 儲存體防火牆設定上啟用 [允許信任的 Microsoft 服務存取此儲存體帳戶] 選項，而且想要使用 Azure 整合執行階段來連線到 Blob 儲存體，則必須使用[受控識別驗證](#managed-identity)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Blob 儲存體專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure Blob 連接器支援下列驗證類型，請參閱詳細資料的對應章節：

- [帳戶金鑰驗證](#account-key-authentication)
- [共用存取簽章驗證](#shared-access-signature-authentication)
- [服務主體驗證](#service-principal-authentication)
- [Azure 資源的受控識別驗證](#managed-identity)

>[!NOTE]
>使用 PolyBase 將資料載入 SQL 資料倉儲時，如果來源或暫存 Blob 儲存體已設定虛擬網路端點，您必須使用 PolyBase 所要求的受控識別驗證，並使用 Self-hosted Integration Runtime 3.18 版或更新版本。 關於其他設定必要條件，請參閱[受控識別驗證](#managed-identity)一節。

>[!NOTE]
>HDInsights 和 Azure Machine Learning 活動僅支援 Azure Blob 儲存體帳戶金鑰驗證。

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為**AzureBlobStorage** (建議) 或 **AzureStorage** (請參閱下面附註)。 |是 |
| connectionString | 針對 connectionString 屬性指定連線到儲存體所需的資訊。 <br/> 您也可以將帳戶金鑰放在 Azure Key Vault 並從連接字串中提取 `accountKey` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>使用帳戶金鑰驗證時，不支援次要 Blob 服務端點。 您可以使用其他驗證類型。

>[!NOTE]
>如果您使用 "AzureStorage" 類型連結服務，它仍然如同現狀受支援，但建議您從現在開始使用這個新的 "AzureBlobStorage" 連結服務類型。

**範例︰**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：在 Azure Key Vault 中儲存帳戶金鑰**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>共用存取簽章驗證

共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以使用共用存取簽章來將儲存體帳戶中物件的有限權限授與用戶端，讓該用戶端可以在一段指定時間內使用。 您不需要共用您的帳戶存取金鑰。 共用存取簽章是一種 URI，此 URI 會在其查詢參數中包含對儲存體資源進行驗證式存取所需的一切資訊。 若要使用共用存取簽章存取儲存體資源，用戶端只需在適當的建構函式或方法中傳入共用存取簽章即可。 如需有關共用存取簽章的詳細資訊，請參閱[共用存取簽章：了解共用存取簽章模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
>- Data Factory 現已支援**服務共用存取簽章**和**帳戶共用存取簽章**。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS) 授與 Azure 儲存體資源的有限存取權](../storage/common/storage-sas-overview.md)。
>- 在稍後的資料集設定中，資料夾路徑是從容器層級開始的絕對路徑。 您需要設定一個對應您 SAS URI 中路徑的。

若要使用共用存取簽章驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為**AzureBlobStorage** (建議) 或 **AzureStorage** (請參閱下面附註)。 |是 |
| sasUri | 指定儲存體資源 (例如 Blob/容器) 的共用存取簽章 URI。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將 SAS 權杖放入 Azure Key Vault 中，以運用自動輪替和移除權杖部分。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>如果您使用 "AzureStorage" 類型連結服務，它仍然如同現狀受支援，但建議您從現在開始使用這個新的 "AzureBlobStorage" 連結服務類型。

**範例︰**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：在 Azure Key Vault 中儲存帳戶金鑰**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

當您建立共用存取簽章 URI 時，請考慮下列各點：

- 根據連結的服務 (讀取、寫入、讀取/寫入) 在資料處理站中的使用方式，在物件上設定適當的讀取/寫入權限。
- 設定適當的 [過期時間]。 請確定儲存體物件的存取權不會在管線的作用中期間內過期。
- URI 應根據需求在正確的容器/Blob 上建立。 Blob 的共用存取簽章 URI 可讓 Data Factory 存取該特定 Blob。 Blob 儲存體容器的共用存取簽章 URI 可讓 Data Factory 逐一查看該容器中 Blob。 若要在稍後提供更多或較少物件的存取權，或更新共用存取簽章 URI，請記得使用新的 URI 更新連結的服務。

### <a name="service-principal-authentication"></a>服務主體驗證

如需一般的 Azure 儲存體服務主體驗證，請參閱[使用 Azure Active Directory 驗證 Azure 儲存體的存取權](../storage/common/storage-auth-aad.md)。

若要使用服務主體驗證，請遵循下列步驟：

1. 遵循[使用 Azure AD 租用戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，以在 Azure Active Directory (Azure AD) 中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 在 Azure Blob 儲存體中授與服務主體適當權限。 如需角色的詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](../storage/common/storage-auth-aad-rbac.md) \(部分機器翻譯\)。

    - 若是存取控制 (IAM) 中的**來源**，至少授與**儲存體 Blob 資料讀者**角色。
    - **作為接收**，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料參與者] 角色。

以下是支援 Azure Blob 儲存體連結服務的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **AzureStorageSas**。 |是 |
| serviceEndpoint | 指定模式為 `https://<accountName>.blob.core.windows.net/` 的 Azure Blob 儲存體服務端點。 |是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>服務主體驗證僅由 "AzureBlobStorage" 類型連結服務支援，但先前的 "AzureStorage" 類型連結服務不提供支援。

**範例︰**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接將此受控識別用於 Blob 儲存體驗證，類似於使用您自己的服務主體。 這可以讓這個指定的處理站從 Data Lake Store 存取及複製資料，或存取及複製資料至 Blob 儲存體。

如需 Azure 儲存體驗證的一般資訊，請參閱[使用 Azure Active Directory 授權存取 Azure Blob 和佇列](../storage/common/storage-auth-aad.md) \(部分機器翻譯\)。 若要使用 Azure 資源的受控識別驗證，請遵循下列步驟：

1. 複製隨著資料處理站一起產生的**受控識別物件識別碼**的值，以[擷取資料處理站受控識別資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 將 Azure Blob 儲存體中適當的權限授與受控識別。 如需角色的詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](../storage/common/storage-auth-aad-rbac.md) \(部分機器翻譯\)。

    - 若是存取控制 (IAM) 中的**來源**，至少授與**儲存體 Blob 資料讀者**角色。
    - **作為接收**，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料參與者] 角色。

>[!IMPORTANT]
>如果您使用 PolyBase 將資料從 Blob (來源或暫存) 載入 SQL 資料倉儲，則在對 Blob 使用受控識別驗證時，請確定您也遵循[本指引](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步驟 1 和 2 執行這些動作：1) 向 Azure Active Directory (Azure AD) 註冊 SQL Database 伺服器，2) 將儲存體 Blob 資料參與者角色指派給 SQL Database 伺服器；其餘部分由 Data Factory 處理。 如果 Blob 儲存體已設定 Azure 虛擬網路端點，若要使用 PolyBase 從儲存體載入資料，您必須使用 PolyBase 所要求的受控識別驗證。

以下是支援 Azure Blob 儲存體連結服務的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **AzureStorageSas**。 |是 |
| serviceEndpoint | 指定模式為 `https://<accountName>.blob.core.windows.net/` 的 Azure Blob 儲存體服務端點。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

> [!NOTE]
> Azure 資源的受控識別驗證僅由 "AzureBlobStorage" 類型連結服務支援，但先前的 "AzureStorage" 類型連結服務不提供支援。 

**範例︰**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
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

在格式型資料集的 `location` 設定下，Azure Blob 支援下列屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集的位置 type 屬性必須設定為 **AzureBlobStorageLocation**。 | 是      |
| 容器  | Blob 容器。                                          | 是      |
| folderPath | 特定容器下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在活動來源設定中指定。 | 否       |
| fileName   | 特定容器 + folderPath 下的檔案名稱。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在活動來源設定中指定。 | 否       |

**範例︰**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Blob 儲存體來源和接收所支援的屬性清單。

### <a name="blob-storage-as-a-source-type"></a>Blob 儲存體作為來源類型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型複製來源的 `storeSettings` 設定下，Azure Blob 支援下列屬性：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **AzureBlobStorageReadSettings**。 | 是                                           |
| 找出要複製的檔案： |  |  |
| 選項 1：靜態路徑<br> | 從資料集內指定的特定容器或資料夾/檔案路徑複製。 如果您想要複製容器/資料夾中的所有 blob，請另外將 `wildcardFileName` 指定為 `*`。 |  |
| 選項2：blob 前置詞<br>- 前置詞 | 在資料集內設定的特定容器下，blob 名稱的前置詞，用於篩選來源 blob。 將會選取名稱開頭為 `container_in_dataset/this_prefix` 的 blob。 使用的是 Blob 服務端篩選條件，效能高於萬用字元篩選條件。 | 否                                                          |
| 選項 3：萬用字元<br>- wildcardFolderPath | 在資料集內設定的特定容器下，含有萬用字元的資料夾路徑，用於篩選來源資料夾。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項 3：萬用字元<br>- wildcardFileName | 在特定容器 + folderPath/wildcardFolderPath 下含有萬用字元的檔案名稱，用於篩選來源檔案。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 4：檔案清單<br>- fileListPath | 表示要複製指定的檔案集。 請指向文字檔，內有您想複製的檔案清單，每行一個檔案，以資料集內所設定路徑的相對路徑表示。<br/>使用此選項時，請勿在資料集內指定檔案名稱。 [檔案清單範例](#file-list-examples)有更多範例可供參閱。 |否 |
| 其他設定： |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 <br>允許的值為 **true** (預設值) 和 **false**。<br>設定 `fileListPath` 時，此屬性不適用。 |否 |
| modifiedDatetimeStart    | 檔案篩選會根據以下屬性：上次修改時間。 <br>如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。<br/>設定 `fileListPath` 時，此屬性不適用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否                                            |

> [!NOTE]
> 針對 Parquet/分隔文字格式，基於回溯相容性，仍照現狀支援下一節所述的 **BlobSource** 類型複製活動來源。 建議您往後使用這個新模型，ADF 製作使用者介面已轉為產生這些新類型。

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>Blob 儲存體作為接收類型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型複製接收的 `storeSettings` 設定下，Azure Blob 支援下列屬性：

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **AzureBlobStorageWriteSettings**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 如果有指定檔案或 Blob 名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| blockSizeInMB | 指定用於將資料寫入區塊 Blob 的區塊大小 (以 MB 為單位)。 請參閱[關於區塊 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 以深入了解。 <br/>允許的值**介於 4 到 100 MB 之間**。 <br/>根據預設，ADF 會根據來源存放區類型和資料，自動決定區塊大小。 進行非二進位複製到 blob 時，預設區塊大小為 100 MB，因此最多可容納 4.95 TB 的資料。 這在資料不大時可能並非最理想，尤其在使用「自我裝載整合執行階段」，而且網路連線不佳的情況下，將導致作業逾時或效能問題。 您可以明確指定區塊大小，同時確保 blockSizeInMB*50000 夠大足以儲存資料，否則複製活動執行會失敗。 | 否 |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否       |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
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
| `container/Folder*` | (空白，使用預設值) | false | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (空白，使用預設值) | true | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>檔案清單範例

本節說明在複製活動來源中使用檔案清單路徑時所產生的行為。

假設您的來源資料夾結構如下，且想要複製以粗體標示的檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | ADF 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集內：**<br>- 容器：`container`<br>- 資料夾路徑：`FolderA`<br><br>**在複製活動來源中：**<br>- 檔案清單路徑：`container/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向同一個資料存放區中的文字檔，內有您想複製的檔案清單，每行一個檔案，以資料集內所設定路徑的相對路徑表示。 |

### <a name="some-recursive-and-copybehavior-examples"></a>一些遞迴和 copyBehavior 範例

本節說明遞迴和 copyBehavior 值在不同組合的情況下，複製作業所產生的行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |

## <a name="preserve-metadata-during-copy"></a>複製期間保留中繼資料

當您將檔案從 Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 複製到 Azure Data Lake Storage Gen2/Azure Blob 時，除了資料以外，還可選擇保留檔案中繼資料。 深入了解[保留中繼資料](copy-activity-preserve-metadata.md#preserve-metadata)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中轉換資料時，您可以用 JSON、Avro、分隔文字或 Parquet 格式，在 Azure Blob 儲存體中讀取和寫入檔案。 如需詳細資訊，請參閱對應資料流程功能中的[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>來源轉換

在來源轉換中，您可以讀取 Azure Blob 儲存體中的容器、資料夾或個別檔案。 [來源選項] 索引標籤可讓您管理檔案的讀取方式。 

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

在「接收」轉換中，您可以寫入 Azure Blob 儲存體中的容器或資料夾。 [設定] 索引標籤可讓您管理檔案的寫入方式。

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

若要了解屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要了解屬性的詳細資料，請參閱 [GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要了解屬性的詳細資料，請參閱[刪除活動](delete-activity.md)

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>基於回溯相容性，仍照現狀支援下列模型。 建議您往後使用以上各節所述的新模型，ADF 製作使用者介面已轉為產生新模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為 **AzureBlob**。 |是 |
| folderPath | Blob 儲存體中容器和資料夾的路徑。 <br/><br/>針對不包含容器名稱的路徑，支援萬用字元篩選。 允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br/><br/>範例：myblobcontainer/myblobfolder/，如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |[是] 適用於複製/查閱活動，[否] 適用於 GetMetadata 活動 |
| fileName | 在指定 "folderPath" 下之 Blob 的**名稱或萬用字元篩選**。 如果沒有為此屬性指定值，資料集就會指向資料夾中的所有 Blob。 <br/><br/>針對篩選，允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際檔案名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。<br/><br/>沒有為輸出資料集指定 fileName 且活動接收器中未指定 **preserveHierarchy** 時，複製活動會自動以下列模式產生 Blob 名稱："*Data.[活動執行識別碼 GUID].[GUID (如果為 FlattenHierarchy)].[格式 (如果已設定)].[壓縮 (如果已設定)]* "，例如"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"；如果您使用資料表名稱而非查詢，從表格式來源進行複製，則名稱模式會是 " *[資料表名稱].[格式].[壓縮 (如果已設定)]* "，例如"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案中篩選檔案時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，這意謂著不會對資料集套用任何檔案屬性篩選條件。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案中篩選檔案時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，這意謂著不會對資料集套用任何檔案屬性篩選條件。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format] 下的 [type] 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)小節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有 Blob，只要指定 **folderPath**。<br>若要複製指定名稱的單一 Blob，以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的 Blob 子集，以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。 

**範例︰**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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
| type | 複製活動來源的 type 屬性必須設定為 **BlobSource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。<br/>允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
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
| type | 複製活動接收的 type 屬性必須設定為 **BlobSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 如果有指定檔案或 Blob 名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟

如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
