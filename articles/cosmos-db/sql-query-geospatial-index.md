---
title: 使用 Azure Cosmos DB 為地理空間資料編制索引
description: 使用 Azure Cosmos DB 為空間資料編制索引
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/03/2020
ms.author: tisande
ms.openlocfilehash: b06a8737c1ceb538417f966a989ccb39069f4d4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85116293"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 為地理空間資料編制索引

我們將 Azure Cosmos DB 的資料庫引擎設計為真正不受架構限制，並提供 JSON 的第一類支援。 Azure Cosmos DB 的寫入優化資料庫引擎原本就瞭解以 GeoJSON 標準表示的空間資料。

簡單來說，大地座標的幾何會投影在 2D 平面上，然後使用 **quadtree**以漸進方式分成格子。 這些格子會根據 **希伯特空間填滿曲線**(Hilbert space filling curve) 內的格子位置對應至 1D，並保留點的位置。 此外，在為位置資料編製索引之後，會經過稱為**鑲嵌式**的處理程序，也就是會將位置上相交的所有格子識別為索引鍵並儲存在 Azure Cosmos DB 索引中。 在查詢時，點和多邊形之類的引數也會經過鑲嵌，以擷取相關的格子 ID 範圍，然後用來從索引擷取資料。

如果您指定包含/* （所有路徑）之空間索引的索引編制原則，則會為在容器內找到的所有資料編制索引，以提供有效率的空間查詢。

> [!NOTE]
> Azure Cosmos DB 支援對點、Linestring、多邊形和 MultiPolygons 編制索引
>
>

## <a name="modifying-geospatial-data-type"></a>修改地理空間資料類型

在您的容器中，**地理空間**設定會指定空間資料的編制索引方式。 為每個容器指定一個**地理空間**設定： geography 或 geometry。

您可以在 Azure 入口網站的 [ **geography** ] 和 [ **geometry** ] 空間類型之間切換。 請務必先建立具有周框方塊的[有效空間幾何索引編制原則](#geometry-data-indexing-examples)，再切換至 geometry 空間類型。

以下說明如何在 Azure 入口網站的**資料總管**中設定**地理空間**設定：

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="設定地理空間設定":::

您也可以 `geospatialConfig` 在 .NET SDK 中修改，以調整**地理空間**設定：

如果未指定，則 `geospatialConfig` 會預設為 geography 資料類型。 當您修改時 `geospatialConfig` ，容器中所有現有的地理空間資料都將會重新建立索引。

以下是藉 `geometry` 由設定 `geospatialConfig` 屬性和新增**boundingBox**，將地理空間資料類型修改為的範例：

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>地理資料索引編制範例

下列 JSON 程式碼片段顯示為**geography**資料類型啟用空間索引的索引編制原則。 它適用于具有 geography 資料類型的空間資料，而且會為在檔中找到的任何 GeoJSON 點、多邊形、MultiPolygon 或 LineString 編制索引，以進行空間查詢。 如果您要使用 Azure 入口網站來修改索引編制原則，可以為索引編制原則指定下列 JSON，以便在您的容器上啟用空間索引編制：

**使用地理空間索引編制的容器索引編制原則 JSON**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> 如果文件中的 GeoJSON 位置值格式不正確或無效，就不會為其編製索引以用於空間查詢。 您可以使用 ST_ISVALID 和 ST_ISVALIDDETAILED 驗證位置值。

您也可以使用 Azure CLI、PowerShell 或任何 SDK 來[修改索引編制原則](how-to-manage-indexing-policy.md)。

## <a name="geometry-data-indexing-examples"></a>幾何資料索引編制範例

使用**geometry**資料類型時，與 geography 資料類型類似，您必須指定要編制索引的相關路徑和類型。 此外，您也必須在 `boundingBox` 索引編制原則中指定，以指出要為該特定路徑編制索引的所需區域。 每個地理空間路徑都需要自己的 `boundingBox` 。

周框方塊包含下列屬性：

- **xmin**：最小的索引 x 座標
- **ymin**：最小索引 y 座標
- **xmax**：索引 x 座標的最大值
- **ymax**：索引 y 座標的最大值

周框方塊是必要的，因為幾何資料佔用的平面可能是無限的。 不過，空間索引需要有限的空間。 對於**geography**資料類型，地球是界限，您不需要設定周框方塊。

建立包含所有（或大部分）資料的周框方塊。 只有在完全位於周框方塊內的物件上計算的作業，才能夠利用空間索引。 使周框方塊大於必要會對查詢效能造成負面影響。

以下是索引編制原則的範例，其會將**幾何**資料與**geospatialConfig**設定為 `geometry` ：

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
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

上述的編制索引原則具有 x 座標的**boundingBox** （-10，10），而 y 座標則為（-20，20）。 具有上述索引編制原則的容器將會為完全在此區域內的所有點、多邊形、MultiPolygons 和 Linestring 編制索引。

> [!NOTE]
> 如果您嘗試將具有**boundingBox**的索引編制原則加入至 `geography` 資料類型的容器中，則會失敗。 在**geospatialConfig** `geometry` 新增**boundingBox**之前，您應該將容器的 geospatialConfig 修改為。 您可以在選取容器的地理空間資料類型之前或之後，新增資料並修改索引編制原則的其餘部分（例如路徑和類型）。

## <a name="next-steps"></a>後續步驟

既然您已了解如何開始使用 Azure Cosmos DB 中的地理空間支援，您可以接著：

* 深入了解 [Azure Cosmos DB 查詢](sql-query-getting-started.md)
* 深入瞭解如何[使用 Azure Cosmos DB 來查詢空間資料](sql-query-geospatial-query.md)
* 深入瞭解[Azure Cosmos DB 中的地理空間和 GeoJSON 位置資料](sql-query-geospatial-intro.md)
