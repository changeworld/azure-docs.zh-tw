---
title: 從 SAP HANA 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 SAP HANA 複製到支援的接收資料存放區。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: 74462b68bea38e4d84219adeedb7c3bb0893bbb4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417230"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP HANA 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-sap-hana-connector.md)
> * [目前版本](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 SAP HANA 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

>[!TIP]
>要瞭解 ADF 對 SAP 資料整合方案的總體支援,請參閱[使用 Azure 數據工廠白皮書進行 SAP 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf),並詳細說明介紹、比較和指導。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 SAP HANA 連接器:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [尋找活動](control-flow-lookup-activity.md)

您可以將資料從 SAP HANA 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 SAP HANA 連接器支援：

- 從任何版本的 SAP HANA 資料庫複製資料。
- 從**HANA 資訊模型**(如分析和計算檢視)和**行/清單**複製數據。
- 使用 **Basic** (基本) 或 **Windows** 驗證來複製資料。
- 從 SAP HANA 源並行複製。 有關詳細資訊,請參閱[SAP HANA 部份的併行副本](#parallel-copy-from-sap-hana)。

> [!TIP]
> 若要將資料複製**到** SAP HANA 資料存放區，請使用一般 ODBC 連接器。 如需詳細資料，請參閱 [SAP HANA 接收器](connector-odbc.md#sap-hana-sink)。 請注意，SAP HANA 連接器和 ODBC 連接器的已連接服務具有不同的類型，因此無法重複使用。

## <a name="prerequisites"></a>Prerequisites

若要使用這個 SAP HANA 接收器，您必須：

- 設定一個「自我裝載 Integration Runtime」。 有關詳細資訊[,請參閱自託管集成運行時](create-self-hosted-integration-runtime.md)文章。
- 在 Integration Runtime 電腦上安裝 SAP HANA ODBC 驅動程式。 您可以從 [SAP 軟體下載中心](https://support.sap.com/swdc)下載 SAP Hana ODBC 驅動程式。 使用關鍵字 **SAP HANA CLIENT for Windows** 搜尋。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP HANA 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SAP HANA 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**SapHana** | 是 |
| connectionString | 使用**基本身份驗證**或**Windows 身份驗證**指定連接到 SAP HANA 所需的資訊。 請參考下列範例。<br>在連接字串中,伺服器/埠是強制性的(預設連接埠為 30015),使用基本身份驗證時必須輸入使用者名和密碼。 有關其他進階設定,請參閱[SAP HANA ODBC 連線屬性](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>您還可以將密碼放入 Azure 密鑰保管庫,然後從連接字串中提取密碼配置。 請參閱[Azure 密鑰保管庫文章中存儲憑據](store-credentials-in-key-vault.md)的詳細資訊。 | 是 |
| userName | 使用 Windows 身份驗證時指定使用者名稱。 範例： `user@domain.com` | 否 |
| 密碼 | 指定使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例:使用基本身份驗證**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例:使用 Windows 認證**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
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

如果您使用的是 SAP HANA 連結服務與以下有效負載,它仍然支援它作為根據,而建議您使用新的未來。

**範例:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
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

有關可用於定義數據集的節和屬性的完整清單,請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 SAP HANA 資料集所支援的屬性清單。

要從 SAP HANA 複製資料,支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為 **:SaphanaTable** | 是 |
| 結構描述 | SAP HANA 資料庫中的架構名稱。 | 否 (如果已指定活動來源中的「查詢」) |
| 資料表 | SAP HANA 資料庫中表的名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

如果使用`RelationalTable`類型化數據集,則仍支援該數據集,同時建議您今後使用新數據集。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SAP HANA 來源所支援的屬性清單。

### <a name="sap-hana-as-source"></a>SAP HANA 作為來源

>[!TIP]
>使用資料分割區高效地從 SAP HANA 中引入數據,[請從 SAP HANA 部分的並行副本](#parallel-copy-from-sap-hana)中學習詳細資訊。

要從 SAP HANA 複製資料,複製活動**來源**部分支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須複製活動來源類型屬性設定為 **:SapHanaSource** | 是 |
| 查詢 | 指定 SQL 查詢從 SAP HANA 執行個體讀取資料。 | 是 |
| 分割區選項 | 指定用於從 SAP HANA 引入數據的數據分區選項。 從[SAP HANA 部分的並行副本](#parallel-copy-from-sap-hana)中瞭解更多資訊。<br>允許值是: **沒有** (預設)、 **物理分割區表**,**薩普哈納動態範圍**。 從[SAP HANA 部分的並行副本](#parallel-copy-from-sap-hana)中瞭解更多資訊。 `PhysicalPartitionsOfTable`只能在從表中複製數據時使用,但不能使用查詢。 <br>啟用分區選項(即不是`None`),從 SAP HANA 同時載入資料的並行性程度[`parallelCopies`](copy-activity-performance-features.md#parallel-copy)由複製活動上的 設置控制。 | False |
| 分割區設定 | 指定資料分割區設定的群組。<br>當分區選項為`SapHanaDynamicRange`時應用。 | False |
| 分割區列名稱 | 指定分區將用於並行複製的來源列的名稱。 如果未指定,則表的索引或主鍵將自動檢測並用作分區列。<br>當分區選項為 `SapHanaDynamicRange`時應用。 如果使用查詢檢索源數據,請挂 `?AdfHanaDynamicRangePartitionCondition`鉤 WHERE 子句。 請參閱[SAP HANA 部分的並行副本](#parallel-copy-from-sap-hana)中的範例。 | 使用`SapHanaDynamicRange`分區時為"是" |
| packetSize | 指定網路封包大小(以千位元組為單位),將資料拆分為多個塊。 如果要複製大量數據,在大多數情況下,增加數據包大小可以提高 SAP HANA 的讀取速度。 調整資料包大小時,建議進行性能測試。 | 否。<br>默認值為 2048 (2MB)。 |

**範例:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果使用`RelationalSource`類型化副本源,則它仍然支援按"當前",同時建議您今後使用新的副本源。

## <a name="parallel-copy-from-sap-hana"></a>來自 SAP HANA 的行複本

數據工廠 SAP HANA 連接器提供內建數據分區,以並行複製來自 SAP HANA 的數據。 您可以在複製活動的**源**表上找到資料分區選項。

![分割區選項的螢幕擷取](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

啟用分區副本時,數據工廠會針對 SAP HANA 源運行並行查詢,以便按分區檢索數據。 並行度由複製活動上的[`parallelCopies`](copy-activity-performance-features.md#parallel-copy)設置控制。 例如,如果將設置為`parallelCopies`4,則數據工廠會根據指定的分區選項和設置同時生成和運行四個查詢,並且每個查詢從 SAP HANA 檢索一部分數據。

建議使用數據分區啟用並行複製,尤其是在從 SAP HANA 中引入大量數據時。 以下是針對不同方案的建議配置。 將資料複製到基於檔案的資料儲存中時,建議將資料寫入資料夾時為多個檔(僅指定資料夾名稱),在這種情況下,性能優於寫入單個檔。

| 狀況                                           | 建議的設定                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| 大桌子滿載。                        | **分區選項**:表的物理分區。 <br><br/>在執行期間,資料工廠會自動偵測指定 SAP HANA 表的物理分區類型,並選擇相應的分區策略:<br>- **範圍分區**:獲取為表定義的分區列和分區範圍,然後按範圍複製數據。 <br>- **哈希分區**:使用哈希分區鍵作為分區列,然後根據 ADF 計算的範圍對數據進行分區和複製。 <br>- **循環分區**或**無分區**:使用主鍵作為分區列,然後根據 ADF 計算的範圍對數據進行分區和複製。 |
| 使用自定義查詢載入大量數據。 | **分區選項**:動態範圍分區。<br>**查詢**`SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`: .<br>**分割區列**:指定用於應用動態範圍分區的欄。 <br><br>在執行期間,資料工廠首先計算指定分區列的值範圍,根據不同的分區列值和 ADF 並行複製設置的數量均勻分佈多個存儲桶中的行,然後`?AdfHanaDynamicRangePartitionCondition`替換為篩選每個分區的分區列值範圍,然後發送到 SAP HANA。<br><br>如果要使用多個欄位作為分區列,可以將每欄的值串聯為查詢中的一列,並將指定為 ADF 中的分割區列`SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`, 如 。 |

**範例:具有表物理分割區的查詢**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**範例:具有動態範圍分割區的查詢**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA 的資料類型對應

從 SAP HANA 複製資料時，會使用下列從 SAP HANA 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| SAP HANA 資料類型 | Data Factory 過渡期資料類型 |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| bigint             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| 日期               | Datetime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| real               | Single                         |
| SECONDDATE         | Datetime                       |
| 短文          | String                         |
| 小十進位       | Decimal                        |
| SMALLINT           | Int16                          |
| 斯幾何     | Byte[]                         |
| STPOINT 類型        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| timestamp          | Datetime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>尋找活動屬性

要瞭解有關屬性的詳細資訊,請檢查[。](control-flow-lookup-activity.md)

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
