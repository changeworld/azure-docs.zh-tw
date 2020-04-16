---
title: 複製並轉換 Azure Blob 儲存中的資料
description: 瞭解如何將資料複製到 Blob 儲存中,以及使用資料工廠轉換 Blob 儲存中的數據。
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b04ff409c95980a1569a2709a475dd8ec74d59b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415474"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>使用 Azure 資料工廠複製並轉換 Azure Blob 儲存中的資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-azure-blob-connector.md)
> * [目前版本](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 資料工廠中的複製活動從 Azure Blob 儲存複製數據,以及使用資料流在 Azure Blob 儲存中轉換數據。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

>[!TIP]
>對於資料湖或資料倉庫遷移方案,請從[使用 Azure 資料工廠將數據從資料湖或數據倉庫遷移到 Azure](data-migration-guidance-overview.md)中瞭解詳細資訊。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Azure Blob 連接器:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [對應資料串流](concepts-data-flow-overview.md)
- [尋找活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [移除活動](delete-activity.md)

對複製活動,此 Blob 儲存連線器支援:

- 將 Blob 複製到一般用途 Azure 儲存體帳戶和經常性存取/非經常性存取 Blob 儲存體，或從這兩處複製 Blob。 
- 使用「帳戶金鑰」、「服務共用存取簽章」、「服務主體」或「Azure 資源的受控識別」驗證來複製 Blob。
- 從區塊、附加或分頁 Blob 複製 Blob，以及將資料只複製到區塊 Blob。
- 依原樣複製 Blob，或是使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生 Blob。
- [在複製期間保留檔案中繼資料](#preserve-metadata-during-copy)。

>[!IMPORTANT]
>如果啟用 **「允許受信任的 Microsoft 服務」在**Azure 儲存防火牆設定上存取此儲存帳戶選項,並且希望使用 Azure 整合執行時連接到 Blob 儲存,則必須使用[託管識別身分驗證](#managed-identity)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Blob 儲存體專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure Blob 連接器支援下列驗證類型，請參閱詳細資料的對應章節：

- [帳戶金鑰驗證](#account-key-authentication)
- [共用存取簽章驗證](#shared-access-signature-authentication)
- [服務主體身份驗證](#service-principal-authentication)
- [Azure 資源驗證的受控識別](#managed-identity)

>[!NOTE]
>使用 PolyBase 將數據載入到 SQL 資料倉儲時,如果源或暫存 Blob 儲存配置了虛擬網路終結點,則必須按照 PolyBase 的要求使用託管標識身份驗證,並使用版本 3.18 或以上的自我託管整合時。 有關更多配置先決條件,請參閱[託管標識身份驗證](#managed-identity)部分。

>[!NOTE]
>HDInsights 和 Azure 機器學習活動僅支援 Azure Blob 儲存帳戶密鑰身份驗證。

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為**AzureBlobStorage** (建議) 或 **AzureStorage** (請參閱下面附註)。 |是 |
| connectionString | 針對 connectionString 屬性指定連線到儲存體所需的資訊。 <br/> 您也可以將帳戶金鑰放在 Azure Key Vault 並從連接字串中提取 `accountKey` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 |是 |
| connectVia | 連線到資料儲存的[整合時](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>使用帳戶金鑰身份驗證時,不支援輔助 Blob 服務終結點。 您可以使用其他身份驗證類型。

>[!NOTE]
>如果您使用 "AzureStorage" 類型連結服務，它仍然如同現狀受支援，但建議您從現在開始使用這個新的 "AzureBlobStorage" 連結服務類型。

**範例:**

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
>- Data Factory 現已支援**服務共用存取簽章**和**帳戶共用存取簽章**。 有關共享存取簽章的詳細資訊,請參閱[使用共享存取簽章 (SAS) 授予對 Azure 資源的有限存取權限](../storage/common/storage-sas-overview.md)。
>- 在稍後的資料集設定中，資料夾路徑是從容器層級開始的絕對路徑。 您需要設定一個對應您 SAS URI 中路徑的。

若要使用共用存取簽章驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為**AzureBlobStorage** (建議) 或 **AzureStorage** (請參閱下面附註)。 |是 |
| sasUri | 指定儲存體資源 (例如 Blob/容器) 的共用存取簽章 URI。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您還可以在 Azure 密鑰保管庫中放置 SAS 令牌,以利用自動旋轉並刪除權杖部分。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 |是 |
| connectVia | 連線到資料儲存的[整合時](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>如果您使用 "AzureStorage" 類型連結服務，它仍然如同現狀受支援，但建議您從現在開始使用這個新的 "AzureBlobStorage" 連結服務類型。

**範例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
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
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
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
| connectVia | 連線到資料儲存的[整合時](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!NOTE]
>服務主體驗證僅由 "AzureBlobStorage" 類型連結服務支援，但先前的 "AzureStorage" 類型連結服務不提供支援。

**範例:**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 資源識別認證的託管識別

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接使用此託管標識進行 Blob 儲存身份驗證,類似於使用您自己的服務主體。 這可以讓這個指定的處理站從 Data Lake Store 存取及複製資料，或存取及複製資料至 Blob 儲存體。

如需 Azure 儲存體驗證的一般資訊，請參閱[使用 Azure Active Directory 授權存取 Azure Blob 和佇列](../storage/common/storage-auth-aad.md) \(部分機器翻譯\)。 若要使用 Azure 資源的受控識別驗證，請遵循下列步驟：

1. 以複製與工廠一起產生的**託管識別物件 ID**的值來[檢索資料工廠託管識別資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 將 Azure Blob 儲存體中適當的權限授與受控識別。 如需角色的詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](../storage/common/storage-auth-aad-rbac.md) \(部分機器翻譯\)。

    - 若是存取控制 (IAM) 中的**來源**，至少授與**儲存體 Blob 資料讀者**角色。
    - **作為接收**，在存取控制 (IAM) 中，至少授與 [儲存體 Blob 資料參與者]**** 角色。

>[!IMPORTANT]
>如果使用 PolyBase 將數據從 Blob(作為源或作為暫存)載入到 SQL 資料倉庫中,則在使用 Blob 的託管標識身份驗證時,請確保也按照[本指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步驟 1 和 2 註冊 SQL 資料庫伺服器(Azure 活動目錄 (Azure AD), 2) 將儲存 Blob 資料參與者角色分配給 SQL 資料庫伺服器;其餘的由數據工廠處理。 如果 Blob 儲存配置了 Azure 虛擬網路終結點,則要使用 PolyBase 從它載入資料,則必須按照 PolyBase 的要求使用託管標識身份驗證。

以下是支援 Azure Blob 儲存體連結服務的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **AzureStorageSas**。 |是 |
| serviceEndpoint | 指定模式為 `https://<accountName>.blob.core.windows.net/` 的 Azure Blob 儲存體服務端點。 |是 |
| connectVia | 連線到資料儲存的[整合時](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

> [!NOTE]
> Azure 資源的受控識別驗證僅由 "AzureBlobStorage" 類型連結服務支援，但先前的 "AzureStorage" 類型連結服務不提供支援。 

**範例:**

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

在基於格式的資料集中的設定`location`下 ,Azure Blob 支援以下屬性:

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集中位置的類型屬性必須設定為**AzureBlob 儲存位置**。 | 是      |
| 容器  | blob 容器。                                          | 是      |
| folderPath | 給定容器下的資料夾的路徑。 如果要使用通配符篩選資料夾,請跳過此設置並在活動源設置中指定。 | 否       |
| fileName   | 給定容器 + 資料夾路徑下的檔名。 如果要使用通配符篩選檔,請跳過此設置並在活動源設置中指定。 | 否       |

**範例:**

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

在基於格式的複本來源中的設定下`storeSettings`,Azure Blob 支援以下屬性:

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下`storeSettings`的類型屬性必須設定為**AzureBlob 儲存讀取設定**。 | 是                                           |
| 遞迴                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 允許的值**為 true(** 預設值) 與**false**。 | 否                                            |
| prefix                   | 在資料集中設定為篩選源 blob 的給定容器下的 blob 名稱的前置字串。 其名稱以此前綴開頭的 Blob 被選中。 <br>僅適用於未指定`wildcardFolderPath``wildcardFileName`和 屬性時。 | 否                                                          |
| 通配子資料夾路徑       | 數據集中配置的給定容器下的通配符的資料夾路徑,用於篩選源資料夾。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 萬用字串檔名稱         | 給定容器下的通配符的檔名 + 資料夾Path/通配符FolderFolderPath用於篩選源檔。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 如果`fileName`資料集中未指定,則為 |
| modifiedDatetimeStart    | 基於屬性的檔篩選器:上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到數據存儲時,才指定。 | 否                                            |

> [!NOTE]
> 對於 Parquet/分隔文字格式,下一節中提及的**BlobSource**類型複製活動源仍支援"作為"向後相容性。 建議您今後使用此新模型,並且 ADF 創作 UI 已切換到生成這些新類型。

**範例:**

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

在基於格式的複本接收器中的設定下`storeSettings`,Azure Blob 支援以下屬性:

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下`storeSettings`的類型屬性必須設定為**AzureBlob 儲存寫入設定**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- 保留層次結構(預設):</b>保留目標資料夾中的檔層次結構。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- 合併檔案</b>:將源資料夾中的所有檔案合併到一個檔案。 如果有指定檔案或 Blob 名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| 塊大小因MB | 指定用於寫入數據以阻止 Blob 的 MB 中的區塊大小。 [瞭解有關區塊的多一個 。](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) <br/>允許的值**介於 4 到 100 MB 之間**。 <br/>預設情況下,ADF 會根據您的來源儲存類型和數據自動確定塊大小。 對於非二進位複製到 Blob,預設區塊大小為 100 MB,以便最多容納 4.95 TB 資料。 當數據不大時,它可能是最佳的,尤其是在使用自託管集成運行時時,網路不佳導致操作超時或性能問題。 您可以顯式指定塊大小,同時確保塊SizeInMB_50000足夠大以存儲數據,否則複製活動運行將失敗。 | 否 |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到數據存儲時,才指定。 | 否       |

**範例:**

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

| folderPath | fileName | 遞迴 | 來源資料夾結構與篩選結果(檢索**粗體**檔案)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (空白，使用預設值) | false | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (空白，使用預設值) | true | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**檔案3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**檔案3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

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

將檔案從 Amazon S3/Azure Blob/Azure 資料儲存第 2 代複製到 Azure 資料湖儲存 Gen2/Azure Blob 時,可以選擇保留檔中數據和數據。 從[保留元數據](copy-activity-preserve-metadata.md#preserve-metadata)中瞭解更多資訊。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在映射資料流中轉換數據時,可以從 Azure Blob 儲存中讀取和寫入 JSON、Avro、分隔文本或鑲木地板格式的檔。 有關詳細資訊,請參閱映射資料串流功能中的[來源轉換](data-flow-source.md)與[接收器轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>來源轉換

在源轉換中,可以從 Azure Blob 儲存中的容器、資料夾或單個檔讀取。 「**來源」選項**卡允許您管理檔案讀取的方式。 

![來源選項](media/data-flow/sourceOptions1.png "來源選項")

**呼接符路徑:** 使用通配符模式將指示 ADF 在單一個源轉換中循環存取每個符合的資料夾和檔案。 這是處理單個流中的多個檔的有效方法。 使用將滑鼠懸停在現有通配符模式上時出現的 + 符號添加多個通配符匹配模式。

從源容器中選擇一系列與模式匹配的檔。 只能在數據集中指定容器。 因此,通配符路徑還必須包括根資料夾中的資料夾路徑。

萬用者表示:

* ```*```表示任何字元
* ```**```顯示遞迴目錄嵌套
* ```?```取代一個字元
* ```[]```符合括號中更多字元之一

* ```/data/sales/**/*.csv```取得 /資料/銷售下所有 csv 檔案
* ```/data/sales/20??/**/```取得 20 世紀的所有檔案
* ```/data/sales/*/*/*.csv```取得 csv 檔案下 /資料/銷售兩個等級
* ```/data/sales/2004/*/12/[XY]1?.csv```在 2004 年 12 月取得所有 csv 檔,以 X 或 Y 開頭,以兩位數字開頭

**分割區根路徑:** 如果檔案來源中的資料夾已採用```key=value```格式(例如,year_2019)進行分區,則可以將該分區資料夾樹的頂層分配給資料串流資料串中的列名稱。

首先,設定通配符以包括分區資料夾的所有路徑以及您希望讀取的葉檔。

![分割區來源設定](media/data-flow/partfile2.png "分割區檔設定")

使用分區根路徑設置定義資料夾結構的頂層。 您可以從您從資料預覽資料內容時,您將看到 ADF 將新增在每個資料夾等級中找到的已解析分區。

![區塊路徑](media/data-flow/partfile1.png "分割區根路徑預覽")

**檔案清單:** 這是一個檔集。 建立包含要處理的相對路徑檔的清單的文本檔。 指向此文字檔。

**要儲存檔案名稱的欄位:** 將源檔案的名稱儲存在資料中的欄中。 在此輸入新欄位以儲存檔案的名稱。

**完成後:** 選擇在資料流程執行後對源檔不執行任何操作、刪除源檔案或移動源檔。 移動的路徑是相對的。

要將源檔移到處理後的另一個位置,首先選擇檔操作的"移動"。 然後,設置"from"目錄。 如果您沒有為路徑使用任何通配符,則"from"設置將與源資料夾位於同一資料夾。

如果您有具有通配符的來源路徑,則語法如下所示:

```/data/sales/20??/**/*.csv```

您可以將「從「指定」

```/data/sales```

和"到"作為

```/backup/priorSales```

在這種情況下,在 /data/sales 下獲取的所有檔案將移動到 /備份/優先銷售。

> [!NOTE]
> 僅當從管道運行(管道調試或執行運行)啟動數據流時,檔操作才會運行,該流使用管道中的執行數據流活動。 檔操作*不在*資料流調試模式下運行。

**按上次修改進行篩選:** 您可以通過指定上次修改檔的日期範圍來篩選處理的檔。 所有日期時間均以 UTC 表示。 

### <a name="sink-properties"></a>接收器屬性

在接收器轉換中,可以寫入 Azure Blob 儲存中的容器或資料夾。 使用**設定設定選項**卡,您可以管理檔案的寫入方式。

![接收器選項](media/data-flow/file-sink-settings.png "接收器選項")

**清除資料夾:** 確定在寫入資料之前是否清除目標資料夾。

**檔案名選項:** 確定目標檔在目標資料夾中的命名方式。 檔案名選項為:
   * **預設值**:允許 Spark 根據 PART 預設值命名檔案。
   * **模式**:輸入一種模式,以枚舉每個分區的輸出檔。 例如,**貸款\n\csv**將創建貸款1.csv,貸款2.csv,等等。
   * **每個分區**:每個分區輸入一個檔名。
   * **作為列中的數據**:將輸出檔設置為列的值。 路徑相對於資料集容器,而不是目標資料夾。 如果數據集中包含資料夾路徑,則該路徑將被覆蓋。
   * **輸出到單個檔**:將分區的輸出檔合併到單個命名檔案中。 路徑與數據集文件相關。 請注意,te 合併操作可能會根據節點大小失敗。 不建議對大型數據集使用此選項。

**報價所有:** 確定是否將報價中的所有值括起來

## <a name="lookup-activity-properties"></a>尋找活動屬性

要瞭解有關屬性的詳細資訊,請檢查[。](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>取得中繼資料活動屬性

要瞭解有關屬性的詳細資訊,請選擇[中取得中繼資料活動](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>移除活動屬性

要瞭解有關屬性的詳細資訊,請選擇[「刪除活動](delete-activity.md)」

## <a name="legacy-models"></a>舊模型

>[!NOTE]
>以下型號仍支援以下型號,以便進行向後相容性。 建議您今後使用上述各節中提及的新模型,並且 ADF 創作 UI 已切換到生成新模型。

### <a name="legacy-dataset-model"></a>舊資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為**AzureBlob**。 |是 |
| folderPath | Blob 儲存體中容器和資料夾的路徑。 <br/><br/>針對不包含容器名稱的路徑，支援萬用字元篩選。 允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br/><br/>範例：myblobcontainer/myblobfolder/，如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |[是] 適用於複製/查閱活動，[否] 適用於 GetMetadata 活動 |
| fileName | 在指定 "folderPath" 下之 Blob 的**名稱或萬用字元篩選**。 如果沒有為此屬性指定值，資料集就會指向資料夾中的所有 Blob。 <br/><br/>針對篩選，允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際檔案名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。<br/><br/>當未為輸出資料集指定檔名並在活動接收器中未指定**保留層次結構**時,複製活動將自動生成具有以下模式的 blob 名稱:"*資料"活動運行 ID GUID]。[GUID 如果扁平層次結構]。[格式(如果已配置)。"[壓縮如果配置]",* 例如"數據.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz";如果使用表名而不是查詢從表格源進行複製,則名稱模式為 *"[表名]"。格式*。[如果配置壓縮]",* 例如"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 基於屬性的檔篩選器:上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意,當您希望從大量檔執行檔篩選器時,啟用此設置會影響數據移動的整體性能。 <br/><br/> 屬性可以是 NULL,這意味著不會將任何文件屬性篩選器應用於數據集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 基於屬性的檔篩選器:上次修改。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意,當您希望從大量檔執行檔篩選器時,啟用此設置會影響數據移動的整體性能。 <br/><br/> 屬性可以是 NULL,這意味著不會將任何文件屬性篩選器應用於數據集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要以特定格式來剖析或產生檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format]**** 下的 [type]**** 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)小節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip****、Deflate****、BZip2**** 及 ZipDeflate****。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有 Blob，只要指定 **folderPath**。<br>若要複製指定名稱的單一 Blob，以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的 Blob 子集，以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。 

**範例:**

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

### <a name="legacy-copy-activity-source-model"></a>舊複製活動來源模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須複製活動來源的類型屬性設定為**BlobSource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。<br/>允許的值**為 true(** 預設值) 與**false**。 | 否 |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到數據存儲時,才指定。 | 否 |

**範例:**

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

### <a name="legacy-copy-activity-sink-model"></a>舊複製活動接收器模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設定為**BlobSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- 保留層次結構(預設):</b>保留目標資料夾中的檔層次結構。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- 合併檔案</b>:將源資料夾中的所有檔案合併到一個檔案。 如果有指定檔案或 Blob 名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到數據存儲時,才指定。 | 否 |

**範例:**

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
