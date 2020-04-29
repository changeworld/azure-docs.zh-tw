---
title: 在 Azure Cosmos DB 中複製和轉換資料（SQL API）
description: 瞭解如何在 Azure Cosmos DB （SQL API）中複製資料，以及如何使用 Data Factory 來轉換 Azure Cosmos DB （SQL API）中的資料。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415372"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>使用 Azure Data Factory 複製和轉換 Azure Cosmos DB （SQL API）中的資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-azure-documentdb-connector.md)
> * [目前的版本](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，從 Azure Cosmos DB (SQL API) 複製資料或將資料複製到該處，以及如何使用資料流程來轉換 Azure Cosmos DB (SQL API) 中的資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。



>[!NOTE]
>此連接器僅支援 Cosmos DB SQL API。 針對 MongoDB API，請參閱[適用於 MongoDB 的 Azure Cosmos DB API 連接器](connector-azure-cosmos-db-mongodb-api.md)。 目前不支援其他 API 類型。

## <a name="supported-capabilities"></a>支援的功能

此 Azure Cosmos DB （SQL API）連接器支援下列活動：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

針對複製活動，此 Azure Cosmos DB （SQL API）連接器支援：

- 從 Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) 複製資料或將資料複製到其中。
- 寫入 Azure Cosmos DB 作為 **insert** 或 **upsert**。
- 依原樣匯入和匯出 JSON 文件，或從表格式資料集複製資料或將資料複製到其中。 範例包括 SQL 資料庫和 CSV 檔案。 若要將檔依來源複製到 JSON 檔案或另一個 Azure Cosmos DB 集合，請參閱匯[入和匯出 json](#import-and-export-json-documents)檔。

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
| type | **Type**屬性必須設定為**CosmosDb**。 | 是 |
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

Azure Cosmos DB （SQL API）資料集支援下列屬性： 

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的**類型**屬性必須設定為**CosmosDbSqlApiCollection**。 |是 |
| collectionName |Azure Cosmos DB 文件集合的名稱。 |是 |

如果您使用 "DocumentDbCollection" 類型資料集，它仍受到支援，可用於複製和查閱活動的回溯相容性，不支援資料流程。 建議您繼續使用新模型。

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

複製活動的 [**來源**] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的**類型**屬性必須設定為**CosmosDbSqlApiSource**。 |是 |
| 查詢 |指定 Azure Cosmos DB 查詢來讀取資料。<br/><br/>範例：<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，則會執行此 SQL 陳述式：`select <columns defined in structure> from mycollection` |
| preferredRegions | 從 Cosmos DB 抓取資料時，所要連接的慣用區域清單。 | 否 |
| pageSize | 查詢結果每頁的檔數目。 預設值為 "-1"，表示使用服務端動態頁面大小高達1000。 | 否 |

如果您使用 "DocumentDbCollectionSource" 類型來源，則仍會針對回溯相容性提供支援。 建議使用新的模型，以提供更豐富的功能，從 Cosmos DB 複製資料。

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

從 Cosmos DB 複製資料時，除非您想要依自己的[方式匯出 JSON 檔](#import-and-export-json-documents)，否則最佳做法是在複製活動中指定對應。 Data Factory 接受您在活動上指定的對應-如果資料列不包含資料行的值，則會為數據行值提供 null 值。 如果您未指定對應，Data Factory 會使用資料中的第一列來推斷架構。 如果第一個資料列未包含完整的架構，則活動作業的結果中將會遺失某些資料行。

### <a name="azure-cosmos-db-sql-api-as-sink"></a>將 Azure Cosmos DB (SQL API) 當作接收端

若要將資料複製到 Azure Cosmos DB (SQL API)，請將複製活動中的**接收**類型設定為 **DocumentDbCollectionSink**。 

複製活動的 [**來源**] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的**type**屬性必須設定為**CosmosDbSqlApiSink**。 |是 |
| writeBehavior |描述如何將資料寫入至 Azure Cosmos DB。 允許的值：**insert** 和 **upsert**。<br/><br/>如果存在具有相同識別碼的文件，**upsert** 的行為會用來取代文件；否則會插入文字。<br /><br />**注意**：如果未在原始文件中或藉由資料行對應來指定識別碼，則 Data Factory 會自動產生文件的識別碼。 這表示您必須確定，為了讓 **upsert** 如預期般運作，您的文件具有識別碼。 |否<br />(預設值為 **insert**) |
| writeBatchSize | Data Factory 會使用 [Azure Cosmos DB 大量執行程式庫](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)將資料寫入 Cosmos DB 中。 **WriteBatchSize**屬性會控制 ADF 提供給程式庫的檔案大小。 您可以嘗試增加 **writeBatchSize** 值來改善效能，而如果您的文件大小很大，請嘗試增加此值 - 請參閱以下祕訣。 |否<br />（預設值為**10000**） |
| disableMetricsCollection | Data Factory 會收集諸如 Cosmos DB ru 的計量，以取得複製效能優化和建議。 如果您擔心此行為，請指定`true`將它關閉。 | 否 (預設值為 `false`) |

>[!TIP]
>若要依預設匯入 JSON 檔，請參閱匯[入或匯出 json 檔](#import-and-export-json-documents)一節。若要從表格式資料複製，請參閱[從關係資料庫移轉至 Cosmos DB](#migrate-from-relational-database-to-cosmos-db)。

>[!TIP]
>Cosmos DB 會將單一要求的大小限制為 2MB。 公式為要求大小 = 單一文件大小 * 寫入批次大小。 如果您遇到指出「要求大小太大。」**** 的錯誤，請在複製接收組態中**縮小 `writeBatchSize` 值**。

如果您使用 "DocumentDbCollectionSink" 類型來源，則仍會針對回溯相容性提供支援。 建議使用新的模型，以提供更豐富的功能，從 Cosmos DB 複製資料。

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

若要將資料從 Azure Cosmos DB 複製到表格式接收或反轉，請參閱[架構對應](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中轉換資料時，您可以在 Cosmos DB 中讀取和寫入集合。 如需詳細資訊，請參閱對應資料流程中的[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)。

### <a name="source-transformation"></a>來源轉換

[來源] 轉換的 [**來源選項**] 索引標籤中提供 Azure Cosmos DB 特定的設定。 

**包含系統資料行：** 如果為 true ```id```， ```_ts```、和其他系統資料行將會包含在 CosmosDB 的資料流程中繼資料中。 更新集合時，請務必包含此資訊，以便您可以抓取現有的資料列識別碼。

**頁面大小：** 查詢結果每頁的檔數目。 預設值為 "-1"，使用最多1000的服務動態頁面。

**輸送量：** 在讀取作業期間，針對此資料流程的每次執行，設定您想要套用至 CosmosDB 集合的 ru 數目的選擇性值。 最小值為400。

**慣用區域：** 選擇此進程的慣用讀取區域。

#### <a name="json-settings"></a>JSON 設定

**單一檔：** 如果 ADF 將整個檔案視為單一 JSON 檔，請選取此選項。

不具引號的資料**行名稱：** 如果 JSON 中的資料行名稱未加上引號，請選取此選項。

**具有批註：** 如果您的 JSON 檔在資料中有批註，請使用此選項。

以**單引號括住：** 如果您檔中的資料行和值以單引號括住，就應該選取此選項。

**反斜線已轉義：** 如果使用反斜線來轉義 JSON 中的字元，請選擇此選項。

### <a name="sink-transformation"></a>接收轉換

[接收] 轉換的 [**設定**] 索引標籤中提供 Azure Cosmos DB 特定的設定。

**更新方法：** 決定您的資料庫目的地所允許的作業。 預設值是只允許插入。 若要更新、upsert 或刪除資料列，必須使用 alter-row 轉換來標記這些動作的資料列。 對於更新、更新插入和刪除，必須設定索引鍵資料行，以決定要改變哪一個資料列。

**集合動作：** 判斷是否要在寫入之前重新建立目的地集合。
* 無：不會對集合執行任何動作。
* 重新建立：將會卸載並重新建立集合

**批次大小**：控制要在每個值區中寫入的資料列數目。 較大的批次大小會改善壓縮和記憶體優化，但會在快取資料時降低記憶體例外狀況的風險。

**分割區索引鍵：** 輸入代表集合之分割區索引鍵的字串。 範例：```/movies/title```

**輸送量：** 針對每次執行此資料流程的情況，設定您想要套用至 CosmosDB 集合的 ru 數目的選擇性值。 最小值為400。

**寫入輸送量預算：** 整數，代表您想要配置給大量內嵌 Spark 作業的 ru 數目。 此數位超出配置給集合的總輸送量。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="import-and-export-json-documents"></a>匯入和匯出 JSON 檔

您可以使用 Azure Cosmos DB (SQL API) 連接器輕鬆地：

* 在兩個 Azure Cosmos DB 集合之間依原樣複製文件。
* 將 JSON 文件從各種來源匯入到 Azure Cosmos DB，包括 Azure Blob 儲存體、Azure Data Lake Store 及 Azure Data Factory 支援的其他檔案型存放區。
* 將 JSON 文件從 Azure Cosmos DB 集合匯出至各種檔案型存放區。

若要達成無從驗證結構描述的複製：

* 當您使用複製資料工具時，請選取 [依原樣匯出到 JSON 檔案或 Cosmos DB 集合]**** 選項。
* 當您使用活動撰寫時，請選擇 JSON 格式與來源或接收的對應檔案存放區。

## <a name="migrate-from-relational-database-to-cosmos-db"></a>從關係資料庫移轉至 Cosmos DB

從關係資料庫（例如 SQL Server 至 Azure Cosmos DB）進行遷移時，複製活動可以輕鬆地從來源對應表格式資料，以在 Cosmos DB 中簡維 JSON 檔。 在某些情況下，您可能會想要重新設計資料模型，根據[Azure Cosmos DB 中的資料](../cosmos-db/modeling-data.md)模型化來針對 NoSQL 使用案例進行優化，例如，藉由內嵌一個 JSON 檔中所有相關的子專案來反正規化資料。 針對這種情況，請參閱[這篇文章](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md)，並逐步解說如何使用 Azure Data Factory 複製活動來達成此目的。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
