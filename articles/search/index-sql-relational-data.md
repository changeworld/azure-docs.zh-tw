---
title: 建立匯入和編制索引的 SQL 關聯式資料模型
titleSuffix: Azure Cognitive Search
description: 瞭解如何在 Azure 認知搜尋中，將關聯式資料模型化、還原標準化為一般結果集，以進行編制索引和全文檢索搜尋。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924515"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中建立關聯式 SQL 資料模型以進行匯入和編制索引

Azure 認知搜尋接受一般資料列集做為 [索引管線](search-what-is-an-index.md)的輸入。 如果您的來源資料源自 SQL Server 關係資料庫中的聯結資料表，本文將說明如何建立結果集，以及如何建立 Azure 認知搜尋索引中的父子式關聯性模型。

在圖例中，我們將根據 [示範資料](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)來參考假設飯店資料庫。 假設資料庫包含具有50旅館的飯店 $ table，以及具有不同類型、費率和便利設施房間的房間 $ 表格，共750個房間。 資料表之間有一對多關聯性。 在我們的方法中，view 會提供傳回50個數據列的查詢，每個飯店有一個資料列，並在每個資料列中內嵌相關聯的房間詳細資料。

   ![飯店資料庫中的資料表和觀點](media/index-sql-relational-data/hotels-database-tables-view.png "飯店資料庫中的資料表和觀點")


## <a name="the-problem-of-denormalized-data"></a>反正規化資料的問題

使用一對多關聯性的挑戰之一，就是建立在聯結資料表上的標準查詢會傳回反正規化的資料，這在 Azure 認知搜尋的情況下無法正常運作。 請考慮下列聯結旅館和房間的範例。

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
此查詢的結果會傳回所有旅館欄位，後面接著所有房間欄位，並針對每個房間值重複出現初步旅館資訊。

   ![未正規化的資料，新增空間欄位時的重複旅館資料](media/index-sql-relational-data/denormalize-data-query.png "未正規化的資料，新增空間欄位時的重複旅館資料")


雖然此查詢會在介面上成功， (提供一般資料列集) 中的所有資料，但卻無法提供預期搜尋體驗的正確檔結構。 在編制索引期間，Azure 認知搜尋會為每個資料列內嵌建立一個搜尋檔。 如果您的搜尋檔看起來像上面的結果，您可能會發現有重複的專案，也就是對應項凸占旅館的七個個別檔。 「佛羅里達州飯店」的查詢只會針對對應項凸占旅館傳回7個結果，並將其他相關旅館深入探討搜尋結果。

若要為每個飯店取得一份檔的預期體驗，您應以正確的資料細微性提供資料列集，但提供完整資訊。 幸好，您可以藉由採用本文中的技巧，輕鬆地完成這項作業。

## <a name="define-a-query-that-returns-embedded-json"></a>定義會傳回內嵌 JSON 的查詢

若要提供預期的搜尋體驗，您的資料集應該針對 Azure 認知搜尋中的每個搜尋檔，各包含一個資料列。 在我們的範例中，我們想要讓每個飯店都有一個資料列，但我們也希望使用者能夠在他們在意的其他房間相關欄位上進行搜尋，例如夜間費率、張床的大小和數量，或是海灘的觀點，全都是房間詳細資料的一部分。

解決方法是將房間細節視為嵌套 JSON，然後將 JSON 結構插入到視圖中的欄位，如第二個步驟所示。 

1. 假設您有兩個聯結的資料表（旅館 $ 和室 $）包含50飯店和750房間的詳細資料，並已聯結在 HotelID 欄位上。 這些資料表個別包含50飯店和750相關房間。

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

2. 建立由父資料表中的所有欄位組成的視圖 (`SELECT * from dbo.Hotels$`) ，並新增包含巢狀查詢輸出的新 *房間* 欄位。 將輸出結構為 JSON 的 **FOR JSON AUTO** 子句 `SELECT * from dbo.Rooms$` 。 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   下列螢幕擷取畫面顯示產生的視圖，以及底部的 *房間* Nvarchar 欄位。 [ *聊天室* ] 欄位只存在於 HotelRooms view 中。

   ![HotelRooms 視圖](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms 視圖")

1. 執行 `SELECT * FROM dbo.HotelRooms` 以取得資料列集。 此查詢會傳回50個數據列（每個飯店一個），並將相關聯的房間資訊作為 JSON 集合。 

   ![HotelRooms view 中的資料列集](media/index-sql-relational-data/hotelrooms-rowset.png "HotelRooms view 中的資料列集")

此資料列集現在已準備好匯入 Azure 認知搜尋。

> [!NOTE]
> 這種方法假設內嵌 JSON 是在 [SQL Server 的最大資料行大小限制](/sql/sql-server/maximum-capacity-specifications-for-sql-server)下。 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>對一對多關聯性的「多」端使用複雜集合

在 Azure 認知搜尋端建立索引架構，以使用嵌套 JSON 建立一對多關聯性的模型。 您在上一節中建立的結果集通常會對應至下面提供的索引架構 (我們會為了簡潔起見，剪下一些欄位) 。

下列範例與 [如何建立複雜資料類型模型](search-howto-complex-data-types.md#creating-complex-fields)的範例類似。 *聊天室*結構是本文的重點，是在名為*飯店*之索引的 fields 集合中。 此範例也會顯示 *位址*的複雜類型，與 *房間* 不同之處在于，它是由一組固定的專案所組成，而不是集合中允許的任意專案數目。

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

由於先前的結果集和上述的索引架構，您擁有成功編制索引作業所需的所有元件。 簡維資料集符合索引編制需求，但仍會保留詳細資訊。 在 Azure 認知搜尋索引中，搜尋結果會輕鬆地進入旅館型實體，同時保留個別房間和其屬性的內容。

## <a name="next-steps"></a>後續步驟

使用您自己的資料集，您可以使用 [匯 [入資料] 嚮導](search-import-data-portal.md) 來建立和載入索引。 Wizard 會偵測內嵌的 JSON 集合（例如 *房間*中包含的集合），並推斷包含複雜型別集合的索引架構。 

  ![匯入資料 wizard 推斷的索引](media/index-sql-relational-data/search-index-rooms-complex-collection.png "匯入資料 wizard 推斷的索引")

請嘗試下列快速入門，以瞭解 [匯入資料] 嚮導的基本步驟。

> [!div class="nextstepaction"]
> [快速入門：使用 Azure 入口網站建立搜尋索引](search-get-started-portal.md)