---
title: 使用 Azure 宇宙資料庫索引地理空間資料
description: 使用 Azure 宇宙資料庫索引空間資料
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137898"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>使用 Azure 宇宙資料庫索引地理空間資料

我們設計了 Azure Cosmos DB 的資料庫引擎，使之真正與架構無關，並為 JSON 提供一流支援。 Azure Cosmos DB 的寫入優化資料庫引擎本機可理解 GeoJSON 標準中表示的空間資料。

簡單來說，大地座標的幾何會投影在 2D 平面上，然後使用 **quadtree**以漸進方式分成格子。 這些格子會根據 **希伯特空間填滿曲線**(Hilbert space filling curve) 內的格子位置對應至 1D，並保留點的位置。 此外，在為位置資料編製索引之後，會經過稱為**鑲嵌式**的處理程序，也就是會將位置上相交的所有格子識別為索引鍵並儲存在 Azure Cosmos DB 索引中。 在查詢時，點和多邊形之類的引數也會經過鑲嵌，以擷取相關的格子 ID 範圍，然後用來從索引擷取資料。

如果指定包含 /*（所有路徑）的空間索引策略，則容器內找到的所有資料都為有效空間查詢編制索引。

> [!NOTE]
> Azure Cosmos DB 支援點、線弦、多邊形和多多邊形的索引
>
>

## <a name="modifying-geospatial-data-type"></a>修改地理空間資料類型

在容器中，`geospatialConfig`指定地理空間資料的索引方式。 應指定每個容器`geospatialConfig`的一個：地理或幾何。 如果未指定，將`geospatialConfig`預設為地理資料類型。 修改 時，`geospatialConfig`將重新編制容器中的所有現有地理空間資料。

> [!NOTE]
> Azure Cosmos DB 目前僅支援對 .NET SDK 中的地理空間配置進行修改，該版本為 3.6 及以上版本。
>

下面是一個示例，用於通過設置`geometry``geospatialConfig`屬性並添加**邊界Box**來修改地理空間資料類型：

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

## <a name="geography-data-indexing-examples"></a>地理資料索引示例

以下 JSON 程式碼片段顯示一個索引策略，該策略為**地理**資料類型啟用了空間索引。 它對於具有地理資料類型的空間資料有效，並將索引在文檔中找到的任何 GeoJSON 點、多邊形、多面或 LineString 以進行空間查詢。 如果使用 Azure 門戶修改索引策略，則可以為索引策略指定以下 JSON 以在容器上啟用空間索引：

**具有地理空間索引的容器索引策略 JSON**

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

您還可以使用 Azure CLI、PowerShell 或任何 SDK[修改索引策略](how-to-manage-indexing-policy.md)。

## <a name="geometry-data-indexing-examples"></a>幾何資料索引示例

對於與地理資料類型類似的**幾何**資料類型，必須指定要索引的相關路徑和類型。 此外，還必須在索引策略中指定`boundingBox`， 以指示要為該特定路徑編制索引的所需區域。 每個地理空間路徑都需要它自己的`boundingBox`。

邊界框由以下屬性組成：

- **xmin**： 最小索引 x 座標
- **ymin**： 最小索引 y 座標
- **xmax**： 最大索引 x 座標
- **ymax**： 最大索引 y 座標

需要邊界框，因為幾何資料佔用的平面可以是無限的。 但是，空間索引需要有限的空間。 對於**地理**資料類型，地球是邊界，您不需要設置邊界框。

應創建一個包含所有（或大部分）資料的邊界框。 只有完全在邊界框內的物件上計算的操作才能利用空間索引。 不應使邊界框明顯大於必要值，因為這會對查詢性能產生負面影響。

下面是一個索引策略的示例，該策略將**幾何資料**索引，地理**空間配置**設置為`geometry`：

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

上述索引策略的**x**座標為 （-10， 10） 和 y 座標（-20， 20）。 具有上述索引策略的容器將索引完全位於此區域內的所有點、多邊形、多多邊形和線字串。

> [!NOTE]
> 如果嘗試將帶有**邊界框**的索引策略添加到具有`geography`資料類型的容器中，它將失敗。 在添加**邊界框**`geometry`之前，應修改容器的**地理空間配置**。 在選擇容器的地理空間資料類型之前或之後，可以添加資料並修改索引策略的其餘部分（如路徑和類型）。

## <a name="next-steps"></a>後續步驟

既然您已了解如何開始使用 Azure Cosmos DB 中的地理空間支援，您可以接著：

* 深入了解 [Azure Cosmos DB 查詢](sql-query-getting-started.md)
* 瞭解有關使用[Azure Cosmos DB 查詢空間資料](sql-query-geospatial-query.md)
* 瞭解有關[Azure 宇宙 DB 中的地理空間和地理JSON 位置資料](sql-query-geospatial-intro.md)