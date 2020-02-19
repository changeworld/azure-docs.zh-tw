---
title: 從 Azure 資料總管複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料複製到 Azure 資料總管或從該處複製資料。
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 4c265cb0cdc665ef52f4dc6e69440e83c22db449
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77460968"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>使用 Azure Data Factory 在 Azure 資料總管之間複製資料

本文說明如何使用 Azure Data Factory 中的「複製活動」，從[Azure 資料總管](../data-explorer/data-explorer-overview.md)複製資料 它是以[複製活動總覽](copy-activity-overview.md)一文為基礎，提供複製活動的一般總覽。

>[!TIP]
>如需 Azure Data Factory 和 Azure 資料總管整合的一般資訊，請深入瞭解[整合 Azure 資料總管與 Azure Data Factory](../data-explorer/data-factory-integration.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Azure 資料總管連接器支援下列活動：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從任何支援的來源資料存放區複製到 Azure 資料總管。 您也可以將資料從 Azure 資料總管複製到任何支援的接收資料存放區。 如需複製活動支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

>[!NOTE]
>3\.14 和更新版本支援使用自我裝載整合執行時間，透過內部部署資料存放區將資料複製到 Azure 資料總管或從該處複製資料。

使用 Azure 資料總管連接器，您可以執行下列動作：

* 使用 Azure Active Directory (Azure AD) 應用程式權杖驗證搭配**服務主體**來複製資料。
* 作為來源時，請使用 KQL (Kusto) 查詢擷取資料。
* 作為接收時，請將資料附加至目的地資料表。

## <a name="getting-started"></a>開始使用

>[!TIP]
>如需 Azure 資料總管連接器的逐步解說，請參閱[使用 Azure Data Factory 從資料庫將資料複製到 azure 資料總管](../data-explorer/data-factory-load-data.md)，並[從資料庫大量複製到 azure 資料總管](../data-explorer/data-factory-template.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關屬性的詳細資料，這些屬性會用來定義 Azure 資料總管連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure 資料總管連接器會使用服務主體驗證。 請遵循下列步驟來取得服務主體，並授與許可權：

1. 遵循[向 Azure AD 租使用者註冊您的應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)中的步驟，在 Azure Active Directory 中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 在 Azure 資料總管中，將正確的許可權授與服務主體。 如需有關角色和許可權以及管理許可權的詳細資訊，請參閱[管理 Azure 資料總管資料庫許可權](../data-explorer/manage-database-permissions.md)。 一般來說，您必須：

    - **作為來源**，請至少授與資料庫**檢視器**角色給您的資料庫
    - **作為接收器**，請至少將**資料庫擷取器**角色授與您的資料庫

>[!NOTE]
>當您使用 Data Factory UI 來撰寫時，您的登入使用者帳戶會用來列出 Azure 資料總管叢集、資料庫和資料表。 如果您沒有這些作業的許可權，請以手動方式輸入名稱。

以下是針對 Azure 資料總管已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設定為**AzureDataExplorer**。 | 是 |
| 端點 | Azure 資料總管叢集的端點 URL，格式為 `https://<clusterName>.<regionName>.kusto.windows.net`。 | 是 |
| [資料庫] | 資料庫名稱。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為「授權識別碼」。 將滑鼠指標暫留在 Azure 入口網站的右上角，即可取出。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為「AAD 應用程式用戶端識別碼」。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為「AAD 應用程式金鑰」。 將此欄位標記為**SecureString** ，將它安全地儲存在 Data Factory 中，或[參考儲存在 Azure Key Vault 中的安全資料](store-credentials-in-key-vault.md)。 | 是 |

**連結服務屬性範例：**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[Azure Data Factory 中的資料集](concepts-datasets-linked-services.md)。 本節列出 Azure 資料總管資料集所支援的屬性。

若要將資料複製到 Azure 資料總管，請將資料集的 type 屬性設定為 **AzureDataExplorerTable**。

以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設定為**AzureDataExplorerTable**。 | 是 |
| 資料表 | 連結服務所參考的資料表名稱。 | Yes (接收)：No (來源) |

**資料集屬性範例：**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[Azure Data Factory 中的管線和活動](concepts-pipelines-activities.md)。 本節提供 Azure 資料總管來源和接收器所支援的屬性清單。

### <a name="azure-data-explorer-as-source"></a>作為來源的 Azure 資料總管

若要從 Azure 資料總管複製資料，請將複製活動來源中的 **type** 屬性設定為 **AzureDataExplorerSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的**類型**屬性必須設定為： **AzureDataExplorerSource** | 是 |
| 查詢 | [KQL 格式](/azure/kusto/query/)中指定的唯讀要求。 使用自訂的 KQL 查詢作為參考。 | 是 |
| queryTimeout | 查詢要求超時之前的等候時間。預設值為10分鐘（00:10:00）;允許的最大值為1小時（01:00:00）。 | 否 |
| noTruncation | 指出是否截斷傳回的結果集。 根據預設，在500000記錄或 64 mb 之後，會截斷結果。 強烈建議進行截斷，以確保活動的正確行為。 |否 |

>[!NOTE]
>根據預設，Azure 資料總管來源的大小限制為500000筆記錄或 64 MB。 若要取出所有記錄而不截斷，您可以在查詢的開頭指定 `set notruncation;`。 如需詳細資訊，請參閱[查詢限制](https://docs.microsoft.com/azure/kusto/concepts/querylimits)。

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>作為接收的 Azure 資料總管

若要將資料複製到 Azure 資料總管，請將複製活動接收中的 type 屬性設定為 **AzureDataExplorerSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的**類型**屬性必須設定為： **AzureDataExplorerSink**。 | 是 |
| ingestionMappingName | Kusto 資料表上預先建立的[對應](/azure/kusto/management/mappings#csv-mapping)名稱。 若要將資料行從來源對應到 Azure 資料總管（適用于[所有支援的來源存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)，包括 CSV/JSON/Avro 格式），您可以使用複製活動資料[行對應](copy-activity-schema-and-type-mapping.md)（以名稱隱含或明確設定）和/或 Azure 資料總管對應。 | 否 |
| additionalProperties | 屬性包，可以用來指定 Azure 資料總管接收尚未設定的任何內嵌屬性。 具體來說，它可以用來指定內嵌標記。 若要深入瞭解，請[流覽 Azure 資料探索資料](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html)內嵌檔。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>查閱活動屬性

如需屬性的詳細資訊，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

* 如需 Azure Data Factory 中的複製活動支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

* 深入瞭解如何[將資料從 Azure Data Factory 複製到 Azure 資料總管](/azure/data-explorer/data-factory-load-data)。
