---
title: 使用 Azure Data Factory 從 Hive 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 Hive 將資料複製到支援的接收資料存放區。
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
ms.openlocfilehash: 3720d917d71fa4e8c5a14bb60fdc7c405be4bfdb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81410446"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>使用 Azure Data Factory 從 Hive 複製資料 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Hive 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Hive 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Hive 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Hive 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Hive 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為：**Hive** | 是 |
| 主機 | Hive 伺服器的 IP 位址或主機名稱，以 '; ' 分隔多部主機（僅限啟用 serviceDiscoveryMode 時）。  | 是 |
| 連接埠 | Hive 伺服器用來接聽用戶端連線的 TCP 連接埠。 如果您連線到 Azure HDInsights，請將連接埠指定為 443。 | 是 |
| serverType | Hive 伺服器的類型。 <br/>允許的值為：**HiveServer1**、**HiveServer2****HiveThriftServer** | 否 |
| thriftTransportProtocol | Thrift 層中使用的傳輸通訊協定。 <br/>允許的值為：**Binary**、**SASL**、**HTTP** | 否 |
| authenticationType | 用來存取 Hive 伺服器的驗證方法。 <br/>允許的值為：**Anonymous**、**Username****UsernameAndPassword****WindowsAzureHDInsightService** | 是 |
| serviceDiscoveryMode | true 表示使用 ZooKeeper 服務，false 表示不使用 ZooKeeper 服務。  | 否 |
| zooKeeperNameSpace | ZooKeeper 上的命名空間，Hive Server 2 節點會新增在 ZooKeeper 下方。  | 否 |
| useNativeQuery | 指定驅動程式是否使用原生 HiveQL 查詢，或將它們轉換成 HiveQL 中的對等格式。  | 否 |
| username | 您用來存取 Hive 伺服器的使用者名稱。  | 否 |
| password | 對應到使用者的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| httpPath | 對應至 Hive 伺服器的部分 URL。  | 否 |
| enableSsl | 指定是否使用 TLS 來加密與伺服器的連接。 預設值為 false。  | 否 |
| trustedCertPath | 包含信任的 CA 憑證的 pem 檔案完整路徑，可在透過 TLS 連線時驗證服務器。 只有在自我裝載 IR 上使用 TLS 時，才可以設定這個屬性。 預設值為隨 IR 安裝的 cacerts.pem 檔案。  | 否 |
| useSystemTrustStore | 指定是否使用來自系統信任存放區或來自指定 PEM 檔案的 CA 憑證。 預設值為 false。  | 否 |
| allowHostNameCNMismatch | 指定在透過 TLS 連線時，是否要求 CA 發行的 TLS/SSL 憑證名稱符合伺服器的主機名稱。 預設值為 false。  | 否 |
| allowSelfSignedServerCert | 指定是否允許來自伺服器的自我簽署憑證。 預設值為 false。  | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入瞭解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例：**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Hive 資料集所支援的屬性清單。

若要從 Hive 複製資料，請將資料集的類型屬性設定為 **HiveObject**。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **HiveObject** | 是 |
| 結構描述 | 結構描述的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| 資料表 | 資料表的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| tableName | 包含架構元件的資料表名稱。 此屬性支援回溯相容性。 針對新的工作負載`schema` ， `table`請使用和。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Hive 來源所支援的屬性清單。

### <a name="hivesource-as-source"></a>將 HiveSource 作為來源

若要從 Hive 複製資料，請將複製活動中的來源類型設定為 **HiveSource**。 複製活動的 [**來源**] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**HiveSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` 。 | 否 (如果已指定資料集中的 "tableName") |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
