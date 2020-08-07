---
title: 管理 Azure Cosmos DB 中的索引編製原則
description: 瞭解如何管理索引編制原則、包含或排除索引中的屬性、如何使用不同的 Azure Cosmos DB Sdk 定義索引編制
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/04/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: de9cdaf1dcc398c5db55de78643a19179f6bfdbc
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876473"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>管理 Azure Cosmos DB 中的索引編製原則

Azure Cosmos DB 會遵循針對每個容器所定義的[索引編製原則](index-policy.md)來為資料編製索引。 新建立的容器所套用的預設索引編製原則，會對任何字串或數字強制執行範圍索引。 您可使用自己的自訂索引編製原則來覆寫此原則。

## <a name="indexing-policy-examples"></a>索引編製原則範例

以下是一些以[JSON 格式](index-policy.md#include-exclude-paths)顯示的索引編制原則範例，也就是它們在 Azure 入口網站上的公開方式。 透過 Azure CLI 或任何 SDK 也可以設定相同的參數。

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>可選擇性地排除一些屬性路徑的退出原則

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

此編制索引原則等同于以下的設定，其會以手動方式將 ```kind``` 、 ```dataType``` 和設 ```precision``` 為其預設值。 這些屬性不再需要明確設定，而且您可以完全從索引編制原則中省略它們 (如上述範例) 所示。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>可選擇性地納入一些屬性路徑的加入原則

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

此編制索引原則等同于以下的設定，其會以手動方式將 ```kind``` 、 ```dataType``` 和設 ```precision``` 為其預設值。 這些屬性不再需要明確設定，而且您可以完全從索引編制原則中省略它們 (如上述範例) 所示。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE]
> 一般會建議您使用**退出**索引編製原則來讓 Azure Cosmos DB 主動地對可能會新增至模型的新屬性編製索引。

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>僅對特定屬性路徑使用空間索引

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>複合式索引編製原則範例

除了包含或排除個別屬性的路徑以外，您也可以指定複合式索引。 如果您要為多個屬性執行具有 `ORDER BY` 子句的查詢，則必須要有這些屬性的[複合式索引](index-policy.md#composite-indexes)。 此外，對於具有篩選的查詢，以及在不同屬性上具有 ORDER BY 子句的查詢，複合索引將有效能上的好處。

> [!NOTE]
> 複合路徑具有隱含的 `/?` ，因為只有該路徑上的純量值會編制索引。 `/*`複合路徑中不支援萬用字元。 您不應該 `/?` `/*` 在複合路徑中指定或。

### <a name="composite-index-defined-for-name-asc-age-desc"></a>針對 (name asc, age desc) 定義的複合式索引：

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

查詢 #1 和查詢 #2 需要上述的複合索引（名稱和年齡）：

查詢 1：

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

查詢 2：

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

這個複合索引將受益于查詢 #3 並查詢 #4 並將篩選優化：

查詢 #3：

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

查詢 #4：

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>為 (名稱 ASC、年齡 ASC) 和 (名稱 ASC、age DESC) 定義的複合索引：

您可以在相同的索引編製原則內定義多個不同的複合式索引。

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>為 (名稱 ASC、年齡 ASC) 定義的複合索引：

指定順序是選擇性動作。 若未指定，將採用遞增順序。

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>排除所有屬性路徑但讓索引編製保持作用狀態

此原則可用於[存留時間 (TTL) 功能](time-to-live.md)有作用但不需要 (即可使用 Azure Cosmos DB 作為純粹的索引鍵-值存放區) 次要索引的情況。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>無索引編製

此原則將會關閉索引編制。 如果 `indexingMode` 設定為 `none` ，您就無法在容器上設定 TTL。

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>正在更新索引編制原則

在 Azure Cosmos DB 中，您可以使用下列任何方法來更新索引編制原則：

- 從 Azure 入口網站
- 使用 Azure CLI
- 使用 PowerShell
- 使用其中一個 SDK

[更新索引編製原則](index-policy.md#modifying-the-indexing-policy)將會觸發索引的轉換。 您也可以從 SDK 追蹤此轉換的進度。

> [!NOTE]
> 更新索引編制原則時，寫入 Azure Cosmos DB 將會中斷。 深入瞭解[索引轉換](indexing-policy.md#modifying-the-indexing-policy)

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

Azure Cosmos 容器會將其索引編製原則儲存為 JSON 文件，並可從 Azure 入口網站直接進行編輯。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 建立新的 Azure Cosmos 帳戶，或選取現有帳戶。

1. 開啟 [資料總管]**** 窗格，然後選取您要處理的容器。

1. 按一下 [規模與設定]****。

1. 修改索引編製原則 JSON 文件 (請參閱[下面](#indexing-policy-examples)範例)

1. 當您完成時，按一下 [儲存]****。

:::image type="content" source="./media/how-to-manage-indexing-policy/indexing-policy-portal.png" alt-text="使用 Azure 入口網站管理編制索引":::

## <a name="use-the-azure-cli"></a>使用 Azure CLI

若要建立具有自訂索引編制原則的容器，請參閱[使用 CLI 建立具有自訂索引原則的容器](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>使用 PowerShell

若要建立具有自訂索引編制原則的容器，請參閱[使用 Powershell 建立具有自訂索引原則的容器](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a>使用 .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

`DocumentCollection` [.Net SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)中的物件 `IndexingPolicy` 會公開屬性，可讓您變更 `IndexingMode` 和新增或移除 `IncludedPaths` 和 `ExcludedPaths` 。

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

若要追蹤索引的轉換進度，請傳遞 `RequestOptions` 物件並將其 `PopulateQuotaInfo` 屬性設定為 `true`。

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

`ContainerProperties` [.Net SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)中的物件 (參閱[此快速入門](create-sql-api-dotnet.md)中有關其使用方式) 會公開可 `IndexingPolicy` 讓您變更 `IndexingMode` 和新增或移除 `IncludedPaths` 和的屬性 `ExcludedPaths` 。

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

若要追蹤索引轉換進度，請傳遞 `RequestOptions` 將 `PopulateQuotaInfo` 屬性設定為的物件 `true` ，然後從 `x-ms-documentdb-collection-index-transformation-progress` 回應標頭取出值。

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

在建立新容器時定義自訂索引編制原則時，SDK V3's Fluent API 可讓您以簡潔且有效率的方式撰寫此定義：

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>使用 Java SDK

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 中的 `DocumentCollection` 物件 (請參閱關於其使用方式的[這個快速入門](create-sql-api-java.md)) 會公開 `getIndexingPolicy()` 和 `setIndexingPolicy()` 方法。 這些方法所管理的 `IndexingPolicy` 物件可讓您變更索引編製模式，以及新增或移除已納入和排除的路徑。

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

若要在容器上追蹤索引轉換進度，請傳遞會要求填入配額資訊的 `RequestOptions` 物件，然後從 `x-ms-documentdb-collection-index-transformation-progress` 回應標頭中擷取值。

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) 中的 `ContainerDefinition` 介面 (請參閱關於其使用方式的[這個快速入門](create-sql-api-nodejs.md)) 會公開 `indexingPolicy` 屬性，以供您變更 `indexingMode` 以及新增或移除 `includedPaths` 和 `excludedPaths`。

取得容器的詳細資料

```javascript
const containerResponse = await client.database('database').container('container').read();
```

將索引編制模式設定為一致

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

新增內含的路徑，包括空間索引

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

新增排除的路徑

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

使用變更來更新容器

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

若要在容器上追蹤索引轉換進度，請傳遞會將 `populateQuotaInfo` 屬性設定為 `true` 的 `RequestOptions` 物件，然後從 `x-ms-documentdb-collection-index-transformation-progress` 回應標頭中擷取值。

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>使用 Python SDK

# <a name="python-sdk-v3"></a>[Python SDK V3](#tab/pythonv3)

使用[PYTHON SDK V3](https://pypi.org/project/azure-cosmos/)時 (請參閱[本快速入門](create-sql-api-python.md)中有關其使用方式) ，容器設定會當做字典來管理。 您可以從這個字典存取索引編製原則和其所有屬性。

取得容器的詳細資料

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

將索引編制模式設定為一致

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

定義包含路徑和空間索引的索引編制原則

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

定義具有排除路徑的索引編制原則

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

加入複合索引

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

使用變更來更新容器

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[Python SDK V4](#tab/pythonv4)

使用[PYTHON SDK V4](https://pypi.org/project/azure-cosmos/)時，容器設定會當做字典來管理。 您可以從這個字典存取索引編製原則和其所有屬性。

取得容器的詳細資料

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

將索引編制模式設定為一致

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

定義包含路徑和空間索引的索引編制原則

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

定義具有排除路徑的索引編制原則

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

加入複合索引

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

使用變更來更新容器

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```
---

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引概觀](index-overview.md)
- [編製索引原則](index-policy.md)
