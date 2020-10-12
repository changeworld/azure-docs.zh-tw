---
title: 使用 Azure Cosmos DB 為地理空間資料編制索引
description: 使用 Azure Cosmos DB 索引空間資料
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/03/2020
ms.author: tisande
ms.openlocfilehash: 546b664c74980b3522fefed82c00eec414641eaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326621"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 為地理空間資料編制索引

我們設計 Azure Cosmos DB 的資料庫引擎，使其成為真正的架構，並提供 JSON 的第一種類別支援。 Azure Cosmos DB 的寫入優化資料庫引擎以原生方式瞭解 GeoJSON 標準中表示的空間資料。

簡單來說，大地座標的幾何會投影在 2D 平面上，然後使用 **quadtree**以漸進方式分成格子。 這些格子會根據 **希伯特空間填滿曲線**(Hilbert space filling curve) 內的格子位置對應至 1D，並保留點的位置。 此外，在為位置資料編製索引之後，會經過稱為**鑲嵌式**的處理程序，也就是會將位置上相交的所有格子識別為索引鍵並儲存在 Azure Cosmos DB 索引中。 在查詢時，點和多邊形之類的引數也會經過鑲嵌，以擷取相關的格子 ID 範圍，然後用來從索引擷取資料。

如果您指定的索引編制原則包含/* (所有路徑) ，則在容器中找到的所有資料都會編制索引以進行有效率的空間查詢。

> [!NOTE]
> Azure Cosmos DB 支援為點、Linestring、多邊形和 MultiPolygons 編制索引
>
>

## <a name="modifying-geospatial-data-type"></a>修改地理空間資料類型

在您的容器中， **地理空間** 設定會指定空間資料的索引方式。 為每個容器指定一個 **地理空間** 設定： geography 或 geometry。

您可以在 Azure 入口網站中的 **geography** 和 **geometry** 空間類型之間切換。 在切換到幾何空間類型之前，請務必使用周框方塊來建立 [有效的空間幾何索引編制原則](#geometry-data-indexing-examples) 。

以下是在 Azure 入口網站內**資料總管**設定**地理空間**設定的方式：

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="設定地理空間設定":::

您也可以 `geospatialConfig` 在 .NET SDK 中修改，以調整 **地理空間**設定：

如果未指定，則 `geospatialConfig` 會預設為 geography 資料類型。 當您修改時 `geospatialConfig` ，容器中所有現有的地理空間資料都會重新建立索引。

以下是藉 `geometry` 由設定 `geospatialConfig` 屬性和新增 **boundingBox**來將地理空間資料類型修改為的範例：

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

下列 JSON 程式碼片段顯示針對 **geography** 資料類型啟用空間索引編制的索引編制原則。 它適用于具有 geography 資料類型的空間資料，而且會為在檔中找到的任何 GeoJSON 點、多邊形、MultiPolygon 或 LineString 編制索引，以用於空間查詢。 如果您要使用 Azure 入口網站修改編制索引原則，您可以為索引編制原則指定下列 JSON，以啟用容器的空間索引編制：

**具有地理位置空間索引的容器索引編制原則 JSON**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
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
    "excludedPaths": []
}
```

> [!NOTE]
> 如果文件中的 GeoJSON 位置值格式不正確或無效，就不會為其編製索引以用於空間查詢。 您可以使用 ST_ISVALID 和 ST_ISVALIDDETAILED 驗證位置值。

您也可以使用 Azure CLI、PowerShell 或任何 SDK 來 [修改編制索引原則](how-to-manage-indexing-policy.md) 。

## <a name="geometry-data-indexing-examples"></a>幾何資料索引編制範例

使用 **geometry** 資料類型（類似于 geography 資料類型）時，您必須指定要編制索引的相關路徑和類型。 此外，您也必須在 `boundingBox` 索引編制原則中指定，以指出要為該特定路徑編制索引的目的地區域。 每個地理空間路徑都需要自己的路徑 `boundingBox` 。

周框方塊是由下列屬性組成：

- **xmin**：索引 x 座標下限
- **ymin**：最小索引的 y 座標
- **xmax**：索引 x 座標上限
- **ymax**：最大索引 y 座標

需要有周框方塊，因為幾何資料佔用的平面可能是無限的。 不過，空間索引需要有限的空間。 針對 **geography** 資料類型，地球是界限，您不需要設定周框方塊。

建立周框方塊，其中包含所有 (或大部分的資料) 。 只有在完全在周框方塊內的物件上計算的作業，才能夠利用空間索引。 將周框方塊設為大於必要，將會對查詢效能造成負面影響。

以下是索引編制原則的索引編制原則，其會將**geospatialConfig**設定為的**幾何**資料編制索引 `geometry` ：

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

上述索引編制原則的 **boundingBox** 為 (-10、10) x 座標和 (-20、20) 用於 y 座標。 具有上述索引編制原則的容器將會為整個區域內的所有點、多邊形、MultiPolygons 和 Linestring 編制索引。

> [!NOTE]
> 如果您嘗試將具有 **boundingBox** 的索引編制原則新增至具有 `geography` 資料類型的容器，它將會失敗。 您應該先修改容器的 **geospatialConfig** ， `geometry` 然後再新增 **boundingBox**。 您可以新增資料並修改索引編制原則的其餘部分 (例如在選取容器的地理空間資料類型之前或之後) 的路徑和類型。

## <a name="next-steps"></a>後續步驟

既然您已了解如何開始使用 Azure Cosmos DB 中的地理空間支援，您可以接著：

* 深入了解 [Azure Cosmos DB 查詢](sql-query-getting-started.md)
* 深入瞭解如何 [使用 Azure Cosmos DB 來查詢空間資料](sql-query-geospatial-query.md)
* 深入瞭解 [Azure Cosmos DB 中的地理空間和 GeoJSON 位置資料](sql-query-geospatial-intro.md)
