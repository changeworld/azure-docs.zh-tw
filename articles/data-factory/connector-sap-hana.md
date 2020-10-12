---
title: 從 SAP Hana 複製資料
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
ms.date: 04/22/2020
ms.openlocfilehash: 92cc94170a01aceaa3e6bd058f4ae6628db04f18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87529580"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP HANA 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-sap-hana-connector.md)
> * [目前的版本](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 SAP HANA 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

>[!TIP]
>若要瞭解 ADF 對 SAP 資料整合案例的整體支援，請參閱 [使用 Azure Data Factory 白皮書的 sap 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ，其中包含每個 sap 連接器、比較和指引的詳細簡介。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 SAP Hana 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 SAP HANA 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 SAP HANA 連接器支援：

- 從任何版本的 SAP HANA 資料庫複製資料。
- 從 **HANA 資訊模型** 複製資料 (例如分析和計算視圖) 和資料 **列/資料行資料表**。
- 使用 **Basic** (基本) 或 **Windows** 驗證來複製資料。
- 從 SAP Hana 來源進行平行複製。 如需詳細資訊，請參閱 SAP Hana 一節 [中的平行複製](#parallel-copy-from-sap-hana) 。

> [!TIP]
> 若要將資料複製**到** SAP HANA 資料存放區，請使用一般 ODBC 連接器。 請參閱 [SAP Hana 接收](#sap-hana-sink) 區段和詳細資料。 請注意，SAP HANA 連接器和 ODBC 連接器的已連接服務具有不同的類型，因此無法重複使用。

## <a name="prerequisites"></a>Prerequisites

若要使用這個 SAP HANA 接收器，您必須：

- 設定一個「自我裝載 Integration Runtime」。 如需詳細資訊，請參閱自我裝載的 [Integration Runtime](create-self-hosted-integration-runtime.md) 文章。
- 在 Integration Runtime 電腦上安裝 SAP HANA ODBC 驅動程式。 您可以從 [SAP 軟體下載中心](https://support.sap.com/swdc)下載 SAP Hana ODBC 驅動程式。 使用關鍵字 **SAP HANA CLIENT for Windows** 搜尋。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP HANA 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SAP HANA 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**SapHana** | 是 |
| connectionString | 使用 **基本驗證** 或 **Windows 驗證**，指定連接 SAP Hana 所需的資訊。 請參考下列範例。<br>在連接字串中，伺服器/埠是強制的 (預設埠是 30015) ，而使用基本驗證時，使用者名稱和密碼是必要的。 如需其他 advanced 設定，請參閱 [SAP HANA ODBC 連接屬性](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取密碼設定。 如需詳細資訊，請參閱 [Azure Key Vault 文章中的儲存認證](store-credentials-in-key-vault.md) 。 | 是 |
| userName | 使用 Windows 驗證時，請指定使用者名稱。 範例： `user@domain.com` | 否 |
| 密碼 | 指定使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例：使用基本驗證**

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

**範例：使用 Windows 驗證**

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

如果您使用 SAP Hana 連結服務搭配下列承載，則仍會依原樣受到支援，但建議您繼續使用新的服務。

**範例︰**

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 SAP HANA 資料集所支援的屬性清單。

若要從 SAP Hana 複製資料，支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **SapHanaTable** | 是 |
| 結構描述 | SAP Hana 資料庫中的架構名稱。 | 否 (如果已指定活動來源中的「查詢」) |
| 資料表 | SAP Hana 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例︰**

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

如果您使用 `RelationalTable` 具型別資料集，雖然仍照現狀支援，但建議您往後使用新的版本。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SAP HANA 來源所支援的屬性清單。

### <a name="sap-hana-as-source"></a>SAP HANA 作為來源

>[!TIP]
>若要使用資料分割有效率地將資料內嵌 SAP Hana，請從 SAP Hana 一節中深入瞭解 [平行複製](#parallel-copy-from-sap-hana) 。

若要從 SAP Hana 複製資料，[複製活動 **來源** ] 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為： **SapHanaSource** | 是 |
| 查詢 | 指定 SQL 查詢從 SAP HANA 執行個體讀取資料。 | 是 |
| partitionOptions | 指定用來從 SAP Hana 內嵌資料的資料分割選項。 從 SAP Hana 一節深入瞭解  [平行複製](#parallel-copy-from-sap-hana) 。<br>允許的值為： **無**   (預設值) 、 **PhysicalPartitionsOfTable**、 **SapHanaDynamicRange**。 從 SAP Hana 一節深入瞭解  [平行複製](#parallel-copy-from-sap-hana) 。 `PhysicalPartitionsOfTable` 只能在從資料表複製資料時使用，但不能用於查詢。 <br>當您啟用分割區選項時 (也就是不 `None`) ，從 SAP Hana 並行載入資料的平行處理原則程度，是由 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 複製活動上的設定所控制。 | False |
| partitionSettings | 指定資料分割的設定群組。<br>當分割選項是 `SapHanaDynamicRange` 時套用。 | False |
| partitionColumnName | 指定將由資料分割用於平行複製之來源資料行的名稱。 如果未指定，則會自動偵測資料表的索引或主鍵，並將其當做資料分割資料行使用。<br>適用于分割區選項為時  `SapHanaDynamicRange` 。 如果您使用查詢來取出來源資料，請  `?AdfHanaDynamicRangePartitionCondition` 在 WHERE 子句中掛上。 請參閱 [從 SAP Hana 區段平行複製的](#parallel-copy-from-sap-hana) 範例。 | 使用分割區時為 Yes `SapHanaDynamicRange` 。 |
| packetSize | 指定要將資料分割成多個區塊的網路封包大小 (以 Kb) 。 如果您有大量資料要複製，增加封包大小可能會在大部分情況下從 SAP Hana 增加讀取速度。 調整封包大小時，建議使用效能測試。 | 否。<br>預設值為 2048 (2MB) 。 |

**範例︰**

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

如果您使用 `RelationalSource` 的是具類型的複製來源，則仍會依原樣受到支援，但建議您繼續使用新的來源。

## <a name="parallel-copy-from-sap-hana"></a>從 SAP Hana 進行平行複製

Data Factory SAP Hana 連接器提供內建的資料分割，以平行方式從 SAP Hana 複製資料。 您可以在複製活動的 [來源] 資料表上找到資料分割選項。

![分割選項的螢幕擷取畫面](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

當您啟用分割區複製時，Data Factory 會對您的 SAP Hana 來源執行平行查詢，以依資料分割抓取資料。 平行程度由複製活動的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定所控制。 例如，如果您設定 `parallelCopies` 為四，Data Factory 會同時產生並執行四個以指定的資料分割選項和設定為基礎的查詢，而每個查詢都會從您的 SAP Hana 中取出部分資料。

建議您啟用資料分割的平行複製，尤其是當您從 SAP Hana 內嵌大量資料時。 以下針對各種情節的建議設定。 將資料複製到以檔案為基礎的資料存放區時，建議您將資料夾做為多個檔案寫入 (只指定資料夾名稱) ，在此情況下，效能比寫入單一檔案更好。

| 狀況                                           | 建議的設定                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| 從大型資料表完整載入。                        | **分割區選項**：資料表的實體分割區。 <br><br/>在執行期間，Data Factory 會自動偵測指定 SAP Hana 資料表的實體分割類型，並選擇對應的資料分割策略：<br>- **範圍分割**：取得為數據表定義的分割區資料行和資料分割範圍，然後依範圍複製資料。 <br>- **雜湊資料分割**：使用雜湊資料分割索引鍵做為分割區資料行，然後根據 ADF 計算範圍來分割及複製資料。 <br>- **迴圈** 配置資源資料分割或 **沒有分割**區：使用主鍵作為分割區資料行，然後根據 ADF 計算範圍來分割及複製資料。 |
| 使用自訂查詢載入大量資料。 | **分割區選項**：動態範圍資料分割。<br>**查詢**：`SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`。<br>**分割**區資料行：指定用來套用動態範圍資料分割的資料行。 <br><br>在執行期間，Data Factory 首先會計算指定之資料分割資料行的值範圍，方法是根據相異資料分割資料行值的數目和 ADF 平行複製設定，將資料列平均分配給數個值區中的資料列，然後以篩選每個資料分割的資料分割資料行值範圍來取代，然後傳送 `?AdfHanaDynamicRangePartitionCondition` 至 SAP Hana。<br><br>如果您想要使用多個資料行做為資料分割資料行，您可以將每個資料行的值串連為查詢中的一個資料行，並在 ADF 中將它指定為數據分割資料行，例如 `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition` 。 |

**範例：使用資料表的實體分割區進行查詢**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**範例：使用動態範圍分割進行查詢**

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
| DATE               | Datetime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Single                         |
| SECONDDATE         | Datetime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| timestamp          | Datetime                       |
| VARBINARY          | Byte[]                         |

## <a name="sap-hana-sink"></a>SAP HANA 接收器

目前，SAP Hana 連接器不支援做為接收器，而您可以使用一般 ODBC 連接器搭配 SAP Hana 驅動程式將資料寫入 SAP Hana。 

遵循 [必要條件](#prerequisites) 設定自我裝載的 Integration Runtime，並先安裝 SAP Hana ODBC 驅動程式。 建立 ODBC 連結服務，以連接到您的 SAP Hana 資料存放區（如下列範例所示），然後據以 ODBC 類型建立資料集和複製活動接收器。 深入瞭解 [ODBC connector](connector-odbc.md) 文章。

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
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

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
