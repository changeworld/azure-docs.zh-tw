---
title: 使用 Azure Data Factory 從 DB2 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 DB2 將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: jingwang
ms.openlocfilehash: 3c65ed7e5fa6bb1652791eee75d4caa4c9c5f1ca
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873642"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 DB2 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-onprem-db2-connector.md)
> * [目前的版本](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 DB2 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 DB2 資料庫連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以從 DB2 資料庫將資料複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此 DB2 連接器支援以下 IBM DB2 平台和版本，以及支援分散式關聯資料庫架構 (DRDA) SQL 存取管理員 (SQLAM) 版本 9、10 和 11。  其會利用 DDM/DRDA 通訊協定。

* IBM DB2 for z/OS 12.1
* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.3
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

>[!TIP]
>DB2 連接器是建置在 Microsoft OLE DB Provider for DB2 之上。 若要針對 DB2 連接器錯誤進行疑難排解，請參閱 [Data Provider 錯誤碼](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

「整合執行階段」提供內建的 DB2 驅動程式，因此從 DB2 複製資料時，您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 DB2 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 DB2 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**Db2** | 是 |
| connectionString | 指定連線到 DB2 執行個體所需的資訊。<br/> 您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取 `password` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

連接字串內的一般屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| 伺服器 |DB2 伺服器的名稱。 您可以指定在伺服器名稱後面加上以冒號隔開的連接埠號碼，例如 `server:port`。<br>DB2 連接器會利用 DDM/DRDA 通訊協定，但若未指定，則預設會使用連接埠 50000。 特定 DB2 資料庫所使用的連接埠可能會根據版本和您的設定而有所不同，例如對於 DB2 LUW，預設連接埠為 50000，對於 AS400，預設連接埠則為 446 或 448 (若已啟用 TLS 的話)。 如需一般設定連接埠的方式，請參閱下列 DB2 文件：[DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html)、[DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm) 和 [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html)。 |是 |
| [資料庫] |DB2 資料庫的名稱。 |是 |
| authenticationType |用來連接到 DB2 資料庫的驗證類型。<br/>允許的值為：**基本**。 |是 |
| username |指定要連線到 DB2 資料庫的使用者名稱。 |是 |
| 密碼 |指定您為使用者名稱所指定之使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| packageCollection | 指定在查詢資料庫時，ADF 自動建立所需套件的位置。 如果未設定此項，Data Factory 會使用 {username} 做為預設值。 | 否 |
| certificateCommonName | 當您使用安全通訊端層 (SSL) 或傳輸層安全性 (TLS) 加密時，必須為憑證一般名稱輸入一值。 | 否 |

> [!TIP]
> 如果您收到指出 `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805` 的錯誤訊息，原因是未對使用者建立所需的套件。 根據預設，ADF 會嘗試以您用來連線到 DB2 的使用者身分，在集合下建立套件。 指定套件集合屬性，以指出在查詢資料庫時，ADF 要建立所需套件的位置。

**範例︰**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
**範例：在 Azure Key Vault 中儲存密碼**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
            "password": { 
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

如果您使用具有以下承載的 DB2 連結服務，其仍然如同現狀受支援，但建議您使用新版本。

**先前的承載：**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 DB2 資料集所支援的屬性清單。

若要從 DB2 複製資料，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**Db2Table** | 是 |
| 結構描述 | 結構描述的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| 資料表 | 資料表的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| tableName | 具有結構描述的資料表名稱。 支援此屬性是基於回溯相容性。 對於新的工作負載，請使用 `schema` 和 `table`。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

如果您使用 `RelationalTable` 具型別資料集，雖然仍照現狀支援，但建議您往後使用新的版本。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 DB2 來源所支援的屬性清單。

### <a name="db2-as-source"></a>DB2 作為來源

若要從 DB2 複製資料，複製活動 **source** 區段中支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**Db2Source** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""` 。 | 否 (如果已指定資料集中的 "tableName") |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果您使用 `RelationalSource` 具型別來源，雖然仍照現狀支援，但建議您往後使用新的版本。

## <a name="data-type-mapping-for-db2"></a>DB2 的資料類型對應

從 DB2 複製資料時，會使用下列從 DB2 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| DB2 資料庫類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| Date |Datetime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| 整數 |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| 數值 |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| 時間戳記 |Datetime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>查詢活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
