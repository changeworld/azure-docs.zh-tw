---
title: 使用 Azure Data Factory 從 OData 來源複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 OData 來源複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 17b78e03e330e342e9d558dd3ca5d9071bcd3c2f
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163925"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 OData 來源複製資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-odata-connector.md)
> * [目前的版本](connector-odata.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 OData 來源複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 OData 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 OData 來源複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具體而言，這個 OData 連接器支援：

- OData 3.0 和 4.0 版。
- 使用下列其中一種驗證來複製資料：**匿名**、**基本**、 **Windows**和**AAD 服務主體**。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 OData 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 OData 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設為 **OData**。 |是 |
| url | OData 服務的根 URL。 |是 |
| authenticationType | 用來連線到 OData 來源的驗證類型。 允許的值為**Anonymous**、 **Basic**、 **Windows**和**AadServicePrincipal**。 不支援以使用者為基礎的 OAuth。 | 是 |
| userName | 如果使用基本或 Windows 驗證，請指定 **userName**。 | 否 |
| 密碼 | 針對您指定 **userName** 的使用者帳戶指定 **password**。 將此欄位標記為 **SecureString** 類型，將它安全地儲存在 Data Factory 中。 您也可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalId | 指定 Azure Active Directory 應用程式的用戶端識別碼。 | 否 |
| aadServicePrincipalCredentialType | 指定要用於服務主體驗證的認證類型。 允許的值包為：`ServicePrincipalKey` 或 `ServicePrincipalCert`。 | 否 |
| servicePrincipalKey | 指定 Azure Active Directory 應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalEmbeddedCert | 指定在 Azure Active Directory 中註冊之您應用程式的 base64 編碼憑證。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalEmbeddedCertPassword | 如果您的憑證受到密碼保護，則指定您憑證的密碼。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。  | 否|
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 否 |
| aadResourceId | 指定您要求授權的 AAD 資源。| 否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 深入瞭解[必要條件](#prerequisites)一節。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

**範例 1：使用匿名驗證**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例 2：使用基本驗證**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**範例 3：使用 Windows 驗證**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**範例4：使用服務主體金鑰驗證**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**範例5：使用服務主體憑證驗證**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

本節提供 OData 資料集所支援的屬性清單。

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 

若要從 OData 複製資料，請將資料集的 **type** 屬性設定為 **ODataResource**。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設定為 **ODataResource**。 | 是 |
| path | OData 資源的路徑。 | 是 |

**範例**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 OData 來源所支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="odata-as-source"></a>OData 作為來源

若要從 OData 複製資料，複製活動的 [**來源**] 區段中支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的**類型**屬性必須設定為**ODataSource**。 | 是 |
| 查詢 | 用來篩選資料的 OData 查詢選項。 範例： `"$select=Name,Description&$top=5"`.<br/><br/>**注意**：OData 連接器會從以下的組合 URL 複製資料：`[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`。 如需詳細資訊，請參閱 [OData URL 元件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |

**範例**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果您使用 `RelationalSource` 具類型的來源，則仍會受到支援，但建議您在未來使用新的來源。

## <a name="data-type-mapping-for-odata"></a>OData 的資料類型對應

從 OData 複製資料時，在 OData 資料類型與 Azure Data Factory 過渡期資料類型之間會使用下列對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收端，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| OData 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> 不支援 OData 複雜資料類型 (例如**物件**)。


## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。