---
title: 使用 Azure 資料工廠從 IBM Informix 源複製資料
description: 瞭解如何使用 Azure 資料工廠管道中的複製活動將資料從 IBM Informix 源複製到受支援的接收器資料存儲。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: cb4b81f7c5e219c520078ecf34eba3f5e98da684
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892582"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>使用 Azure 資料工廠從 IBM Informix 資料存儲複製資料

本文概述了如何使用 Azure 資料工廠中的複製活動從 IBM Informix 資料存儲中複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Informix 連接器：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從 Informix 源複製到任何受支援的接收器資料存儲。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

## <a name="prerequisites"></a>Prerequisites

要使用此 Informix 連接器，您需要：

- 設定一個「自我裝載 Integration Runtime」。 有關詳細資訊[，請參閱自託管集成運行時](create-self-hosted-integration-runtime.md)文章。
- 為集成運行時電腦上的資料存儲安裝 Informix ODBC 驅動程式。 例如，您可以使用驅動程式"IBM INFORMIX INformix 驅動程式 （64 位）"。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于 Informix 連接器的資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

Informix 連結服務支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設置為 **：Informix** | 是 |
| connectionString | ODBC 連接字串（不包括憑據部分）。 您可以指定連接字串或使用在集成運行時電腦上設置的系統 DSN（資料來源名稱）（您仍然需要相應地在連結服務中指定憑據部分）。 <br> 您還可以在 Azure 金鑰保存庫中輸入密碼， `password` 並將配置從連接字串中拔出。 有關詳細資訊，請參閱 [在 Azure 金鑰保存庫中](store-credentials-in-key-vault.md) 存儲憑據。| 是 |
| authenticationType | 用於連接到 Informix 資料存儲的身份驗證類型。<br/>允許的值為：**Basic** (基本) 和 **Anonymous** (匿名)。 | 是 |
| userName | 如果您要使用 Basic 驗證，請指定使用者名稱。 | 否 |
| 密碼 | 指定您為 userName 指定之使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| 認證 (credential) | 以驅動程式特定「屬性-值」格式指定之連接字串的存取認證部分。 請將此欄位標示為 SecureString。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**例子：**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

有關可用於定義資料集的節和屬性的完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Informix 資料集支援的屬性清單。

要從 Informix 複製資料，支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設置為 **：InformixTable** | 是 |
| tableName | Informix 中的表的名稱。 | 就來源而言為非必要 (如果已指定活動來源中的「查詢」)；<br/>就接收器而言為必要 |

**範例**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Informix 源支援的屬性清單。

### <a name="informix-as-source"></a>Informix 作為源

要從 Informix 複製資料，複製活動**源**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須將複製活動源的類型屬性設置為 **：InformixSource** | 是 |
| 查詢 | 使用自訂查詢來讀取資料。 例如：`"SELECT * FROM MyTable"`。 | 否 (如果已指定資料集中的 "tableName") |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
