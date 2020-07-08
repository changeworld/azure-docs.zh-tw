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
ms.date: 06/12/2020
ms.openlocfilehash: 2edf2b61f46e3638af3c2291932a6ab1c1cf23e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85100900"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>使用 Azure Data Factory 在 Azure Blob 儲存體中複製和轉換資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-azure-blob-connector.md)
> * [目前的版本](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料從 Azure Blob 儲存體複製到其中。 它也會說明如何使用資料流程活動來轉換 Azure Blob 儲存體中的資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

>[!TIP]
>若要瞭解 data lake 或資料倉儲的遷移案例，請參閱[使用 Azure Data Factory 將資料從您的 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure Blob 儲存體連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

針對複製活動，此 Blob 儲存體連接器支援：

- 將 Blob 複製到一般用途 Azure 儲存體帳戶和經常性存取/非經常性存取 Blob 儲存體，或從這兩處複製 Blob。 
- 使用帳戶金鑰、服務共用存取簽章（SAS）、服務主體或 Azure 資源驗證的受控識別來複製 blob。
- 從區塊、附加或分頁 Blob 複製 Blob，以及將資料只複製到區塊 Blob。
- 複製 blob 的方式為，或使用[支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生 blob。
- [複製期間保留檔案中繼資料](#preserving-metadata-during-copy)。

>[!IMPORTANT]
>如果您在 Azure 儲存體防火牆設定中啟用 [**允許受信任的 Microsoft 服務存取此儲存體帳戶**] 選項，而且想要使用 Azure 整合執行時間來連線到 Blob 儲存體，則必須使用[受控識別驗證](#managed-identity)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Blob 儲存體專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

此 Blob 儲存體連接器支援下列驗證類型。 如需詳細資訊，請參閱對應的章節。

- [帳戶金鑰驗證](#account-key-authentication)
- [共用存取簽章驗證](#shared-access-signature-authentication)
- [服務主體驗證](#service-principal-authentication)
- [適用于 Azure 資源驗證的受控識別](#managed-identity)

>[!NOTE]
>當您使用 PolyBase 將資料載入 Azure SQL 資料倉儲時，如果您的來源或暫存 Blob 儲存體已設定 Azure 虛擬網路端點，則必須使用 PolyBase 所需的受控識別驗證。 您也必須使用具有3.18 或更新版本的自我裝載整合執行時間。 如需更多設定必要條件，請參閱[受控識別驗證](#managed-identity)一節。

>[!NOTE]
>Azure HDInsight 和 Azure Machine Learning 活動僅支援使用 Azure Blob 儲存體帳戶金鑰的驗證。

### <a name="account-key-authentication"></a>帳戶金鑰驗證

針對儲存體帳戶金鑰驗證，Data Factory 支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設定為**AzureBlobStorage** （建議）或**AzureStorage** （請參閱下列附注）。 |Yes |
| connectionString | 針對**connectionString**屬性指定連接到儲存體所需的資訊。 <br/> 您也可以將帳戶金鑰放在 Azure Key Vault 中，並 `accountKey` 從連接字串中提取設定。 如需詳細資訊，請參閱下列範例和[Azure Key Vault 文章中的 Store 認證](store-credentials-in-key-vault.md)。 |Yes |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure integration runtime 或自我裝載整合執行時間（如果您的資料存放區位於私人網路中）。 如果未指定此屬性，服務就會使用預設的 Azure integration runtime。 |No |

>[!NOTE]
>當您使用帳戶金鑰驗證時，不支援次要 Blob 服務端點。 您可以使用其他驗證類型。

>[!NOTE]
>如果您使用 "AzureStorage" 類型連結服務，它仍會受到支援。 但建議您使用新的「AzureBlobStorage」連結服務類型。

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

共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以使用共用存取簽章來將儲存體帳戶中物件的有限權限授與用戶端，讓該用戶端可以在一段指定時間內使用。 

您不需要共用您的帳戶存取金鑰。 共用存取簽章是一種 URI，此 URI 會在其查詢參數中包含對儲存體資源進行驗證式存取所需的一切資訊。 若要使用共用存取簽章存取儲存體資源，用戶端只需在適當的建構函式或方法中傳入共用存取簽章即可。 

如需有關共用存取簽章的詳細資訊，請參閱[共用存取簽章：了解共用存取簽章模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
>- Data Factory 現已支援*服務共用存取簽章*和*帳戶共用存取簽章*。 如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章授與對 Azure 儲存體資源的有限存取權](../storage/common/storage-sas-overview.md)。
>- 在較新的資料集設定中，資料夾路徑是從容器層級開始的絕對路徑。 您需要設定一個對應您 SAS URI 中路徑的。

Data Factory 支援使用共用存取簽章驗證的下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設定為**AzureBlobStorage** （建議）或**AzureStorage** （請參閱下列附注）。 |是 |
| sasUri | 指定儲存體資源（例如 blob 或容器）的共用存取簽章 URI。 <br/>將此欄位標記為**SecureString** ，將它安全地儲存在 Data Factory 中。 您也可以將 SAS 權杖放在 Azure Key Vault 中，以使用自動旋轉並移除權杖部分。 如需詳細資訊，請參閱下列範例，並[將認證儲存在 Azure Key Vault 中](store-credentials-in-key-vault.md)。 |Yes |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure integration runtime 或自我裝載整合執行時間（如果您的資料存放區位於私人網路中）。 如果未指定此屬性，服務就會使用預設的 Azure integration runtime。 |No |

>[!NOTE]
>如果您使用 "AzureStorage" 類型連結服務，它仍會受到支援。 但建議您使用新的「AzureBlobStorage」連結服務類型。

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
- URI 應根據需要在正確的容器或 blob 上建立。 Blob 的共用存取簽章 URI 可讓 Data Factory 存取該特定 Blob。 Blob 儲存體容器的共用存取簽章 URI 可讓 Data Factory 逐一查看該容器中 Blob。 若要在稍後提供更多或較少物件的存取權，或更新共用存取簽章 URI，請記得使用新的 URI 更新連結的服務。

### <a name="service-principal-authentication"></a>服務主體驗證

如需 Azure 儲存體服務主體驗證的一般資訊，請參閱[使用 Azure Active Directory 驗證 Azure 儲存體的存取權](../storage/common/storage-auth-aad.md)。

若要使用服務主體驗證，請遵循下列步驟：

1. 遵循[使用 Azure AD 租用戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，以在 Azure Active Directory (Azure AD) 中註冊應用程式實體。 請記下這些值，以用來定義連結的服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 在 Azure Blob 儲存體中授與服務主體適當權限。 如需有關角色的詳細資訊，請參閱[使用 RBAC 來管理 Azure 儲存體資料的存取權限](../storage/common/storage-auth-aad-rbac.md)。

    - **作為來源**，在 [**存取控制（IAM）**] 中，至少授與 [**儲存體 Blob 資料讀取**者] 角色。
    - **作為接收**，在 [**存取控制（IAM）**] 中，至少授與 [**儲存體 Blob 資料參與者**] 角色。

以下是支援 Azure Blob 儲存體連結服務的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設定為**AzureBlobStorage**。 |Yes |
| serviceEndpoint | 指定模式為 `https://<accountName>.blob.core.windows.net/` 的 Azure Blob 儲存體服務端點。 |Yes |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | Yes |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為**SecureString** ，將它安全地儲存在 Data Factory 中，或[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 | Yes |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站的右上角來抓取。 | Yes |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure integration runtime 或自我裝載整合執行時間（如果您的資料存放區位於私人網路中）。 如果未指定此屬性，服務就會使用預設的 Azure integration runtime。 |No |

>[!NOTE]
>服務主體驗證僅由 "AzureBlobStorage" 類型連結服務支援，而非先前的 "AzureStorage" 類型連結服務。

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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a>適用于 Azure 資源驗證的受控識別

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接將此受控識別用於 Blob 儲存體驗證，這類似于使用您自己的服務主體。 它可讓這個指定的處理站存取 Blob 儲存體或從中複製資料。

如需 Azure 儲存體驗證的一般資訊，請參閱[使用 Azure Active Directory 驗證 Azure 儲存體的存取權](../storage/common/storage-auth-aad.md)。 若要使用 Azure 資源驗證的受控識別，請遵循下列步驟：

1. 藉由複製與您的處理站一起產生的受控識別物件識別碼值，來抓取[Data Factory 受控識別資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 授與 Azure Blob 儲存體中的受控識別許可權。 如需有關角色的詳細資訊，請參閱[使用 RBAC 來管理 Azure 儲存體資料的存取權限](../storage/common/storage-auth-aad-rbac.md)。

    - **作為來源**，在 [**存取控制（IAM）**] 中，至少授與 [**儲存體 Blob 資料讀取**者] 角色。
    - **作為接收**，在 [**存取控制（IAM）**] 中，至少授與 [**儲存體 Blob 資料參與者**] 角色。

>[!IMPORTANT]
>如果您使用 PolyBase 將資料從 Blob 儲存體（做為來源或暫存）載入 SQL 資料倉儲中，當您使用 Blob 儲存體的受控識別驗證時，請確定您也遵循[本指南](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步驟1和2。 這些步驟會向 Azure AD 註冊您的伺服器，並將儲存體 Blob 資料參與者角色指派給您的伺服器。 Data Factory 會處理其餘部分。 如果您已使用 Azure 虛擬網路端點設定 Blob 儲存體，若要使用 PolyBase 來載入資料，您必須使用 PolyBase 所需的受控識別驗證。

以下是支援 Azure Blob 儲存體連結服務的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設定為**AzureBlobStorage**。 |Yes |
| serviceEndpoint | 指定模式為 `https://<accountName>.blob.core.windows.net/` 的 Azure Blob 儲存體服務端點。 |Yes |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure integration runtime 或自我裝載整合執行時間（如果您的資料存放區位於私人網路中）。 如果未指定此屬性，服務就會使用預設的 Azure integration runtime。 |No |

> [!NOTE]
> 僅由 "AzureBlobStorage" 類型連結服務支援 Azure 資源驗證的受控識別，而非先前的 "AzureStorage" 類型連結服務。

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

下列屬性支援以格式為基礎之資料集的 [設定] 下的 Azure Blob 儲存體 `location` ：

| 屬性   | 說明                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集中位置的**type**屬性必須設定為**AzureBlobStorageLocation**。 | 是      |
| 容器  | Blob 容器。                                          | 是      |
| folderPath | 指定容器下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源設定] 中指定。 | No       |
| fileName   | 指定容器和資料夾路徑下的檔案名。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在 [活動來源設定] 中指定。 | 否       |

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

在 `storeSettings` 以格式為基礎的複製來源的 [設定] 下，Azure Blob 儲存體支援下列屬性：

| 屬性                 | 說明                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 底下的**type**屬性 `storeSettings` 必須設定為**AzureBlobStorageReadSettings**。 | Yes                                           |
| 尋找要複製的檔案： |  |  |
| 選項 1：靜態路徑<br> | 從資料集內指定的特定容器或資料夾/檔案路徑複製。 如果您想要從容器或資料夾複製所有 blob，請另外將指定 `wildcardFileName` 為 `*` 。 |  |
| 選項2：blob 前置詞<br>- 前置詞 | 在資料集內設定的指定容器下，blob 名稱的前置詞，用來篩選來源 blob。 系統會選取其名稱開頭為的 blob `container_in_dataset/this_prefix` 。 它會利用 Blob 儲存體的服務端篩選，提供比萬用字元篩選更好的效能。 | No                                                          |
| 選項 3：萬用字元<br>- wildcardFolderPath | 在資料集內設定的指定容器下，具有萬用字元的資料夾路徑，用來篩選來源資料夾。 <br>允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 `^`如果您的資料夾名稱包含萬用字元或內的此 escape 字元，請使用來進行 escape。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | No                                            |
| 選項 3：萬用字元<br>- wildcardFileName | 在指定容器和資料夾路徑（或萬用字元資料夾路徑）下具有萬用字元的檔案名，用以篩選來源檔案。 <br>允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 `^`如果您的資料夾名稱包含萬用字元或內的此 escape 字元，請使用來進行 escape。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 4：檔案清單<br>- fileListPath | 表示要複製指定的檔案集。 指向包含您要複製之檔案清單的文字檔，每行一個檔案，這是在資料集中設定之路徑的相對路徑。<br/>當您使用此選項時，請勿指定資料集中的檔案名。 [檔案清單範例](#file-list-examples) (英文) 有更多範例可供參閱。 |No |
| 其他設定： |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當**遞迴**設定為**true**且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。 <br>允許的值為 **true** (預設值) 和 **false**。<br>設定 `fileListPath` 時，此屬性不適用。 |否 |
| deleteFilesAfterCompletion | 指出在成功移至目的地存放區之後，是否會從來源存放區刪除二進位檔案。 檔案刪除是每個檔案，因此當複製活動失敗時，您會看到某些檔案已複製到目的地，並從來源刪除，而其他檔案仍在來源存放區上剩餘。 <br/>此屬性只在二進位複製案例中有效，其中資料來源存放區為 Blob、ADLS Gen1、ADLS Gen2、S3、Google Cloud Storage、File、Azure 檔案、SFTP 或 FTP。 預設值： false。 |No |
| modifiedDatetimeStart    | 檔案會根據屬性進行篩選：上次修改。 <br>若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br> 屬性可以是**Null**，這表示不會將檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值 `modifiedDatetimeEnd` ，但為**Null**時，將會選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值 `modifiedDatetimeStart` ，但為**Null**時，將會選取上次修改屬性小於日期時間值的檔案。<br/>設定 `fileListPath` 時，不適用此屬性。 | No                                            |
| modifiedDatetimeEnd      | 同上。                                               | No                                            |
| maxConcurrentConnections | 儲存體的並行連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | No                                            |

> [!NOTE]
> 針對 Parquet/分隔文字格式，下一節中所述之複製活動來源的**BlobSource**類型仍然受支援，因為這是為了回溯相容性。 我們建議您使用新的模型，直到 Data Factory 撰寫 UI 已切換為產生這些新類型為止。

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

下列屬性在以 `storeSettings` 格式為基礎的複製接收中的 [設定] 下支援 Azure Blob 儲存體：

| 屬性                 | 說明                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 底下的**type**屬性 `storeSettings` 必須設定為**AzureBlobStorageWriteSettings**。 | Yes      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 如果有指定檔案或 Blob 名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| blockSizeInMB | 指定用來將資料寫入區塊 blob 的區塊大小（以 mb 為單位）。 請參閱[關於區塊 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 以深入了解。 <br/>允許的值*介於 4 mb 到 100 mb 之間*。 <br/>根據預設，Data Factory 會依據您的來源存放區類型和資料，自動決定區塊大小。 針對非二進位複製到 Blob 儲存體，預設區塊大小為 100 MB，因此可納入（最多） 4.95 TB 的資料。 當您的資料不大時，特別是當您使用自我裝載整合執行時間的網路連線不佳，因而導致作業超時或效能問題時，這可能不是最佳的作法。 您可以明確指定區塊大小，同時確保 `blockSizeInMB*50000` 夠大，足以儲存資料。 否則，複製活動執行將會失敗。 | No |
| maxConcurrentConnections | 儲存體的並行連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | No       |

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

本節說明在複製活動來源中使用檔案清單路徑所產生的行為。

假設您有下列源資料夾結構，而且想要以粗體複製檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | Data Factory 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| 容器<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;中繼資料<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集內：**<br>- 容器：`container`<br>- 資料夾路徑：`FolderA`<br><br>**在複製活動來源中：**<br>- 檔案清單路徑：`container/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向相同資料存放區中的文字檔，其中包含您想要複製的檔案清單（每行一個檔案），其中包含資料集中所設定路徑的相對路徑。 |

### <a name="some-recursive-and-copybehavior-examples"></a>一些遞迴和 copyBehavior 範例

本節說明**遞迴**和**copyBehavior**值的不同組合之複製作業所產生的行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用與來源相同的結構，建立目的檔案夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會使用下列結構建立目的檔案夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會使用下列結構建立目的檔案夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會使用下列結構建立目的檔案夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會使用下列結構建立目的檔案夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會使用下列結構建立目的檔案夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |

## <a name="preserving-metadata-during-copy"></a>複製期間保留中繼資料

當您將檔案從 Amazon S3、Azure Blob 儲存體或 Azure Data Lake Storage Gen2 複製到 Azure Data Lake Storage Gen2 或 Azure Blob 儲存體時，您可以選擇保留檔案中繼資料和資料。 深入了解[保留中繼資料](copy-activity-preserve-metadata.md#preserve-metadata)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

當您要轉換對應資料流程中的資料時，您可以下列格式從 Azure Blob 儲存體讀取和寫入檔案：
* [JSON](format-json.md#mapping-data-flow-properties)
* [Avro](format-avro.md#mapping-data-flow-properties)
* [分隔符號文字](format-delimited-text.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)。

格式特定設定位於該格式的檔中。 如需詳細資訊，請參閱對應資料流程中的對應資料流程和[接收轉換](data-flow-sink.md)[中的來源轉換](data-flow-source.md)。

### <a name="source-transformation"></a>來源轉換

在 [來源轉換] 中，您可以從 Azure Blob 儲存體中的容器、資料夾或個別檔案進行讀取。 使用 [**來源選項**] 索引標籤來管理檔案的讀取方式。 

![來源選項](media/data-flow/sourceOptions1.png "來源選項")

**萬用字元路徑：** 使用萬用字元模式會指示 Data Factory 在單一來源轉換中，迴圈處理每個相符的資料夾和檔案。 這在單一流程內處理多個檔案時很有效。 在您將滑鼠停留在現有的萬用字元模式上時，使用顯示的加號新增多個萬用字元比對模式。

從來源容器中，選擇符合模式的一系列檔案。 只有容器可以在資料集內指定。 因此，萬用字元路徑也必須包含始於根資料夾的資料夾路徑。

萬用字元範例：

* ```*```表示任何一組字元。
* ```**```表示遞迴目錄的嵌套。
* ```?```取代一個字元。
* ```[]```符合括弧中的一或多個字元。

* ```/data/sales/**/*.csv```取得/data/sales. 下的所有 .csv 檔案
* ```/data/sales/20??/**/```取得20世紀的所有檔案。
* ```/data/sales/*/*/*.csv```取得 .csv 檔案/data/sales. 下的兩個層級
* ```/data/sales/2004/*/12/[XY]1?.csv```取得2004年12月的所有 .csv 檔案，從 X 或 Y 前面加上兩位數的數位。

資料**分割根路徑：** 如果您的檔案來源中有使用格式的資料分割資料夾 ```key=value``` （例如 `year=2019` ），則您可以將該分割區資料夾樹狀結構的最上層指派給資料流程資料流程中的資料行名稱。

首先，設定萬用字元以包含所有分割資料夾的路徑，加上您想要讀取的分葉檔案。

![分割區來源檔案設定](media/data-flow/partfile2.png "分割區檔案設定")

使用 [資料**分割根路徑**] 設定來定義資料夾結構的最上層。 當您透過資料預覽來查看資料的內容時，您會看到 Data Factory 會新增在您的每個資料夾層級中找到的已解析分割區。

![分割區根路徑](media/data-flow/partfile1.png "分割區根路徑預覽")

**檔案清單：** 這是檔案集。 建立文字檔，其中包含要處理的相對路徑檔案清單。 指向這個文字檔。

**要儲存檔案名稱的資料行：** 將來源檔案的名稱儲存在資料中的資料行。 在此輸入新的資料行名稱以儲存檔案名稱字串。

**完成後：** 選擇在資料流程後不操作來源檔案、刪除來源檔案、或移動來源檔案。 移動時的路徑是相對路徑。

若要在處理後將來源檔案移到另一個位置，請先選取 [移動] 當作檔案操作。 然後，設定「來源」目錄。 如果路徑中未使用任何萬用字元，則「來源」設定與來源資料夾相同。

如果您有具有萬用字元的來源路徑，您的語法看起來會像這樣：

```/data/sales/20??/**/*.csv```

您可以指定 "from" 作為：

```/data/sales```

而且您可以將 "to" 指定為：

```/backup/priorSales```

在此案例中，/data/sales 中的所有檔案都會移至 /backup/priorSales。

> [!NOTE]
> 只有當您從管線執行 (管線偵錯或執行) 啟動資料流程，且該管線執行在管線中使用「執行資料流程」活動時，檔案操作才會執行。 在資料流程偵錯模式下「不會」執行檔案操作。

**依上次修改時間篩選：** 您可以指定檔案上次修改的日期範圍，以篩選您處理的檔案。 所有的日期時間都是 UTC 格式。 

### <a name="sink-properties"></a>接收屬性

在「接收」轉換中，您可以寫入至 Azure Blob 儲存體中的容器或資料夾。 使用 [**設定**] 索引標籤來管理檔案的寫入方式。

![接收選項](media/data-flow/file-sink-settings.png "接收選項")

**清除資料夾：** 決定是否在寫入資料之前先清除目的地資料夾。

**檔案名稱選項：** 決定目的地檔案在目的地資料夾中的命名方式。 檔案名稱的選項如下：
   * **預設值**：可讓 Spark 根據 PART 預設值來命名檔案。
   * **模式**：輸入模式來列舉每個分割區的輸出檔案。 例如，**loans[n].csv** 會建立 loans1.csv、loans2.csv，依此類推。
   * **每個分割區**：針對每個分割區輸入一個檔案名稱。
   * **作為資料行中的資料**：將輸出檔案設定為資料行的值。 路徑相對於資料集容器，而不是目的地資料夾。 如果資料集含有資料夾路徑，則會被覆寫。
   * **輸出至單一檔案**：將分割的輸出檔案結合成單一的具名檔案。 路徑相對於資料集資料夾。 請注意，合併作業可能會根據節點大小而失敗。 對於大型資料集，我們不建議採用此選項。

**全部報價：** 決定是否將所有值括在引號中。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要瞭解屬性的詳細資料，請檢查[GetMetadata 活動](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要瞭解有關屬性的詳細資料，請核取 [[刪除活動](delete-activity.md)]。

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>下列模型仍受到支援，因為這是為了回溯相容性。 我們建議您使用先前所述的新模型。 Data Factory 撰寫 UI 已切換為產生新的模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的**類型**屬性必須設定為**AzureBlob**。 |Yes |
| folderPath | Blob 儲存體中容器和資料夾的路徑。 <br/><br/>路徑支援萬用字元篩選，但不包括容器名稱。 允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 `^`如果您的資料夾名稱包含萬用字元或內的此 escape 字元，請使用來進行 escape。 <br/><br/>例如： myblobcontainer/myblobfolder/。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |針對複製或查閱活動為 [是]，[GetMetadata] 活動為 [否] |
| fileName | 在指定的**folderPath**值下，blob 的名稱或萬用字元篩選準則。 如果沒有為此屬性指定值，資料集就會指向資料夾中的所有 Blob。 <br/><br/>對於篩選，允許的萬用字元為： `*` （比對零或多個字元）和 `?` （符合零或單一字元）。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>`^`如果您的檔案名具有萬用字元或內的此 escape 字元，請使用來進行 escape。<br/><br/>當未指定輸出資料集的**fileName** ，且在活動接收中未指定**PreserveHierarchy**時，複製活動會自動以下列模式產生 Blob 名稱： "*Data. [活動執行識別碼 GUID]。[如果 FlattenHierarchy，則為 GUID]。[格式化（若已設定）]。[已設定壓縮]]*。 例如： "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz"。 <br/><br/>如果您使用資料表名稱（而非查詢）從表格式來源複製，則名稱模式會是 "*[資料表名稱]. [格式]。[已設定壓縮]]*。 例如：「MyTable.csv」。 |No |
| modifiedDatetimeStart | 檔案會根據屬性進行篩選：上次修改。 若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要篩選大量檔案時，啟用這項設定將會影響資料移動的整體效能。 <br/><br/> 屬性可以是**Null**，這表示不會將檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值 `modifiedDatetimeEnd` ，但為**Null**時，將會選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值 `modifiedDatetimeStart` ，但為**Null**時，將會選取上次修改屬性小於日期時間值的檔案。| No |
| modifiedDatetimeEnd | 檔案會根據屬性進行篩選：上次修改。 若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要篩選大量檔案時，啟用這項設定將會影響資料移動的整體效能。 <br/><br/> 屬性可以是**Null**，這表示不會將檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值 `modifiedDatetimeEnd` ，但為**Null**時，將會選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值 `modifiedDatetimeStart` ，但為**Null**時，將會選取上次修改屬性小於日期時間值的檔案。| No |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format] 下的 [type] 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)小節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有 Blob，只要指定 **folderPath**。<br>若要複製指定名稱的單一 blob，請針對資料夾部分指定**folderPath** ，並針對檔案名指定**fileName** 。<br>若要複製資料夾下的 blob 子集，請為資料夾部分指定**folderPath** ，並針對萬用字元篩選器指定**fileName** 。 

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

### <a name="legacy-source-model-for-the-copy-activity"></a>複製活動的舊版來源模型

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的**類型**屬性必須設定為**BlobSource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當**遞迴**設定為**true**且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。<br/>允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 儲存體的並行連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | No |

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

### <a name="legacy-sink-model-for-the-copy-activity"></a>複製活動的舊版接收模型

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的**type**屬性必須設定為**BlobSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 如果有指定檔案或 Blob 名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 儲存體的並行連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | No |

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

如需 Data Factory 中的複製活動支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
