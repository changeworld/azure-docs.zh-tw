---
title: 使用 Azure Cosmos DB 為地理空間資料編制索引
description: 使用 Azure Cosmos DB 為空間資料編制索引
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566370"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 為地理空間資料編制索引

我們將 Azure Cosmos DB 的資料庫引擎設計為真正不受架構限制，並提供 JSON 的第一類支援。 Azure Cosmos DB 的寫入優化資料庫引擎原本就瞭解以 GeoJSON 標準表示的空間資料。

簡單來說，大地座標的幾何會投影在 2D 平面上，然後使用 **quadtree**以漸進方式分成格子。 這些格子會根據 **希伯特空間填滿曲線**(Hilbert space filling curve) 內的格子位置對應至 1D，並保留點的位置。 此外，在為位置資料編製索引之後，會經過稱為**鑲嵌式**的處理程序，也就是會將位置上相交的所有格子識別為索引鍵並儲存在 Azure Cosmos DB 索引中。 在查詢時，點和多邊形之類的引數也會經過鑲嵌，以擷取相關的格子 ID 範圍，然後用來從索引擷取資料。

如果您指定包含/* （所有路徑）之空間索引的索引編制原則，則會為在容器內找到的所有資料編制索引，以提供有效率的空間查詢。

> [!NOTE]
> Azure Cosmos DB 支援對點、Linestring、多邊形和 MultiPolygons 編制索引
>
>

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
>
>
>

您也可以使用 Azure CLI、PowerShell 或任何 SDK 來[修改索引編制原則](how-to-manage-indexing-policy.md)。

## <a name="next-steps"></a>後續步驟

既然您已了解如何開始使用 Azure Cosmos DB 中的地理空間支援，您可以接著：

* 深入了解 [Azure Cosmos DB 查詢](sql-query-getting-started.md)
* 深入瞭解如何[使用 Azure Cosmos DB 來查詢空間資料](sql-query-geospatial-query.md)
* 深入瞭解[Azure Cosmos DB 中的地理空間和 GeoJSON 位置資料](sql-query-geospatial-intro.md)