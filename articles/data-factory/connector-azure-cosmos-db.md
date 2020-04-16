---
title: 複製並轉換 Azure 宇宙 DB (SQL API) 中的資料
description: 瞭解如何將資料複製到 Azure Cosmos DB (SQL API) 複製,以及透過使用資料工廠轉換 Azure Cosmos DB (SQL API) 中的數據。
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: f0aa70333454b327a0ca76beef2985062ce56715
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415372"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>使用 Azure 資料工廠複製並轉換 Azure 宇宙資料庫 (SQL API) 中的數據

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-azure-documentdb-connector.md)
> * [目前版本](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，從 Azure Cosmos DB (SQL API) 複製資料或將資料複製到該處，以及如何使用資料流程來轉換 Azure Cosmos DB (SQL API) 中的資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。



>[!NOTE]
>此連接器僅支援Cosmos DB SQL API。 針對 MongoDB API，請參閱[適用於 MongoDB 的 Azure Cosmos DB API 連接器](connector-azure-cosmos-db-mongodb-api.md)。 目前不支援其他 API 類型。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Azure 宇宙資料庫 (SQL API) 連接器:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [對應資料串流](concepts-data-flow-overview.md)
- [尋找活動](control-flow-lookup-activity.md)

對於複製活動,此 Azure 宇宙資料庫 (SQL API) 連接器支援:

- 從 Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) 複製資料或將資料複製到其中。
- 寫入 Azure Cosmos DB 作為 **insert** 或 **upsert**。
- 依原樣匯入和匯出 JSON 文件，或從表格式資料集複製資料或將資料複製到其中。 範例包括 SQL 資料庫和 CSV 檔案。 要按「按「身份」 複製到 JSON 檔或從另一個 Azure Cosmos 資料庫集合複製或從該集合複製,請參閱[匯入及匯出 JSON 文件](#import-and-export-json-documents)。

Data Factory 可與 [Azure Cosmos DB 大量執行程式庫](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)整合，以在寫入至 Azure Cosmos DB 時提供最理想的效能。

> [!TIP]
> [資料移轉影片](https://youtu.be/5-SRNiC_qOU)會引導您完成將資料從 Azure Blob 儲存體複製到 Azure Cosmos DB 的步驟。 此影片也會說明在一般情況下將資料擷取到 Azure Cosmos DB 的效能調整考量。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 Azure Cosmos DB (SQL API) 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure Cosmos DB (SQL API) 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **類型**屬性必須設定為**CosmosDb**。 | 是 |
| connectionString |指定連線到 Azure Cosmos DB 資料庫所需的資訊。<br />**注意**：您必須在連接字串中指定資料庫資訊，如後續範例所示。 <br/> 您也可以將帳戶金鑰放在 Azure Key Vault 並從連接字串中提取 `accountKey` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定此屬性，則會使用預設的 Azure Integration Runtime。 |否 |

**範例**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：在 Azure Key Vault 中儲存帳戶金鑰**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
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

## <a name="dataset-properties"></a>資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。

Azure 宇宙資料庫 (SQL API) 資料集支援以下屬性: 

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集**的類型**屬性必須設定為**CosmosDbSqlApi 集合**。 |是 |
| collectionName |Azure Cosmos DB 文件集合的名稱。 |是 |

如果使用「DocumentDbCollection」類型數據集,則對於複製和查找活動的向後相容性,它仍然支援它,資料流不支援它。 建議您今後使用新模型。

**範例**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 Azure Cosmos DB (SQL API) 來源和接收端所支援的屬性清單。 如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。

### <a name="azure-cosmos-db-sql-api-as-source"></a>將 Azure Cosmos DB (SQL API) 當作來源

若要從 Azure Cosmos DB (SQL API) 複製資料，請將複製活動中的**來源**類型設定為 **DocumentDbCollectionSource**。 

複製活動**來源**部份支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源**的類型**屬性必須設定為**CosmosDbSqlApiSource**。 |是 |
| 查詢 |指定 Azure Cosmos DB 查詢來讀取資料。<br/><br/>範例：<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，則會執行此 SQL 陳述式：`select <columns defined in structure> from mycollection` |
| 預設區域 | 從 Cosmos DB 檢索數據時要連接到的首選區域清單。 | 否 |
| pageSize | 查詢結果每頁的文檔數。 默認值為"-1",這意味著使用服務端動態頁面大小高達 1000。 | 否 |

如果使用「DocumentDbCollectionSource」類型源,則對於向後相容性,它仍然支援它。 建議您使用新的模型,該模型提供了更豐富的功能來複製來自Cosmos DB的數據。

**範例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

從Cosmos DB複製資料時,除非要[按"實際"匯出JSON文檔](#import-and-export-json-documents),否則最佳做法是在複製活動中指定映射。 數據工廠尊重您在活動上指定的映射 - 如果行不包含列的值,則為列值提供 null 值。 如果不指定映射,數據工廠將使用數據中的第一行推斷架構。 如果第一行不包含完整架構,則活動操作的結果將缺少某些列。

### <a name="azure-cosmos-db-sql-api-as-sink"></a>將 Azure Cosmos DB (SQL API) 當作接收端

若要將資料複製到 Azure Cosmos DB (SQL API)，請將複製活動中的**接收**類型設定為 **DocumentDbCollectionSink**。 

複製活動**來源**部份支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器**的類型**屬性必須設定為**CosmosDbSqlApiSink**。 |是 |
| writeBehavior |描述如何將資料寫入至 Azure Cosmos DB。 允許的值：**insert** 和 **upsert**。<br/><br/>如果存在具有相同識別碼的文件，**upsert** 的行為會用來取代文件；否則會插入文字。<br /><br />**注意**：如果未在原始文件中或藉由資料行對應來指定識別碼，則 Data Factory 會自動產生文件的識別碼。 這表示您必須確定，為了讓 **upsert** 如預期般運作，您的文件具有識別碼。 |否<br />(預設值為 **insert**) |
| writeBatchSize | Data Factory 會使用 [Azure Cosmos DB 大量執行程式庫](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)將資料寫入 Cosmos DB 中。 **writeBatchSize 屬性**控制 ADF 向函式庫提供的文件的大小。 您可以嘗試增加 **writeBatchSize** 值來改善效能，而如果您的文件大小很大，請嘗試增加此值 - 請參閱以下祕訣。 |否<br />(預設值為**10,000**) |
| 禁用"指標收集" | 數據工廠收集參數 DB RD 等指標,以便進行複製性能優化和建議。 如果您關注此行為,請指定`true`將其關閉。 | 否 (預設值為 `false`) |

>[!TIP]
>要按本月導入 JSON 文件,請參閱[導入或匯出 JSON 文檔](#import-and-export-json-documents)部分;要從表格元件資料複製,請參閱[從關聯資料庫移至 Cosmos DB](#migrate-from-relational-database-to-cosmos-db)。

>[!TIP]
>Cosmos DB 會將單一要求的大小限制為 2MB。 公式為要求大小 = 單一文件大小 * 寫入批次大小。 如果您遇到指出「要求大小太大。」**** 的錯誤，請在複製接收組態中**縮小 `writeBatchSize` 值**。

如果使用「DocumentDbCollection Sink」類型源,則對於向後相容性,它仍然支援它。 建議您使用新的模型,該模型提供了更豐富的功能來複製來自Cosmos DB的數據。

**範例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>結構描述對應

要將資料從 Azure Cosmos DB 複製到表表接收器或反向,請參考[架構映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在映射資料流中轉換數據時,可以在Cosmos DB中讀取和寫入集合。 關於詳細資訊,請參考映射資料串流的[來源轉換](data-flow-source.md)與[接收器轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>來源轉換

特定於 Azure Cosmos DB 的設定可在源轉換的 **「源選項**」選項卡中提供。 

**包括系統列:** 如果為```id```true,將包含 、、以及```_ts```來自 CosmosDB 的數據流元數據中。 更新集合時,請務必包含此集合,以便可以獲取現有的行 ID。

**頁面大小:** 查詢結果每頁的文檔數。 默認值為"-1",它使用服務動態頁最多達 1000。

**輸送量:** 為要應用於CosmosDB集合的R,在讀取操作期間每次執行此資料流設置可選值。 最小值為 400。

**預設區域:** 選擇此過程的首選讀取區域。

#### <a name="json-settings"></a>JSON 設定

**單個文件:** 如果 ADF 要將整個檔視為單個 JSON 文件,請選擇此選項。

**未參考的欄名稱:** 如果 JSON 中的列名稱未引用,請選擇此選項。

**有評論:** 如果您的 JSON 文件在數據中具有註釋,請使用此選項。

**單次報價:** 如果文檔中的列和值引用單個引號,則應選擇此選項。

**反斜杠逃脫:** 如果使用斜杠來轉義 JSON 中的字元,請選擇此選項。

### <a name="sink-transformation"></a>水槽變換

特定於 Azure Cosmos DB 的設定可在接收器轉換的 **「設定」** 選項卡中提供。

**更新方法:** 確定資料庫目標上允許的操作。 預設值是僅允許插入。 要更新、更新或刪除行,需要更改行轉換來標記這些操作的行。 對於更新、升級和刪除,必須設置鍵列或列以確定要更改的行。

**收集操作:** 確定在寫入之前是否重新創建目標集合。
* 無:不會對集合執行任何操作。
* 重新建立:集合將被刪除並重新建立

**批次調整大小**:控制每個儲存桶中寫入的行數。 較大的批處理大小可改善壓縮和記憶體優化,但在緩存數據時存在記憶體異常風險。

**分割區鍵:** 輸入表示集合的分區鍵的字串。 範例： ```/movies/title```

**輸送量:** 為要應用於CosmosDB集合的每次執行此資料流的R的編號設置可選值。 最小值為 400。

**寫入輸送量預算:** 表示要分配給批量引入 Spark 作業的 R 的整數。 此數位已出分配給集合的總輸送量。

## <a name="lookup-activity-properties"></a>尋找活動屬性

要瞭解有關屬性的詳細資訊,請檢查[。](control-flow-lookup-activity.md)

## <a name="import-and-export-json-documents"></a>匯入及匯出 JSON 文件

您可以使用 Azure Cosmos DB (SQL API) 連接器輕鬆地：

* 在兩個 Azure Cosmos DB 集合之間依原樣複製文件。
* 將 JSON 文件從各種來源匯入到 Azure Cosmos DB，包括 Azure Blob 儲存體、Azure Data Lake Store 及 Azure Data Factory 支援的其他檔案型存放區。
* 將 JSON 文件從 Azure Cosmos DB 集合匯出至各種檔案型存放區。

若要達成無從驗證結構描述的複製：

* 當您使用複製資料工具時，請選取 [依原樣匯出到 JSON 檔案或 Cosmos DB 集合]**** 選項。
* 使用活動創作時,選擇 JSON 格式,使用相應的檔案存儲源或接收器。

## <a name="migrate-from-relational-database-to-cosmos-db"></a>從關聯資料庫移至宇宙資料庫

從關係資料庫(例如 SQL Server) 遷移到 Azure Cosmos DB 時,複製活動可以輕鬆地將表格數據從源映射到 Cosmos DB 中的拼合 JSON 文檔。 在某些情況下,您可能希望重新設計數據模型,以便根據[Azure Cosmos DB 中的數據建模](../cosmos-db/modeling-data.md)來優化它,例如,通過將所有相關子項嵌入到一個 JSON 文件中,使數據非規範化。 對於此類情況,請參閱[本文](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md),介紹如何使用 Azure 數據工廠複製活動實現它。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
