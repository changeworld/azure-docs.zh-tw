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
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 4207c4ddfcbab325b1ae119dcd200af30fc59f58
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844929"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>使用 Azure Data Factory 從 Hive 複製和轉換資料 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Hive 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Hive 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Hive 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Hive 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Hive 連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為：**Hive** | 是 |
| 主機 | Hive 伺服器的 IP 位址或主機名稱（以 '; ' 分隔），只有在啟用 serviceDiscoveryMode) 時才 (。  | 是 |
| 連接埠 | Hive 伺服器用來接聽用戶端連線的 TCP 連接埠。 如果您連線到 Azure HDInsights，請將連接埠指定為 443。 | 是 |
| serverType | Hive 伺服器的類型。 <br/>允許的值為：**HiveServer1**、**HiveServer2****HiveThriftServer** | 否 |
| thriftTransportProtocol | Thrift 層中使用的傳輸通訊協定。 <br/>允許的值為：**Binary**、**SASL**、**HTTP** | 否 |
| authenticationType | 用來存取 Hive 伺服器的驗證方法。 <br/>允許的值為： **Anonymous**、 **Username**、 **UsernameAndPassword**、 **>windowsazurehdinsightservice**。 目前不支援 Kerberos 驗證。 | 是 |
| serviceDiscoveryMode | true 表示使用 ZooKeeper 服務，false 表示不使用 ZooKeeper 服務。  | 否 |
| zooKeeperNameSpace | ZooKeeper 上的命名空間，Hive Server 2 節點會新增在 ZooKeeper 下方。  | 否 |
| useNativeQuery | 指定驅動程式是否使用原生 HiveQL 查詢，或將它們轉換成 HiveQL 的相等形式。  | 否 |
| username | 您用來存取 Hive 伺服器的使用者名稱。  | 否 |
| 密碼 | 對應到使用者的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| httpPath | 對應至 Hive 伺服器的部分 URL。  | 否 |
| enableSsl | 指定是否使用 TLS 加密與伺服器的連接。 預設值為 false。  | 否 |
| trustedCertPath | Pem 檔案的完整路徑，包含信任的 CA 憑證，以便在透過 TLS 連接時驗證服務器。 只有在自我裝載 IR 上使用 TLS 時，才能設定此屬性。 預設值為隨 IR 安裝的 cacerts.pem 檔案。  | 否 |
| useSystemTrustStore | 指定是否使用來自系統信任存放區或來自指定 PEM 檔案的 CA 憑證。 預設值為 false。  | 否 |
| allowHostNameCNMismatch | 指定在透過 TLS 連線時，是否要求 CA 發出的 TLS/SSL 憑證名稱符合伺服器的主機名稱。 預設值為 false。  | 否 |
| allowSelfSignedServerCert | 指定是否允許來自伺服器的自我簽署憑證。 預設值為 false。  | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |
| storageReference | 在對應資料流程中用於暫存資料之儲存體帳戶的連結服務參考。 只有在對應資料流程中使用 Hive 連結服務時，才需要此項 | 否 |

**範例︰**

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

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **HiveObject** | 是 |
| 結構描述 | 結構描述的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| 資料表 | 資料表的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| tableName | 資料表的名稱，包括架構部分。 支援此屬性是基於回溯相容性。 對於新的工作負載，請使用 `schema` 和 `table`。 | 否 (如果已指定活動來源中的「查詢」) |

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

若要從 Hive 複製資料，請將複製活動中的來源類型設定為 **HiveSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**HiveSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` 。 | 否 (如果已指定資料集中的 "tableName") |

**範例︰**

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

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中，以 [內嵌資料集](data-flow-source.md#inline-datasets) 來源的形式支援 hive 連接器。 使用查詢或直接從 HDInsight 中的 Hive 資料表讀取。 Hive 資料會在儲存體帳戶中暫存為 parquet 檔案，然後再轉換成資料流程的一部分。 

### <a name="source-properties"></a>來源屬性

下表列出 hive 來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 市集 | 商店必須是 `hive` | 是 |  `hive` | store | 
| 格式 | 是否從資料表或查詢讀取 | 是 | `table` 或 `query` | format |
| 結構描述名稱 | 如果從資料表讀取，則為來源資料表的架構 |  是，如果格式為 `table` | 字串 | schemaName |
| 資料表名稱 | 如果從資料表讀取，則為數據表名稱 |   是，如果格式為 `table` | 字串 | tableName |
| 查詢 | 如果格式為 `query` ，則為 Hive 連結服務上的來源查詢 | 是，如果格式為 `query` | 字串 | 查詢 |
| 上演 | 系統一律會暫存 Hive 資料表。 | 是 | `true` | 上演 |
| 儲存體容器 | 用來暫存資料，然後從 Hive 讀取或寫入 Hive 的儲存體容器。 Hive 叢集必須具有此容器的存取權。 | 是 | 字串 | storageContainer |
| 暫存資料庫 | 在連結服務中指定的使用者帳戶可以存取的架構/資料庫。 它是用來在暫存期間建立外部資料表，之後再卸載 | 否 | `true` 或 `false` | stagingDatabaseName |
| 預先 SQL 腳本 | 要在讀取資料前于 Hive 資料表上執行的 SQL 程式碼 | 否 | 字串 | preSQLs |

#### <a name="source-example"></a>來源範例

以下是 Hive 來源設定的範例：

![Hive 來源範例](media/data-flow/hive-source.png "[Hive 來源範例")

這些設定會轉譯成下列資料流程腳本：

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>已知限制

* 不支援讀取複雜類型，例如陣列、對應、結構和等位。 
* Hive 連接器只支援4.0 版或更高版本 (Apache Hive 3.1.0 的 Azure HDInsight Hive 資料表) 

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
