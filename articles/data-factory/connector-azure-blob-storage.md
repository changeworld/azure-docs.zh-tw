---
title: 複製和轉換 Azure Blob 儲存體中的資料
description: 瞭解如何在 Blob 儲存體之間複製資料，以及如何使用 Data Factory 在 Blob 儲存體中轉換資料。
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: 3a67dc68415dacaa024fae1b1de97bb82eb65723
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891577"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>使用 Azure Data Factory 複製和轉換 Azure Blob 儲存體中的資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-azure-blob-connector.md)
> * [目前的版本](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Azure Blob 儲存體複製資料，以及使用資料流程來轉換 Azure Blob 儲存體中的資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

>[!TIP]
>針對 data lake 或資料倉儲遷移案例，深入瞭解[如何使用 Azure Data Factory 將資料從您的 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure Blob 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

針對複製活動，此 Blob 儲存體連接器支援：

- 將 Blob 複製到一般用途 Azure 儲存體帳戶和經常性存取/非經常性存取 Blob 儲存體，或從這兩處複製 Blob。 
- 使用「帳戶金鑰」、「服務共用存取簽章」、「服務主體」或「Azure 資源的受控識別」驗證來複製 Blob。
- 從區塊、附加或分頁 Blob 複製 Blob，以及將資料只複製到區塊 Blob。
- 依原樣複製 Blob，或是使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生 Blob。
- [複製期間保留檔案中繼資料](#preserve-metadata-during-copy)。

>[!IMPORTANT]
>如果您在 Azure 儲存體防火牆設定上啟用 [**允許受信任的 Microsoft 服務存取此儲存體帳戶**] 選項，而且想要使用 Azure 整合執行時間來連線到您的 Blob 儲存體，則必須使用[受控識別驗證](#managed-identity)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Blob 儲存體專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure Blob 連接器支援下列驗證類型，請參閱詳細資料的對應章節：

- [帳戶金鑰驗證](#account-key-authentication)
- [共用存取簽章驗證](#shared-access-signature-authentication)
- [服務主體驗證](#service-principal-authentication)
- [Azure 資源驗證的受控識別](#managed-identity)

>[!NOTE]
>使用 PolyBase 將資料載入 SQL 資料倉儲時，如果您的來源或暫存 Blob 儲存體已設定虛擬網路端點，您必須使用 PolyBase 所需的受控識別驗證，並使用3.18 版或更新版本的自我裝載 Integration Runtime。 如需更多設定必要條件，請參閱[受控識別驗證](#managed-identity)一節。

>[!NOTE]
>HDInsights 和 Azure Machine Learning 活動僅支援 Azure Blob 儲存體帳戶金鑰驗證。

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為**AzureBlobStorage** (建議) 或 **AzureStorage** (請參閱下面附註)。 |是 |
| connectionString | 針對 connectionString 屬性指定連線到儲存體所需的資訊。 <br/> 您也可以將帳戶金鑰放在 Azure Key Vault 並從連接字串中提取 `accountKey` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 |是 |
| connectVia | 要用來連接到資料存放區的[整合運行](concepts-integration-runtime.md)時間。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>使用帳戶金鑰驗證時，不支援次要 Blob 服務端點。 您可以使用其他驗證類型。

>[!NOTE]
>如果您使用 "AzureStorage" 類型連結服務，它仍然如同現狀受支援，但建議您從現在開始使用這個新的 "AzureBlobStorage" 連結服務類型。

**範例：**

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

共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以使用共用存取簽章來將儲存體帳戶中物件的有限權限授與用戶端，讓該用戶端可以在一段指定時間內使用。 您不需要共用您的帳戶存取金鑰。 共用存取簽章是一種 URI，此 URI 會在其查詢參數中包含對儲存體資源進行驗證式存取所需的一切資訊。 若要使用共用存取簽章存取儲存體資源，用戶端只需在適當的建構函式或方法中傳入共用存取簽章即可。 如需共用存取簽章的詳細資訊，請參閱[共用存取簽章：了解共用存取簽章模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
>- Data Factory 現已支援**服務共用存取簽章**和**帳戶共用存取簽章**。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](../storage/common/storage-sas-overview.md)。
>- 在稍後的資料集設定中，資料夾路徑是從容器層級開始的絕對路徑。 您需要設定一個對應您 SAS URI 中路徑的。

若要使用共用存取簽章驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為**AzureBlobStorage** (建議) 或 **AzureStorage** (請參閱下面附註)。 |是 |
| sasUri | 指定儲存體資源 (例如 Blob/容器) 的共用存取簽章 URI。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將 SAS 權杖放在 Azure Key Vault 中，以運用自動輪替並移除權杖部分。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 |是 |
| connectVia | 要用來連接到資料存放區的[整合運行](concepts-integration-runtime.md)時間。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>如果您使用 "AzureStorage" 類型連結服務，它仍然如同現狀受支援，但建議您從現在開始使用這個新的 "AzureBlobStorage" 連結服務類型。

**範例：**

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
- 設定適當的 [過期時間]****。 請確定儲存體物件的存取權不會在管線的作用中期間內過期。
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
    - **作為接收**，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料參與者]**** 角色。

以下是支援 Azure Blob 儲存體連結服務的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **AzureStorageSas**。 |是 |
| serviceEndpoint | 指定模式為 `https://<accountName>.blob.core.windows.net/` 的 Azure Blob 儲存體服務端點。 |是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |
| connectVia | 要用來連接到資料存放區的[整合運行](concepts-integration-runtime.md)時間。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>服務主體驗證僅由 "AzureBlobStorage" 類型連結服務支援，但先前的 "AzureStorage" 類型連結服務不提供支援。

**範例：**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>適用于 Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接將此受控識別用於 Blob 儲存體驗證，類似于使用您自己的服務主體。 這可以讓這個指定的處理站從 Data Lake Store 存取及複製資料，或存取及複製資料至 Blob 儲存體。

如需 Azure 儲存體驗證的一般資訊，請參閱[使用 Azure Active Directory 授權存取 Azure Blob 和佇列](../storage/common/storage-auth-aad.md) \(部分機器翻譯\)。 若要使用 Azure 資源的受控識別驗證，請遵循下列步驟：

1. 藉由複製與您的處理站一起產生的**受控識別物件識別碼**值，來[取出 data factory 受控識別資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 將 Azure Blob 儲存體中適當的權限授與受控識別。 如需角色的詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](../storage/common/storage-auth-aad-rbac.md) \(部分機器翻譯\)。

    - 若是存取控制 (IAM) 中的**來源**，至少授與**儲存體 Blob 資料讀者**角色。
    - **作為接收**，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料參與者]**** 角色。

>[!IMPORTANT]
>如果您使用 PolyBase 將資料從 Blob （做為來源或暫存）載入至 SQL 資料倉儲，使用 Blob 的受控識別驗證時，請確定您也遵循[本指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步驟1和2到1）向 Azure Active Directory （Azure AD）註冊您的 SQL Database 伺服器，並2）將儲存體 Blob 資料參與者角色指派給您的 SQL Database 伺服器;其餘部分則由 Data Factory 處理。 如果您的 Blob 儲存體已設定 Azure 虛擬網路端點，若要使用 PolyBase 來載入資料，您必須使用 PolyBase 所需的受控識別驗證。

以下是支援 Azure Blob 儲存體連結服務的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **AzureStorageSas**。 |是 |
| serviceEndpoint | 指定模式為 `https://<accountName>.blob.core.windows.net/` 的 Azure Blob 儲存體服務端點。 |是 |
| connectVia | 要用來連接到資料存放區的[整合運行](concepts-integration-runtime.md)時間。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

> [!NOTE]
> Azure 資源的受控識別驗證僅由 "AzureBlobStorage" 類型連結服務支援，但先前的 "AzureStorage" 類型連結服務不提供支援。 

**範例：**

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

下列屬性在以格式為基礎的資料`location`集的 [設定] 下支援 Azure Blob：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Dataset 中位置的 type 屬性必須設定為**AzureBlobStorageLocation**。 | 是      |
| 容器  | Blob 容器。                                          | 是      |
| folderPath | 指定容器下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源設定] 中指定。 | 否       |
| fileName   | 指定容器的檔案名 + folderPath。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在 [活動來源設定] 中指定。 | 否       |

**範例：**

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

下列屬性在以格式為基礎之複製`storeSettings`來源的 [設定] 下支援 Azure Blob：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 底下的 type 屬性`storeSettings`必須設定為**AzureBlobStorageReadSettings**。 | 是                                           |
| 遞迴                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 允許的值為**true** （預設值）和**false**。 | 否                                            |
| prefix                   | 在資料集內設定的指定容器下，blob 名稱的前置詞，用來篩選來源 blob。 系統會選取名稱開頭為此前置詞的 blob。 <br>只有在未`wildcardFolderPath`指定`wildcardFileName`和屬性時才適用。 | 否                                                          |
| wildcardFolderPath       | 在資料集內設定的指定容器下，具有萬用字元的資料夾路徑，用以篩選來源資料夾。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| wildcardFileName         | 在指定容器 + folderPath/wildcardFolderPath 下具有萬用字元的檔案名，用以篩選來源檔案。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 如果`fileName`未在 dataset 中指定，則為是 |
| modifiedDatetimeStart    | 檔案會根據屬性進行篩選：上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 連接到儲存體存放區的連線數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否                                            |

> [!NOTE]
> 針對 Parquet/分隔文字格式，下一節中所提及的**BlobSource**類型複製活動來源仍然受到回溯相容性的支援。 建議您繼續使用此新模型，而 ADF 撰寫 UI 已切換為產生這些新的類型。

**範例：**

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

下列屬性在以格式為基礎的複製`storeSettings`接收的設定下支援 Azure Blob：

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 底下的 type 屬性`storeSettings`必須設定為**AzureBlobStorageWriteSettings**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>-PreserveHierarchy （預設值）</b>：保留目的檔案夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>-MergeFiles</b>：將源資料夾中的所有檔案合併成一個檔案。 如果有指定檔案或 Blob 名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| blockSizeInMB | 指定用來將資料寫入區塊 blob 的區塊大小（以 MB 為單位）。 深入瞭解[區塊 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)。 <br/>允許的值**介於4到 100 MB 之間**。 <br/>根據預設，ADF 會依據您的來源存放區類型和資料，自動決定區塊大小。 若為非二進位複製到 Blob，預設區塊大小為 100 MB，因此最多可容納 4.95 TB 的資料。 當您的資料不大時，特別是當您使用具有不佳網路的自我裝載 Integration Runtime 導致作業超時或效能問題時，這可能不是最佳做法。 您可以明確指定區塊大小，同時確保 blockSizeInMB * 50000 夠大以儲存資料，否則複製活動執行將會失敗。 | 否 |
| maxConcurrentConnections | 連接到儲存體存放區的連線數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否       |

**範例：**

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

| folderPath | fileName | 遞迴 | 源資料夾結構和篩選結果（會取出以**粗體顯示**的檔案）|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (空白，使用預設值) | false | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (空白，使用預設值) | true | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3 .csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3 .csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

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

當您將檔案從 Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 複製到 Azure Data Lake Storage Gen2/Azure Blob 時，您可以選擇保留檔案中繼資料和資料。 深入瞭解[保留中繼資料](copy-activity-preserve-metadata.md#preserve-metadata)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中轉換資料時，您可以從 Azure Blob 儲存體以 JSON、Avro、分隔文字或 Parquet 格式讀取和寫入檔案。 如需詳細資訊，請參閱對應資料流程功能中的[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>來源轉換

在「來源」轉換中，您可以在 Azure Blob 儲存體中讀取容器、資料夾或個別檔案。 [**來源選項**] 索引標籤可讓您管理檔案的讀取方式。 

![來源選項](media/data-flow/sourceOptions1.png "來源選項")

**萬用字元路徑：** 使用萬用字元模式會指示 ADF 在單一來源轉換中，迴圈處理每個相符的資料夾和檔案。 這是在單一流程內處理多個檔案的有效方式。 當滑鼠停留在現有的萬用字元模式上時，使用顯示的 + 符號新增多個萬用字元符合模式。

從您的來源容器中，選擇符合模式的一系列檔案。 只有容器可以在資料集內指定。 因此，您的萬用字元路徑也必須包含根資料夾中的資料夾路徑。

萬用字元範例：

* ```*```代表任何一組字元
* ```**```表示遞迴目錄的嵌套
* ```?```取代一個字元
* ```[]```比對括弧中的一個或多個字元

* ```/data/sales/**/*.csv```取得/data/sales 下的所有 csv 檔案
* ```/data/sales/20??/**/```取得20世紀的所有檔案
* ```/data/sales/*/*/*.csv```取得 csv 檔案/data/sales 下的兩個層級
* ```/data/sales/2004/*/12/[XY]1?.csv```取得2004年12月的所有 csv 檔案，從 X 或 Y 前面加上兩位數的數位

資料**分割根路徑：** 如果您的檔案來源中有使用格式的```key=value```資料分割資料夾（例如 year = 2019），則您可以將該資料分割資料夾樹狀結構的最上層指派給資料流程資料流程中的資料行名稱。

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

在「接收」轉換中，您可以寫入 Azure Blob 儲存體中的容器或資料夾。 [**設定**] 索引標籤可讓您管理檔案的寫入方式。

![接收選項](media/data-flow/file-sink-settings.png "接收選項")

**清除資料夾：** 決定是否在寫入資料之前清除目的資料夾。

**檔案名選項：** 決定目的地檔案在目的地資料夾中的命名方式。 [檔案名] 選項如下：
   * **預設值**：允許 SPARK 根據部分預設值來命名檔案。
   * **模式**：輸入可列舉每個分割區輸出檔案的模式。 例如，**貸款 [n] .csv**會建立 loans1 .csv、loans2 等等。
   * **每個資料分割**：每個分割區輸入一個檔案名。
   * 當做**資料行中的資料**：將輸出檔案設為數據行的值。 路徑相對於資料集容器，而不是目的地資料夾。 如果您的資料集內有資料夾路徑，則會覆寫它。
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
| type | 資料集的類型屬性必須設定為**AzureBlob**。 |是 |
| folderPath | Blob 儲存體中容器和資料夾的路徑。 <br/><br/>針對不包含容器名稱的路徑，支援萬用字元篩選。 允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br/><br/>範例：myblobcontainer/myblobfolder/，如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |[是] 適用於複製/查閱活動，[否] 適用於 GetMetadata 活動 |
| fileName | 在指定 "folderPath" 下之 Blob 的**名稱或萬用字元篩選**。 如果沒有為此屬性指定值，資料集就會指向資料夾中的所有 Blob。 <br/><br/>針對篩選，允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際檔案名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。<br/><br/>當未指定輸出資料集的 fileName，且在活動接收中未指定**preserveHierarchy**時，複製活動會自動以下列模式產生 blob 名稱： "*Data. [活動執行識別碼 GUID]。[如果 FlattenHierarchy，則為 GUID]。[格式化（若已設定）]。[已設定壓縮]*"，例如" Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a .txt. gz ";如果您使用 [資料表名稱] 而非 [查詢] 從表格式來源複製，則名稱模式會是 "*[資料表名稱]. [格式]。[已設定壓縮]*"，例如" MyTable. csv "。 |否 |
| modifiedDatetimeStart | 檔案會根據屬性進行篩選：上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案進行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，表示不會將檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案會根據屬性進行篩選：上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案進行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，表示不會將檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要以特定格式來剖析或產生檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format]**** 下的 [type]**** 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)小節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip****、Deflate****、BZip2**** 及 ZipDeflate****。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有 Blob，只要指定 **folderPath**。<br>若要複製指定名稱的單一 Blob，以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的 Blob 子集，以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。 

**範例：**

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
| type | 複製活動來源的類型屬性必須設定為**BlobSource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。<br/>允許的值為**true** （預設值）和**false**。 | 否 |
| maxConcurrentConnections | 連接到儲存體存放區的連線數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

**範例：**

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
| type | 複製活動接收器的 type 屬性必須設定為**BlobSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>-PreserveHierarchy （預設值）</b>：保留目的檔案夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>-MergeFiles</b>：將源資料夾中的所有檔案合併成一個檔案。 如果有指定檔案或 Blob 名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 連接到儲存體存放區的連線數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

**範例：**

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
