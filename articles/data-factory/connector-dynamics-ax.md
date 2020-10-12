---
title: 從 Dynamics AX 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 Dynamics AX 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 23a486dfe1256cea46f6722873950ffcb1bde084
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84982691"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Dynamics AX 複製資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Dynamics AX 來源複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

此 Dynamics AX 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Dynamics AX 複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具體而言，此 Dynamics AX 連接器支援使用 **OData 通訊協定**搭配**服務主體驗證**從 Dynamics AX 複製資料。

>[!TIP]
>您也可以使用此連接器從 **Dynamics 365 Finance and Operations** 複製資料。 請參閱 Dynamics 365 的 [OData 支援](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) \(英文\) 和[驗證方法](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) \(英文\)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 Dynamics AX 連接器專屬的 Data Factory 實體。

## <a name="prerequisites"></a>必要條件

若要使用服務主體驗證，請遵循下列步驟：

1. 遵循[使用 Azure AD 租用戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，以在 Azure Active Directory (Azure AD) 中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 移至 Dynamics AX，然後將適當使用權限授與此服務主體以存取您的 Dynamics AX。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Dynamics AX 已連結服務所支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | [type] 屬性必須設定為 [DynamicsAX]。 |是 |
| url | Dynamics AX (或 Dynamics 365 Finance and Operations) 執行個體 OData 端點。 |是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |
| aadResourceId | 指定您要求授權的 AAD 資源。 例如，如果您的 Dynamics URL 是 `https://sampledynamics.sandbox.operations.dynamics.com/data/`，則對應的 AAD 資源通常會是 `https://sampledynamics.sandbox.operations.dynamics.com`。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以選擇 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

**範例**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>資料集屬性

本節提供 Dynamics AX 資料集所支援的屬性清單。

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 

若要從 Dynamics AX 複製資料，請將資料集的 [type] 屬性設定為 [DynamicsAXResource]。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 [type] 屬性必須設定為 [DynamicsAXResource]。 | 是 |
| path | Dynamics AX OData 實體的路徑。 | 是 |

**範例**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 Dynamics AX 來源所支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="dynamics-ax-as-source"></a>Dynamics AX 作為來源

若要從 Dynamics AX 複製資料，請將複製活動中的 [source] 類型設定為 [DynamicsAXSource]。 複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 [type] 屬性必須設定為 [DynamicsAXSource]。 | 是 |
| 查詢 | 用來篩選資料的 OData 查詢選項。 範例： `"?$select=Name,Description&$top=5"`.<br/><br/>**注意**：連接器會從以下的組合 URL 複製資料：`[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`。 如需詳細資訊，請參閱 [OData URL 元件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |
| httpRequestTimeout | 用來取得回應的 HTTP 要求會有的逾時值 (**TimeSpan** 值)。 此值是取得回應的逾時值，而非讀取回應資料的逾時值。 如果未指定，預設值為 **00:30:00** (30 分鐘) 。 | 否 |

**範例**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
