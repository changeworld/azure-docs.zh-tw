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
ms.openlocfilehash: 945ef895304a151ea7e0ef5b94ed0b42757743ad
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890622"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP HANA 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-sap-hana-connector.md)
> * [目前的版本](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 SAP HANA 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

>[!TIP]
>若要瞭解 ADF 對於 SAP 資料整合案例的整體支援，請參閱[使用 Azure Data Factory 白皮書的 SAP 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，其中包含詳細的簡介、comparsion 和指引。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 SAP Hana 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 SAP HANA 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 SAP HANA 連接器支援：

- 從任何版本的 SAP HANA 資料庫複製資料。
- 從**HANA 資訊模型**（例如分析和計算視圖）和資料**列/資料行資料表**複製資料。
- 使用 **Basic** (基本) 或 **Windows** 驗證來複製資料。
- 從 SAP Hana 來源進行平行複製。 如需詳細資訊，請參閱[SAP Hana 的平行複製](#parallel-copy-from-sap-hana)一節。

> [!TIP]
> 若要將資料複製**到** SAP HANA 資料存放區，請使用一般 ODBC 連接器。 請參閱[SAP Hana 接收](#sap-hana-sink)一節中的詳細資料。 請注意，SAP HANA 連接器和 ODBC 連接器的已連接服務具有不同的類型，因此無法重複使用。

## <a name="prerequisites"></a>Prerequisites

若要使用這個 SAP HANA 接收器，您必須：

- 設定一個「自我裝載 Integration Runtime」。 如需詳細資訊，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md)一文。
- 在 Integration Runtime 電腦上安裝 SAP HANA ODBC 驅動程式。 您可以從 [SAP 軟體下載中心](https://support.sap.com/swdc)下載 SAP Hana ODBC 驅動程式。 使用關鍵字 **SAP HANA CLIENT for Windows** 搜尋。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP HANA 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SAP HANA 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**SapHana** | 是 |
| connectionString | 指定使用**基本驗證**或**Windows 驗證**連接到 SAP Hana 所需的資訊。 請參考下列範例。<br>在 [連接字串] 中，伺服器/埠是強制的（預設通訊埠是30015），而使用者名稱和密碼則是在使用基本驗證時的必要項。 如需其他 advanced 設定，請參閱[SAP HANA ODBC 連接屬性](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取密碼設定。 如需詳細資訊，請參閱[將認證儲存在 Azure Key Vault](store-credentials-in-key-vault.md)一文。 | 是 |
| userName | 使用 Windows 驗證時，請指定使用者名稱。 範例：`user@domain.com` | 否 |
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

如果您使用的是具有下列承載的 SAP Hana 連結服務，它仍會受到支援，但建議您繼續使用新的服務。

**範例：**

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

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **SapHanaTable** | 是 |
| 結構描述 | SAP Hana 資料庫中的架構名稱。 | 否 (如果已指定活動來源中的「查詢」) |
| 資料表 | SAP Hana 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例：**

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

如果您使用`RelationalTable`的是具類型的資料集，則仍會受到支援，但建議您在未來使用新的 dataset。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SAP HANA 來源所支援的屬性清單。

### <a name="sap-hana-as-source"></a>SAP HANA 作為來源

>[!TIP]
>若要使用資料分割有效率地內嵌 SAP Hana 的資料，請從 SAP Hana 一節的[平行複製](#parallel-copy-from-sap-hana)深入瞭解。

若要從 SAP Hana 複製資料，複製活動的 [**來源**] 區段中支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為： **SapHanaSource** | 是 |
| 查詢 | 指定 SQL 查詢從 SAP HANA 執行個體讀取資料。 | 是 |
| partitionOptions | 指定用來從 SAP Hana 內嵌資料的資料分割選項。 若要深入瞭解，請從[SAP Hana 的平行複製](#parallel-copy-from-sap-hana)一節。<br>允許值為： **None** （預設值）、 **PhysicalPartitionsOfTable**、 **SapHanaDynamicRange**。 若要深入瞭解，請從[SAP Hana 的平行複製](#parallel-copy-from-sap-hana)一節。 `PhysicalPartitionsOfTable`只能在從資料表複製資料時使用，但不能用於查詢。 <br>當分割區選項已啟用（也就是不`None`是）時，要從 SAP Hana 並行載入資料的平行處理原則程度， [`parallelCopies`](copy-activity-performance-features.md#parallel-copy)是由複製活動上的設定所控制。 | False |
| partitionSettings | 指定資料分割的設定群組。<br>當資料分割選項為`SapHanaDynamicRange`時套用。 | False |
| partitionColumnName | 指定資料分割將用於平行複製的來源資料行名稱。 如果未指定，則會自動偵測資料表的索引或主鍵，並當做資料分割資料行使用。<br>適用于資料分割選項為 `SapHanaDynamicRange`時。 如果您使用查詢來抓取來源資料，請在 WHERE `?AdfHanaDynamicRangePartitionCondition`子句中掛上。 請參閱[從 SAP Hana 進行平行複製](#parallel-copy-from-sap-hana)一節中的範例。 | 使用`SapHanaDynamicRange`資料分割時為是。 |
| packetSize | 指定將資料分割成多個區塊的網路封包大小（以 Kb 為單位）。 如果您要複製大量資料，增加封包大小可能會在大部分情況下從 SAP Hana 增加讀取速度。 調整封包大小時，建議執行效能測試。 | 不需要。<br>預設值為2048（2MB）。 |

**範例：**

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

如果您使用`RelationalSource`的是具類型的複製來源，則仍會受到支援，但建議您在未來使用新的版本。

## <a name="parallel-copy-from-sap-hana"></a>從 SAP Hana 的平行複製

Data Factory SAP Hana 連接器會提供內建的資料分割，以平行方式從 SAP Hana 複製資料。 您可以在複製活動的**源**資料表上找到資料分割選項。

![資料分割選項的螢幕擷取畫面](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

當您啟用資料分割複本時，Data Factory 會對您的 SAP Hana 來源執行平行查詢，以依分割區來取得資料。 平行程度是由複製活動上[`parallelCopies`](copy-activity-performance-features.md#parallel-copy)的設定所控制。 例如，如果您將設定`parallelCopies`為四，Data Factory 會根據您指定的資料分割選項和設定，同時產生並執行四個查詢，而且每個查詢都會從您的 SAP Hana 取得部分資料。

建議您啟用具有資料分割的平行複製，特別是當您從 SAP Hana 內嵌大量資料時。 以下是適用于不同案例的建議設定。 將資料複製到以檔案為基礎的資料存放區時，建議您以多個檔案的形式寫入資料夾（僅指定資料夾名稱），在此情況下，效能會比寫入單一檔案更好。

| 狀況                                           | 建議的設定                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| 來自大型資料表的完整載入。                        | 資料**分割選項**：資料表的實體分割區。 <br><br/>在執行期間，Data Factory 會自動偵測指定 SAP Hana 資料表的實體資料分割類型，並選擇對應的資料分割策略：<br>- **範圍分割**：取得針對資料表定義的分割區資料行和分割區範圍，然後依範圍複製資料。 <br>- **雜湊資料分割**：使用雜湊資料分割索引鍵做為資料分割資料行，然後根據 ADF 計算的範圍分割和複製資料。 <br>- **迴圈**配置資源分割或**沒有分割**區：使用主鍵做為資料分割資料行，然後根據 ADF 計算的範圍分割和複製資料。 |
| 使用自訂查詢載入大量資料。 | 資料**分割選項**：動態範圍分割區。<br>**查詢**： `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`。<br>資料**分割資料行**：指定用來套用動態範圍分割區的資料行。 <br><br>在執行期間，Data Factory 會先計算指定之分割區資料行的值範圍，方法是根據相異分割區的資料行值和 ADF 平行複製設定的數目，將資料列平均分散在`?AdfHanaDynamicRangePartitionCondition`數個 bucket 中，然後以篩選每個資料分割的資料分割資料行值範圍來取代，然後傳送至 SAP Hana。<br><br>如果您想要使用多個資料行做為資料分割資料行，您可以串連每個資料行的值做為查詢中的一個資料行，並`SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`將它指定為 ADF 中的分割區資料行，例如。 |

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
| 日期               | Datetime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| real               | Single                         |
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

### <a name="sap-hana-sink"></a>SAP HANA 接收器

目前，SAP Hana 連接器不支援做為接收器，而您可以搭配 SAP Hana 驅動程式使用一般 ODBC 連接器，將資料寫入 SAP Hana。 

請遵循設定自我裝載 Integration Runtime 的[必要條件](#prerequisites)，並先安裝 SAP Hana ODBC 驅動程式。 建立 ODBC 連結服務，以連接到您的 SAP Hana 資料存放區（如下列範例所示），然後據以 ODBC 類型建立資料集和複製活動接收器。 若要深入瞭解，請參閱[ODBC connector](connector-odbc.md)文章。

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

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
