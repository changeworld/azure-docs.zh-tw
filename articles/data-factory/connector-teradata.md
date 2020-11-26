---
title: 使用 Azure Data Factory 從 Teradata 有利複製資料
description: Data Factory 服務的 Teradata 連接器可讓您將 Teradata 有利中的資料複製到 Data Factory 作為接收所支援的資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2020
ms.author: jingwang
ms.openlocfilehash: a48ac86e8f9814adef9be2360b2446335d368447
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296551"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Teradata 有利複製資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
>
> * [第 1 版](v1/data-factory-onprem-teradata-connector.md)
> * [目前的版本](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Teradata 有利複製資料。 它是以 [複製活動總覽](copy-activity-overview.md)為基礎。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Teradata 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Teradata 有利複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Teradata 連接器支援：

- Teradata **版本14.10、15.0、15.10、16.0、16.10 和 16.20**。
- 使用 **基本**、 **Windows** 或 **LDAP** 驗證來複製資料。
- 從 Teradata 來源進行平行複製。 如需詳細資訊，請參閱 [Teradata 的平行複製](#parallel-copy-from-teradata) 一節。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

如果您使用自我裝載的 Integration Runtime，請注意，它會提供從3.18 版開始的內建 Teradata 驅動程式。 您不需要手動安裝任何驅動程式。 驅動程式在自我裝載整合執行時間電腦上需要 "C++ 可轉散發套件 2012 Update 4"。 如果您尚未安裝，請從 [這裡](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)下載。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Teradata 連接器特定的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Teradata 連結服務支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設為 **Teradata**。 | 是 |
| connectionString | 指定連接至 Teradata 實例所需的資訊。 請參考下列範例。<br/>您也可以將密碼放在 Azure Key Vault 中，並 `password` 從連接字串中提取設定。 如需詳細資訊，請參閱 [Azure Key Vault 中的儲存認證](store-credentials-in-key-vault.md) 。 | 是 |
| username | 指定要連接到 Teradata 的使用者名稱。 當您使用 Windows 驗證時適用。 | 否 |
| 密碼 | 為您為使用者名稱指定的使用者帳戶指定密碼。 您也可以選擇 [參考儲存在 Azure Key Vault 中的密碼](store-credentials-in-key-vault.md)。 <br>當您使用 Windows 驗證，或參考 Key Vault 中的密碼進行基本驗證時適用。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

您可以根據您的案例，在連接字串中設定更多的連接屬性：

| 屬性 | 描述 | 預設值 |
|:--- |:--- |:--- |
| UseDataEncryption | 指定是否要加密與 Teradata 資料庫的所有通訊。 允許的值為0或1。<br><br/>- **0 (停用，預設)**：僅加密驗證資訊。<br/>- **1 (啟用)**：加密在驅動程式與資料庫之間傳遞的所有資料。 | `0` |
| CharacterSet | 要用於會話的字元集。 例如， `CharacterSet=UTF16` 。<br><br/>這個值可以是使用者定義的字元集，也可以是下列其中一個預先定義的字元集： <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>-LATIN1252_0A<br/>-LATIN9_0A<br/>-LATIN1_0A<br/>-SHIFT-JIS (Windows、DOS 相容、KANJISJIS_0S) <br/>-EUC (Unix 相容、KANJIEC_0U) <br/>-IBM 大型主機 (KANJIEBCDIC5035_0I) <br/>-KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0) <br/>-GB (SCHGB2312_1T0) <br/>-SCHINESE936_6R0<br/>-TCHINESE950_8R0<br/>-NetworkKorean (HANGULKSC5601_2R4) <br/>-HANGUL949_7R0<br/>-ARABIC1256_6A0<br/>-CYRILLIC1251_2A0<br/>-HEBREW1255_5A0<br/>-LATIN1250_1A0<br/>-LATIN1254_7A0<br/>-LATIN1258_8A0<br/>-THAI874_4A0 | `ASCII` |
| MaxRespSize |SQL 要求的回應緩衝區大小上限，以 kb (Kb) 。 例如， `MaxRespSize=‭10485760‬` 。<br/><br/>針對 Teradata 資料庫16.00 版或更新版本，最大值為7361536。 若是使用舊版的連接，最大值為1048576。 | `65536` |
| MechanismName | 若要使用 LDAP 通訊協定來驗證連接，請指定 `MechanismName=LDAP` 。 | N/A |

**使用基本驗證的範例**

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

**使用 Windows 驗證的範例**

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

**使用 LDAP 驗證的範例**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
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
> 仍支援下列承載。 不過，您應該要使用新的。

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

本節提供 Teradata 資料集所支援的屬性清單。 如需定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。

若要從 Teradata 複製資料，支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為 `TeradataTable` 。 | 是 |
| [資料庫] | Teradata 實例的名稱。 | 否 (如果已指定活動來源中的「查詢」) |
| 資料表 | Teradata 實例中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例︰**

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
> `RelationalTable` 仍然支援型別資料集。 不過，我們建議您使用新的資料集。

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
>若要使用資料分割有效率地從 Teradata 載入資料，請從 [Teradata 區段的平行複製](#parallel-copy-from-teradata) 深入瞭解。

若要從 Teradata 複製資料，[複製活動 **來源** ] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為 `TeradataSource` 。 | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。<br>當您啟用分割區載入時，您必須在查詢中攔截任何對應的內建資料分割參數。 如需範例，請參閱 Teradata 一節 [中的平行複製](#parallel-copy-from-teradata) 。 | 如果已指定資料集中的資料表，則沒有 ()  |
| partitionOptions | 指定用來從 Teradata 載入資料的資料分割選項。 <br>允許的值為： **None** (預設) 、 **Hash** 和 **DynamicRange**。<br>啟用分割區選項時 (也就是，不 `None`) 時，從 Teradata 並行載入資料的平行處理原則程度，是由 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 複製活動上的設定所控制。 | 否 |
| partitionSettings | 指定資料分割的設定群組。 <br>當分割選項不是 `None` 時套用。 | 否 |
| partitionColumnName | 指定要由範圍分割區或雜湊資料分割用於平行複製的來源資料行名稱。 如果未指定，則會 autodetected 資料表的主要索引，並將其當做資料分割資料行使用。 <br>當資料分割選項為或時套用 `Hash` `DynamicRange` 。 如果您使用查詢來取出來源資料，請攔截 `?AdfHashPartitionCondition` 或  `?AdfRangePartitionColumnName` 在 WHERE 子句中。 請參閱 [從 Teradata 並行複製的](#parallel-copy-from-teradata) 範例一節。 | 否 |
| partitionUpperBound | 從分割資料行複製出資料時的最大值。 <br>當分割選項是 `DynamicRange` 時套用。 如果您使用查詢來取出來源資料，請在 WHERE 子句中加上 `?AdfRangePartitionUpbound`。 如需範例，請參閱 [Teradata 一節中的平行複製](#parallel-copy-from-teradata) 。 | 否 |
| partitionLowerBound | 從分割資料行複製出資料時的最小值。 <br>當分割選項是 `DynamicRange` 時套用。 如果您使用查詢來取出來源資料，請在 WHERE 子句中加上 `?AdfRangePartitionLowbound`。 如需範例，請參閱 [Teradata 一節中的平行複製](#parallel-copy-from-teradata) 。 | No |

> [!NOTE]
>
> `RelationalSource` 類型複製來源仍受支援，但不支援 Teradata (分割區選項) 的新內建平行載入。 不過，我們建議您使用新的資料集。

**範例：使用沒有資料分割的基本查詢來複製資料**

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

## <a name="parallel-copy-from-teradata"></a>從 Teradata 進行平行複製

Data Factory Teradata 連接器提供內建的資料分割，以平行方式從 Teradata 複製資料。 您可以在複製活動的 [來源] 資料表上找到資料分割選項。

![分割選項的螢幕擷取畫面](./media/connector-teradata/connector-teradata-partition-options.png)

當您啟用分割區複製時，Data Factory 會針對您的 Teradata 來源執行平行查詢，以依分割區載入資料。 平行程度由複製活動的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定所控制。 例如，如果您設定 `parallelCopies` 為四，Data Factory 會同時產生並執行四個以指定的資料分割選項和設定為基礎的查詢，而每個查詢都會從您的 Teradata 中取出部分資料。

當您從 Teradata 載入大量資料時，建議您啟用資料分割的平行複製。 以下針對各種情節的建議設定。 將資料複製到以檔案為基礎的資料存放區時，建議分成多個檔案來寫入資料夾 (僅指定資料夾名稱)，這樣效能會比寫入單一檔案更好。

| 狀況                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 從大型資料表完整載入。                                   | **分割區選項**： Hash。 <br><br/>在執行期間，Data Factory 會自動偵測主要索引資料行、對其套用雜湊，並依資料分割複製資料。 |
| 使用自訂查詢載入大量資料。                 | **分割區選項**： Hash。<br>**查詢**：`SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>**分割** 區資料行：指定用於套用雜湊資料分割的資料行。 如果未指定，Data Factory 會自動偵測您在 Teradata 資料集中指定之資料表的 PK 資料行。<br><br>在執行期間，Data Factory 會 `?AdfHashPartitionCondition` 以雜湊分割邏輯取代，並傳送至 Teradata。 |
| 使用自訂查詢載入大量資料，並以一個整數資料行的平均分佈值來分割範圍。 | **分割選項**：動態範圍分割。<br>**查詢**：`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分割資料行**：指定用來分割資料的資料行。 您可以按照整數資料類型的資料行來分割。<br>**分割上限** 和 **分割下限**：指定您是否想要篩選分割資料行，而只取出下限範圍和上限範圍之間的資料。<br><br>在執行期間，Data Factory 會以 `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` `?AdfRangePartitionLowbound` 每個資料分割的實際資料行名稱和值範圍取代、和，並傳送至 Teradata。 <br>例如，如果分割資料行 "ID" 已設定下限 1 和上限 80，且平行複製設定為 4，則 Data Factory 會分成 4 個分割區來取出資料。 識別碼的範圍分別為 [1,20]、[21, 40]、[41, 60] 和 [61, 80]。 |

**範例：使用雜湊分割的查詢**

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

**範例：使用動態範圍分割進行查詢**

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

當您從 Teradata 複製資料時，會套用下列對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

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
| Graphic |不支援。 在來源查詢中套用明確轉換。 |
| 整數 |Int32 |
| Interval Day |不支援。 在來源查詢中套用明確轉換。 |
| 間隔日至小時 |不支援。 在來源查詢中套用明確轉換。 |
| 間隔日至分鐘 |不支援。 在來源查詢中套用明確轉換。 |
| 間隔日至秒鐘 |不支援。 在來源查詢中套用明確轉換。 |
| Interval Hour |不支援。 在來源查詢中套用明確轉換。 |
| 間隔小時至分鐘 |不支援。 在來源查詢中套用明確轉換。 |
| 間隔小時至秒鐘 |不支援。 在來源查詢中套用明確轉換。 |
| Interval Minute |不支援。 在來源查詢中套用明確轉換。 |
| 間隔分鐘至秒鐘 |不支援。 在來源查詢中套用明確轉換。 |
| Interval Month |不支援。 在來源查詢中套用明確轉換。 |
| Interval Second |不支援。 在來源查詢中套用明確轉換。 |
| Interval Year |不支援。 在來源查詢中套用明確轉換。 |
| 間隔年至月 |不支援。 在來源查詢中套用明確轉換。 |
| 數字 |Double |
| 期間 (日期)  |不支援。 在來源查詢中套用明確轉換。 |
| 期間 (時間)  |不支援。 在來源查詢中套用明確轉換。 |
| 使用時區) 的期間 (時間 |不支援。 在來源查詢中套用明確轉換。 |
| Period (Timestamp) |不支援。 在來源查詢中套用明確轉換。 |
| 具有時區) 的期間 (時間戳記 |不支援。 在來源查詢中套用明確轉換。 |
| SmallInt |Int16 |
| Time |TimeSpan |
| 時區的時間 |TimeSpan |
| 時間戳記 |Datetime |
| 時區的時間戳記 |Datetime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |不支援。 在來源查詢中套用明確轉換。 |
| Xml |不支援。 在來源查詢中套用明確轉換。 |


## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
