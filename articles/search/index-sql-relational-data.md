---
title: 用於匯入和編制索引的 SQL 關聯式資料模型
titleSuffix: Azure Cognitive Search
description: 瞭解如何在 Azure 認知搜尋中建立關聯式資料的模型，並將其還原為一般結果集，以進行索引和全文檢索搜尋。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2ebeb7f6fee77c43c9da97b922fc215d75196145
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82117564"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中建立關聯式 SQL 資料的模型以進行匯入和編制索引

Azure 認知搜尋會接受一般資料列集作為[索引管線](search-what-is-an-index.md)的輸入。 如果來源資料源自 SQL Server 關係資料庫中聯結的資料表，本文會說明如何建立結果集，以及如何在 Azure 認知搜尋索引中建立父子式關聯性的模型。

如圖所示，我們將根據[示範資料](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)來參考假設的飯店資料庫。 假設資料庫包含一個具有50飯店的飯店 $ table，以及一個房間 $ 資料表，內含各種類型、費率和可設施的房間，共750個房間。 資料表之間有一對多關聯性。 在我們的方法中，view 會提供傳回50個數據列的查詢，每個飯店一個資料列，並將相關聯的房間細節內嵌在每個資料列中。

   ![飯店資料庫中的資料表和視圖](media/index-sql-relational-data/hotels-database-tables-view.png "飯店資料庫中的資料表和視圖")


## <a name="the-problem-of-denormalized-data"></a>反正規化資料的問題

使用一對多關聯性的其中一項挑戰是，以聯結資料表為基礎的標準查詢會傳回不正規化的資料，這在 Azure 認知搜尋案例中並不適用。 請考慮下列聯結旅館和房間的範例。

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
此查詢的結果會傳回所有的飯店欄位，後面接著所有的房間欄位，並針對每個房間值重複的初步飯店資訊。

   ![反正規化的資料，新增會議室欄位時的重複飯店資料](media/index-sql-relational-data/denormalize-data-query.png "反正規化的資料，新增會議室欄位時的重複飯店資料")


雖然此查詢會在介面上成功（以一般資料列集提供所有資料），但它卻無法針對預期的搜尋體驗傳遞正確的檔結構。 在編制索引期間，Azure 認知搜尋會針對每個資料列內嵌建立一個搜尋檔。 如果您的搜尋檔看起來像上述結果，您會發現重複專案有七個獨立的檔，分別適用于對應項圓頂飯店。 「佛羅里達州飯店」的查詢只會針對對應項圓頂飯店傳回7個結果，並將其他相關飯店向下推送至搜尋結果。

若要為每個飯店取得一份檔的預期體驗，您應該以正確的資料細微性提供資料列集，但使用完整的資訊。 幸好，您可以藉由採用本文中的技術，輕鬆完成這項工作。

## <a name="define-a-query-that-returns-embedded-json"></a>定義會傳回內嵌 JSON 的查詢

若要提供預期的搜尋體驗，您的資料集應包含 Azure 認知搜尋中每個搜尋檔的一個資料列。 在我們的範例中，我們想要讓每個飯店有一個資料列，但我們也希望使用者能夠搜尋他們關心的其他聊天室相關欄位，例如夜間費率、大小和床鋪數，或海灘的觀點，全都是會議室詳細資料的一部分。

解決方法是將聊天室詳細資料視為嵌套的 JSON 來捕捉，然後將 JSON 結構插入視圖中的欄位，如第二個步驟所示。 

1. 假設您有兩個聯結的資料表：飯店 $ 和室 $，其中包含50飯店和750房間的詳細資料，並已聯結在 HotelID 欄位上。 這些資料表分別包含50飯店和750相關的房間。

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

2. 建立由父資料表（）中所有欄位組成的視圖 `SELECT * from dbo.Hotels$` ，並加入新的*會議室*欄位，其中包含了巢狀查詢的輸出。 的**FOR JSON AUTO**子句會 `SELECT * from dbo.Rooms$` 將輸出結構為 json。 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   下列螢幕擷取畫面顯示產生的視圖，其底部有 [*房間*Nvarchar] 欄位。 [*會議室*] 欄位只存在於 [HotelRooms] 視圖中。

   ![HotelRooms 視圖](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms 視圖")

1. 執行 `SELECT * FROM dbo.HotelRooms` 以取得資料列集。 此查詢會傳回50個數據列（每個飯店一個），並將相關聯的會議室資訊當做 JSON 集合。 

   ![HotelRooms view 的資料列集](media/index-sql-relational-data/hotelrooms-rowset.png "HotelRooms view 的資料列集")

此資料列集現在已準備好匯入到 Azure 認知搜尋。

> [!NOTE]
> 此方法假設內嵌 JSON 低於 SQL Server 的資料[行大小](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server)上限。 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>針對一對多關聯性的「多」端使用複雜集合

在「Azure 認知搜尋」端，使用「嵌套 JSON」來建立建立一對多關聯性模型的索引架構。 您在上一節中建立的結果集，通常會對應到下方提供的索引架構（為了簡潔起見，我們會剪下一些欄位）。

下列範例類似[如何模型複雜資料類型](search-howto-complex-data-types.md#creating-complex-fields)中的範例。 這篇*文章的重點結構是*在名為*飯店*的索引的 fields 集合中。 這個範例也會顯示*Address*的複雜型別，這與*房間*不同，因為它是由一組固定的專案所組成，而不是集合中允許的多個任意數目的專案。

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

假設先前的結果集和上述的索引架構，您就擁有成功編制索引作業所需的所有元件。 簡維資料集符合索引需求，但仍保留詳細資訊。 在 Azure 認知搜尋索引中，搜尋結果會輕鬆地放入飯店型實體，同時保留個別房間及其屬性的內容。

## <a name="next-steps"></a>後續步驟

使用您自己的資料集，您可以使用 [匯[入資料] wizard](search-import-data-portal.md)來建立和載入索引。 此嚮導會偵測內嵌的 JSON 集合（例如*會議室*中所包含的集合），並推斷包含複雜型別集合的索引架構。 

  ![匯入資料 wizard 所推斷的索引](media/index-sql-relational-data/search-index-rooms-complex-collection.png "匯入資料 wizard 所推斷的索引")

請嘗試下列快速入門，以瞭解 [匯入資料] 嚮導的基本步驟。

> [!div class="nextstepaction"]
> [快速入門：使用 Azure 入口網站建立搜尋索引](search-get-started-portal.md)