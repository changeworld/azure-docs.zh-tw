---
title: 使用 Azure Data Factory 從 Sybase 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 Sybase 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: ac9dff4b16d8ba1b346a2827f3b5487dbf97392e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84669829"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>使用 Azure Data Factory 從 Sybase 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-onprem-sybase-connector.md)
> * [目前的版本](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Sybase 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Sybase 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Sybase 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Sybase 連接器支援：

- SAP Sybase SQL Anywhere (ASA) **第16版和更新版本**。
- 使用 **Basic** (基本) 或 **Windows** 驗證來複製資料。

不支援 Sybase IQ 和 ASE。 您可以改用一般 ODBC 連接器搭配 Sybase 驅動程式。

## <a name="prerequisites"></a>Prerequisites

若要使用這個 Sybase 連接器，您必須：

- 設定一個「自我裝載 Integration Runtime」。 如需詳細資訊，請參閱自我裝載的 [Integration Runtime](create-self-hosted-integration-runtime.md) 文章。
- 在 Integration Runtime 電腦上安裝 [Sybase iAnywhere.Data.SQLAnywhere 的資料提供者](https://go.microsoft.com/fwlink/?linkid=324846) 16 版或更新版本。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Sybase 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Sybase 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**Sybase** | 是 |
| 伺服器 | Sybase 伺服器的名稱。 |是 |
| [資料庫] | Sybase 資料庫的名稱。 |是 |
| authenticationType | 用來連接到 Sybase 資料庫的驗證類型。<br/>允許的值為：**Basic** (基本) 和 **Windows**。 |是 |
| username | 指定連線到 Sybase 資料庫時所要使用的使用者名稱。 |是 |
| 密碼 | 指定您為使用者名稱所指定之使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例︰**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
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

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Sybase 資料集所支援的屬性清單。

若要從 Sybase 複製資料，支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **SybaseTable** | 是 |
| tableName | Sybase 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

如果您使用 `RelationalTable` 具型別資料集，雖然仍照現狀支援，但建議您往後使用新的版本。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Sybase 來源所支援的屬性清單。

### <a name="sybase-as-source"></a>Sybase 作為來源

若要從 Sybase 複製資料，[複製活動 **來源** ] 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為： **SybaseSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` 。 | 否 (如果已指定資料集中的 "tableName") |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果您使用 `RelationalSource` 具型別來源，雖然仍照現狀支援，但建議您往後使用新的版本。

## <a name="data-type-mapping-for-sybase"></a>Sybase 的資料類型對應

從 Sybase 複製資料時，會使用下列從 Sybase 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

Sybase 支援 T-SQL 類型。 如需從 SQL 類型對應到 Azure Data Factory 過渡期資料類型的對應表，請參閱 [Azure SQL Database 連接器 - 資料類型對應](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database)一節。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。



## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
