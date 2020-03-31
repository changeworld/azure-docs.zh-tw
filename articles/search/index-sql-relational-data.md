---
title: 用於導入和索引的 SQL 關係資料建模
titleSuffix: Azure Cognitive Search
description: 瞭解如何對關係資料建模，將非正常化為平面結果集，以便在 Azure 認知搜索中編制索引和全文檢索搜尋。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790086"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中建模關係 SQL 資料以導入和編制索引

Azure 認知搜索接受平面行集作為[索引管道](search-what-is-an-index.md)的輸入。 如果來源資料源自 SQL Server 關係資料庫中的聯合資料表，本文將介紹如何構造結果集，以及如何在 Azure 認知搜索索引中建模父子關係。

作為一個示例，我們將參考一個基於[演示資料](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)的假設酒店資料庫。 假設資料庫包括包含 50 家酒店的 Hotels$ 表和包含不同類型、價格和設施的客房，共 750 間客房。 表之間存在一對多關聯性。 在我們的方法中，視圖將提供返回 50 行的查詢，每家酒店返回一行，並將關聯的房間詳細資訊嵌入到每行中。

   ![酒店資料庫中的表和視圖](media/index-sql-relational-data/hotels-database-tables-view.png "酒店資料庫中的表和視圖")


## <a name="the-problem-of-denormalized-data"></a>資料非正常化問題

處理一對多關聯性的挑戰之一是，基於聯合資料表構建的標準查詢將返回非正常化資料，這在 Azure 認知搜索方案中不起作用。 請考慮以下連接酒店和客房的示例。

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
此查詢的結果返回所有 Hotel 欄位，後跟所有"房間"欄位，每個房間值將重複初步酒店資訊。

   ![非正常化資料，添加房間欄位時的冗余酒店資料](media/index-sql-relational-data/denormalize-data-query.png "非正常化資料，添加房間欄位時的冗余酒店資料")


當此查詢在表面上成功（在平面行集中提供所有資料）時，它無法為預期的搜索體驗提供正確的文檔結構。 在索引期間，Azure 認知搜索將為每行引入創建一個搜索文檔。 如果您的搜索文檔看起來像上述結果，您就會感覺到重複 - 僅雙圓頂酒店就有七份單獨的文檔。 關於"佛羅里達的酒店"的查詢將返回七個結果，只為雙圓頂酒店，推動其他相關酒店深入到搜尋結果。

要獲得每個酒店一個文檔的預期體驗，您應該以正確的細微性提供行集，但提供完整的資訊。 幸運的是，通過採用本文中的技術，您可以輕鬆做到這一點。

## <a name="define-a-query-that-returns-embedded-json"></a>定義返回嵌入 JSON 的查詢

要提供預期的搜索體驗，資料集應包含 Azure 認知搜索中每個搜索文檔的一行。 在我們的示例中，我們希望每家酒店有一行，但我們也希望使用者能夠搜索他們關心的其他與房間相關的欄位，例如夜間房價、床位大小和數量，或海灘景觀，所有這些都是房間細節的一部分。

解決方案是捕獲房間細節作為嵌套 JSON，然後將 JSON 結構插入到視圖中的欄位中，如第二步所示。 

1. 假設您有兩個聯接的桌子，Hotel$ 和 Rooms$，其中包含 50 家酒店和 750 間客房的詳細資訊，並在 HotelID 欄位中加入。 這些桌子分別包含 50 家酒店和 750 間相關客房。

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. 創建由父表 （）`SELECT * from dbo.Hotels$`中的所有欄位組成的視圖，並添加包含巢狀查詢輸出的新 *"會議室*"欄位。 將輸出構造為 JSON`SELECT * from dbo.Rooms$`的**FOR JSON AUTO**子句。 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   以下螢幕截圖顯示了生成的視圖，底部有 *"房間*Nvarchar"欄位。 "*房間*"欄位僅存在於"酒店客房"視圖中。

   ![酒店客房景觀](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms 視圖")

1. 運行`SELECT * FROM dbo.HotelRooms`以檢索行集。 此查詢返回 50 行，每個酒店一行，關聯的房間資訊作為 JSON 集合。 

   ![從酒店客房視圖中的行集](media/index-sql-relational-data/hotelrooms-rowset.png "從酒店客房視圖中的行集")

此行集現已準備好導入 Azure 認知搜索。

> [!NOTE]
> 此方法假定嵌入式 JSON 在[SQL Server 的最大列大小限制](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server)下。 如果資料不適合，可以嘗試程式設計方法，如[示例：為 Azure 認知搜索建模 AdventureWorks 清單資料庫](search-example-adventureworks-modeling.md)。

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>對一對多關聯性的"多"一側使用複雜集合

在 Azure 認知搜索端，創建一個索引架構，使用嵌套 JSON 對一對多關聯性建模。 您在上一節中創建的結果集通常對應于下面提供的索引架構（為了簡潔起見，我們剪切了一些欄位）。

下面的示例類似于[如何對複雜資料類型建模](search-howto-complex-data-types.md#creating-complex-fields)中的示例。 *房間*結構，一直是本文的重點，是在欄位收集一個索引命名*酒店*。 此示例還顯示*了位址*的複雜類型，它與 *"會議室"* 不同，因為它由一組固定的項組成，而不是集合中允許的多個任意數量的項。

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

給定上一個結果集和上述索引架構，您擁有成功索引操作所需的所有元件。 拼合的資料集滿足索引要求，但保留詳細資訊。 在 Azure 認知搜索索引中，搜尋結果將很容易落入基於酒店的實體中，同時保留各個房間的上下文及其屬性。

## <a name="next-steps"></a>後續步驟

使用自己的資料集，可以使用["導入資料嚮導](search-import-data-portal.md)"創建和載入索引。 嚮導檢測嵌入的 JSON 集合（如 *"Rooms"* 中包含的集合）並推斷包含複雜類型集合的索引架構。 

  ![導入資料嚮導推斷的索引](media/index-sql-relational-data/search-index-rooms-complex-collection.png "導入資料嚮導推斷的索引")

請嘗試以下快速入門，瞭解導入資料嚮導的基本步驟。

> [!div class="nextstepaction"]
> [快速入門：使用 Azure 門戶創建搜索索引](search-get-started-portal.md)