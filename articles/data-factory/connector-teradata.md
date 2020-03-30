---
title: 使用 Azure 資料工廠從 Teradata Vantage 複製資料
description: 資料工廠服務的 Teradata 連接器允許您將資料從 Teradata Vantage 複製到資料工廠支援的資料存儲作為接收器。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: c7c6cebf0a5c6371893dff52b2e8d7c064a40084
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257932"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>使用 Azure 資料工廠從 Teradata Vantage 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
>
> * [版本 1](v1/data-factory-onprem-teradata-connector.md)
> * [當前版本](connector-teradata.md)

本文概述了如何使用 Azure 資料工廠中的複製活動從 Teradata Vantage 複製資料。 它基於[複製活動概述](copy-activity-overview.md)。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Teradata 連接器：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從 Teradata Vantage 複製到任何受支援的接收器資料存儲。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Teradata 連接器支援：

- 太資料**版本 14.10、 15.0、 15.10、 16.0、 16.10 和 16.20**.
- 使用**基本**或**Windows**身份驗證複製資料。
- 從 Teradata 源並行複製。 有關詳細資訊，請參閱[Teradata 部分的並行副本](#parallel-copy-from-teradata)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

如果使用自託管集成運行時，請注意，它提供了從版本 3.18 開始的內置 Teradata 驅動程式。 您無需手動安裝任何驅動程式。 該驅動程式要求在自託管集成運行時電腦上使用"可視C++可轉散布 2012 更新 4"。 如果您尚未安裝，[請在此處](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)下載。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于 Teradata 連接器的資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

Teradata 連結服務支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設置為**Teradata**。 | 是 |
| connectionString | 指定連接到 Teradata 實例所需的資訊。 請參考下列範例。<br/>您還可以在 Azure 金鑰保存庫中放置密碼，然後從連接`password`字串中提取配置。 有關詳細資訊，請參閱[在 Azure 金鑰保存庫中存儲憑據](store-credentials-in-key-vault.md)。 | 是 |
| username | 指定要連接到 Teradata 的使用者名。 使用 Windows 身份驗證時應用。 | 否 |
| 密碼 | 為使用者名指定的使用者帳戶指定密碼。 您還可以選擇[引用存儲在 Azure 金鑰保存庫中的機密](store-credentials-in-key-vault.md)。 <br>在使用 Windows 身份驗證或引用金鑰保存庫中的密碼進行基本驗證時應用。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 從[先決條件](#prerequisites)部分瞭解更多資訊。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

每個案例在連接字串中可以設置的更多連接屬性：

| 屬性 | 描述 | 預設值 |
|:--- |:--- |:--- |
| CharacterSet | 用於會話的字元集。 例如， `CharacterSet=UTF16`.<br><br/>此值可以是使用者定義的字元集，也可以是以下預定義的字元集之一： <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- 換檔 JIS（視窗、DOS 相容、KANJISJIS_0S）<br/>- EUC（Unix 相容，KANJIEC_0U）<br/>- IBM 大型機 （KANJIEBCDIC5035_0I）<br/>- KANJI932_1S0<br/>- BIG5 （TCHBIG5_1R0）<br/>- GB （SCHGB2312_1T0）<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- 網路韓語 （HANGULKSC5601_2R4）<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | 預設值為 `ASCII`。 |
| 最大重值 |SQL 請求的回應緩衝區的最大大小（以千位元組 （KB） 為單位。 例如， `MaxRespSize=‭10485760‬`.<br/><br/>對於 Teradata 資料庫版本 16.00 或更高版本，最大值為 7361536。 對於使用早期版本的連接，最大值為 1048576。 | 預設值為 `65536`。 |

**使用基本驗證的示例**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**使用 Windows 身份驗證的示例**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 仍然支援以下有效負載。 但是，今後您應該使用新的。

**先前的承載：**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

本節提供 Teradata 資料集支援的屬性清單。 有關可用於定義資料集的節和屬性的完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。

要從 Teradata 複製資料，支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設置為`TeradataTable`。 | 是 |
| [資料庫] | Teradata 實例的名稱。 | 否 (如果已指定活動來源中的「查詢」) |
| 資料表 | Teradata 實例中的表的名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**例子：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`類型資料集仍然受支援。 但是，我們建議您使用新資料集。

**先前的承載：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 Teradata 來源所支援的屬性清單。 如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="teradata-as-source"></a>Teradata 作為來源

>[!TIP]
>要使用資料分區有效地載入 Teradata 的資料，[請從 Teradata 部分的並行副本](#parallel-copy-from-teradata)中學習詳細資訊。

要從 Teradata 複製資料，複製活動**源**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須將複製活動源的類型屬性設置為`TeradataSource`。 | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。<br>啟用分區載入時，需要在查詢中掛鉤任何相應的內置分區參數。 有關示例，請參閱[Teradata 部分的並行副本](#parallel-copy-from-teradata)。 | 否（如果指定了資料集中的表） |
| 分區選項 | 指定用於從 Teradata 載入資料的資料分區選項。 <br>允許值為：**無**（預設）、**雜湊**值和**動態範圍**。<br>啟用分區選項（即不是`None`），從 Teradata 同時載入資料的並行性程度由複製活動上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)設置控制。 | 否 |
| 分區設置 | 指定資料分區設置的組。 <br>當分區選項未時`None`應用。 | 否 |
| 分區列名稱 | 指定區域分區或雜湊分割將用於並行複製的源列的名稱。 如果未指定，則表的主索引將自動檢測並用作分區列。 <br>當分區選項為`Hash`或`DynamicRange`時應用。 如果使用查詢檢索來源資料，請掛鉤`?AdfHashPartitionCondition`或`?AdfRangePartitionColumnName`WHERE 子句中。 請參閱從[Teradata 部分的並行副本](#parallel-copy-from-teradata)中的示例。 | 否 |
| 分區 上邊界 | 要將資料複製出去的分區列的最大值。 <br>當分區選項為`DynamicRange`時應用。 如果使用查詢檢索來源資料，請掛鉤`?AdfRangePartitionUpbound`WHERE 子句。 例如，請參閱[Teradata 部分的並行副本](#parallel-copy-from-teradata)。 | 否 |
| 分區下限 | 要將資料複製出去的分區列的最小值。 <br>當分區選項為`DynamicRange`時應用。 如果使用查詢檢索來源資料，請掛鉤`?AdfRangePartitionLowbound`WHERE 子句。 例如，請參閱[Teradata 部分的並行副本](#parallel-copy-from-teradata)。 | 否 |

> [!NOTE]
>
> `RelationalSource`類型複製源仍然受支援，但它不支援來自 Teradata（分區選項）的內置並行負載。 但是，我們建議您使用新資料集。

**示例：使用沒有分區的基本查詢複製資料**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>來自 Teradata 的並行副本

資料工廠 Teradata 連接器提供內置資料分區，以並行複製來自 Teradata 的資料。 您可以在複製活動的**源**表上找到資料分區選項。

![分區選項的螢幕截圖](./media/connector-teradata/connector-teradata-partition-options.png)

啟用分區副本時，資料工廠會針對 Teradata 源運行並行查詢，以便按分區載入資料。 並行度由複製活動上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)設置控制。 例如，如果設置為`parallelCopies`4，則資料工廠會根據指定的分區選項和設置同時生成和運行四個查詢，並且每個查詢從 Teradata 檢索一部分資料。

建議使用資料分區啟用並行複製，尤其是在從 Teradata 載入大量資料時。 以下是針對不同方案的建議配置。 將資料複製到基於檔的資料存儲中時，將重新命令以多個檔形式寫入資料夾（僅指定資料夾名稱），在這種情況下，性能優於寫入單個檔。

| 狀況                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 大桌子滿載。                                   | **分區選項**：雜湊。 <br><br/>在執行期間，資料工廠會自動檢測 PK 列，對它應用雜湊，並通過分區複製資料。 |
| 使用自訂查詢載入大量資料。                 | **分區選項**：雜湊。<br>**查詢** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`： .<br>**分區列**：指定用於應用雜湊分割的列。 如果未指定，資料工廠將自動檢測您在 Teradata 資料集中指定的表的 PK 列。<br><br>在執行期間，資料工廠`?AdfHashPartitionCondition`將替換為雜湊分割邏輯，併發送到 Teradata。 |
| 通過使用自訂查詢載入大量資料，該查詢具有具有均勻分佈值的整數列，用於定界分割。 | **分區選項**：動態範圍分區。<br>**查詢** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`： .<br>**分區列**：指定用於對資料進行分區的列。 您可以使用整數資料類型對列進行分區。<br>**分區上限**和**分區下限**：指定是否要針對分區列進行篩選，以便僅在下部和上部範圍之間檢索資料。<br><br>在執行期間，資料工廠`?AdfRangePartitionColumnName``?AdfRangePartitionUpbound``?AdfRangePartitionLowbound`將 替換 和 替換為每個分區的實際列名稱和值範圍，併發送到 Teradata。 <br>例如，如果分區列"ID"設置下限為 1，上限設置為 80，並行副本設置為 4，則資料工廠將資料按 4 個分區檢索資料。 他們的年號分別在[1，20]、[21、40]、[41、60]和[61、80]之間。 |

**示例：使用雜湊分割的查詢**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**示例：具有動態範圍分區的查詢**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata 的資料類型對應

從 Teradata 複製資料時，將應用以下映射。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Teradata 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |Datetime |
| Decimal |Decimal |
| Double |Double |
| Graphic |不支援。 在源查詢中應用顯式強制轉換。 |
| 整數  |Int32 |
| Interval Day |不支援。 在源查詢中應用顯式強制轉換。 |
| 間隔日至小時 |不支援。 在源查詢中應用顯式強制轉換。 |
| 間隔日至分鐘 |不支援。 在源查詢中應用顯式強制轉換。 |
| 間隔日至秒鐘 |不支援。 在源查詢中應用顯式強制轉換。 |
| Interval Hour |不支援。 在源查詢中應用顯式強制轉換。 |
| 間隔小時至分鐘 |不支援。 在源查詢中應用顯式強制轉換。 |
| 間隔小時至秒鐘 |不支援。 在源查詢中應用顯式強制轉換。 |
| Interval Minute |不支援。 在源查詢中應用顯式強制轉換。 |
| 間隔分鐘至秒鐘 |不支援。 在源查詢中應用顯式強制轉換。 |
| Interval Month |不支援。 在源查詢中應用顯式強制轉換。 |
| Interval Second |不支援。 在源查詢中應用顯式強制轉換。 |
| Interval Year |不支援。 在源查詢中應用顯式強制轉換。 |
| 間隔年至月 |不支援。 在源查詢中應用顯式強制轉換。 |
| Number |Double |
| 期間（日期） |不支援。 在源查詢中應用顯式強制轉換。 |
| 期間（時間） |不支援。 在源查詢中應用顯式強制轉換。 |
| 期間（帶時區的時間） |不支援。 在源查詢中應用顯式強制轉換。 |
| Period (Timestamp) |不支援。 在源查詢中應用顯式強制轉換。 |
| 期間（帶時區的時間戳記） |不支援。 在源查詢中應用顯式強制轉換。 |
| SmallInt |Int16 |
| Time |TimeSpan |
| 時區的時間 |TimeSpan |
| 時間戳記 |Datetime |
| 時區的時間戳記 |Datetime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |不支援。 在源查詢中應用顯式強制轉換。 |
| Xml |不支援。 在源查詢中應用顯式強制轉換。 |


## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
