---
title: 將資料複製到 Azure 資料資源管理員或從 Azure 資料資源管理員複製
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
ms.openlocfilehash: 5f0eada5184f2c8e36b0deb5a9d27a8a9230776d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382754"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>使用 Azure 資料工廠將資料複製到或從 Azure 資料資源管理員複製資料

本文介紹如何使用 Azure 資料工廠中的複製活動將資料複製到 Azure 資料資源管理員或從[Azure 資料資源管理員複製](/azure/data-explorer/data-explorer-overview)。 它基於[複製活動概述](copy-activity-overview.md)文章,其中提供了複製活動的一般概述。

>[!TIP]
>對於 Azure 資料工廠和 Azure 資料資源管理員整合,從[將 Azure 資料資源管理器與 Azure 資料工廠整合](/azure/data-explorer/data-factory-integration)中瞭解詳細資訊。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Azure 資料資源管理員連接器:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [尋找活動](control-flow-lookup-activity.md)

您可以將資料從任何支援的來源資料存放區複製到 Azure 資料總管。 您也可以將資料從 Azure 資料總管複製到任何支援的接收資料存放區。 有關複製活動支援為源或接收體的清單,請參閱[支援資料儲存](copy-activity-overview.md#supported-data-stores-and-formats)表。

>[!NOTE]
>版本3.14及更高版本支援使用自託管整合時將資料複製到或從Azure資料資源管理員中透過本地資料儲存複製資料。

使用 Azure 資料資源管理員連接器,可執行以下操作:

* 使用 Azure Active Directory (Azure AD) 應用程式權杖驗證搭配**服務主體**來複製資料。
* 作為來源時，請使用 KQL (Kusto) 查詢擷取資料。
* 作為接收時，請將資料附加至目的地資料表。

## <a name="getting-started"></a>開始使用

>[!TIP]
>有關 Azure 資料資源管理員連接器的演練,請參閱[使用 Azure 資料工廠將資料從 Azure 資料資源管理員複製資料,](/azure/data-explorer/data-factory-load-data)並將[批次複製資料, 並將批次複製資料到 Azure 資料資源管理員](/azure/data-explorer/data-factory-template)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關屬性的詳細資料，這些屬性會用來定義 Azure 資料總管連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure 資料資源管理器連接器使用服務主體身份驗證。 按照以下步驟獲取服務主體並授予許可權:

1. 按照 Azure [AD 租戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)的步驟,在 Azure 活動目錄中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 在 Azure 數據資源管理器中授予服務主體正確的許可權。 有關角色和許可權以及管理權限的詳細資訊,請參閱[管理 Azure 資料資源管理員資料庫權限](/azure/data-explorer/manage-database-permissions)。 通常,您必須:

    - **作為來源**,至少將**資料庫檢視器**角色授予資料庫
    - **作為接收器**,至少將**資料庫引入者**角色授予資料庫

>[!NOTE]
>使用數據工廠 UI 創作時,登錄使用者帳戶用於列出 Azure 資料資源管理器群集、資料庫和表。 如果您沒有這些操作的許可權,請手動輸入名稱。

Azure 資料資源管理員連結服務支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **類型**屬性必須設定為 Azure**資料資源管理員**。 | 是 |
| 端點 | Azure 資料總管叢集的端點 URL，格式為 `https://<clusterName>.<regionName>.kusto.windows.net`。 | 是 |
| [資料庫] | 資料庫名稱。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為「權威 ID」。。 通過將滑鼠指標懸停在 Azure 門戶的右上角來檢索它。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為「AAD 應用程式客戶端 ID」。。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 這在[Kusto 連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)中稱為「AAD」應用程式金鑰」。 將此欄位標記為**安全字串**,以將其安全地儲存在資料工廠中,或[參考儲存在 Azure 金鑰保存庫中的安全資料](store-credentials-in-key-vault.md)。 | 是 |

**連結服務屬性範例:**

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

有關可用於定義資料集的節和屬性的完整清單,請參閱 Azure[資料工廠 中的資料集](concepts-datasets-linked-services.md)。 本節列出了 Azure 數據資源管理員數據集支援的屬性。

若要將資料複製到 Azure 資料總管，請將資料集的 type 屬性設定為 **AzureDataExplorerTable**。

以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **類型**屬性必須設定為**AzureDataExplorerTable**。 | 是 |
| 資料表 | 連結服務所參考的資料表名稱。 | Yes (接收)：No (來源) |

**資料集屬性範例:**

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

有關可用於定義活動的節和屬性的完整清單,請參閱 Azure[資料工廠中的導管和活動](concepts-pipelines-activities.md)。 本節提供 Azure 資料資源管理員來源和接收器支援的屬性的清單。

### <a name="azure-data-explorer-as-source"></a>作為來源的 Azure 資料總管

若要從 Azure 資料總管複製資料，請將複製活動來源中的 **type** 屬性設定為 **AzureDataExplorerSource**。 複製活動**來源**部份支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須複製活動來源**的類型**屬性設定為 **:AzureDataExplorerSource** | 是 |
| 查詢 | [KQL 格式](/azure/kusto/query/)中指定的唯讀要求。 使用自訂的 KQL 查詢作為參考。 | 是 |
| queryTimeout | 查詢請求超時前的等待時間。默認值為 10 分鐘(00:10:00);允許的最大值為 1 小時(01:00:00)。 | 否 |
| 不流 | 指示是否截截返回的結果集。 默認情況下,結果在 500,000 條記錄或 64 兆位元組 (MB) 後被截斷。 強烈建議進行截斷,以確保活動的正確行為。 |否 |

>[!NOTE]
>默認情況下,Azure 資料資源管理器來源的大小限制為 500,000 條記錄或 64 MB。 若要檢索所有記錄而不進行截斷,可以在查詢的開頭`set notruncation;`指定。 有關詳細資訊,請參閱[查詢限制](https://docs.microsoft.com/azure/kusto/concepts/querylimits)。

**範例：**

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

若要將資料複製到 Azure 資料總管，請將複製活動接收中的 type 屬性設定為 **AzureDataExplorerSink**。 複製活動**接收器**部份支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須複製活動接收器**的類型**屬性設定為 **:AzureDataExplorerSink**。 | 是 |
| ingestionMappingName | 庫斯托表上預先創建的[映射](/azure/kusto/management/mappings#csv-mapping)的名稱。 要將列從源映射到 Azure 資料資源管理員(適用於[所有受支援的來源儲存和格式](copy-activity-overview.md#supported-data-stores-and-formats)(包括 CSV/JSON/Avro 格式),可以使用複製活動[列映射](copy-activity-schema-and-type-mapping.md)(隱式名稱或顯式表示為配置)和/或 Azure 資料資源管理器映射。 | 否 |
| 其他屬性 | 屬性包,可用於指定 Azure 資料資源管理員接收器尚未設置的任何引入屬性。 具體來說,它可用於指定引入標記。 從[Azure 數據流覽數據引入文檔](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html)中瞭解更多資訊。 | 否 |

**範例：**

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

## <a name="lookup-activity-properties"></a>尋找活動屬性

有關屬性的詳細資訊,請參閱[活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

* 有關 Azure 資料工廠複製活動作為來源與接收器支援的資料儲存的清單,請參考[的資料儲存](copy-activity-overview.md#supported-data-stores-and-formats)。

* 詳細瞭解如何[將資料從 Azure 資料工廠複製到 Azure 資料資源管理員](/azure/data-explorer/data-factory-load-data)。
