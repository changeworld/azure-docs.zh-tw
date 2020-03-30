---
title: 多模型功能
description: Azure SQL Database 可讓您在相同的資料庫中使用多個資料模型。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73802823"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Azure SQL Database 的多模型功能

多模型資料庫可讓您儲存及處理以多種資料格式 (例如關聯式資料、圖形、JSON/XML 文件、索引鍵/值組) 表現的資料。

## <a name="when-to-use-multi-model-capabilities"></a>何時使用多模型功能

Azure SQL Database 的設計訴求是要使用關聯式模型，在各種一般用途應用程式的大部分情況下達到最佳效能。 不過，Azure SQL Database 不限於關聯式資料。 Azure SQL Database 可讓您使用緊密整合到關聯式模型中的各種非關聯式格式。
在以下情況下，應考慮使用 Azure SQL 資料庫的多模型功能：
- 您有一些更適合 NoSQL 模型的資訊或結構，並且不想使用單獨的 NoSQL 資料庫。
- 大多數資料都適合關係模型，您需要以 NoSQL 樣式對資料的某些部分進行建模。
- 您希望利用豐富的 Transact-SQL 語言來查詢和分析關係資料和 NoSQL 資料，並將其與可以使用 SQL 語言的各種工具和應用程式集成。
- 您希望應用資料庫功能（如[記憶體中技術](sql-database-in-memory.md)）來提高 NoSQL 資料結構的分析或處理性能，使用[異動複寫](sql-database-managed-instance-transactional-replication.md)或[可讀副本](sql-database-read-scale-out.md)在異地創建資料副本，並從主資料庫卸載一些分析工作負載。

## <a name="overview"></a>總覽

Azure SQL 提供下列多模型功能：
- [圖形功能](#graph-features)可讓您以一組節點和邊緣的形式表現資料，並使用以 `MATCH` 運算子增強的標準 Transact-SQL 查詢來查詢圖形資料。
- [JSON 功能](#json-features)可讓您將 JSON 文件放在資料表、將關聯式資料轉換為 JSON 文件，反之亦然。 您可以使用以 JSON 函式增強的標準 Transact-SQL 語言來剖析文件，並使用非叢集式索引、資料行存放區索引或記憶體最佳化的資料表，將查詢最佳化。
- [空間功能](#spatial-features)可讓您儲存地理和幾何資料、使用空間索引來編製其索引，以及使用空間查詢來擷取資料。
- [XML 功能](#xml-features)可讓您在資料庫中儲存 XML 資料及編製其索引，並使用原生 XQuery/XPath 作業來處理 XML 資料。 Azure SQL 資料庫有專門處理 XML 資料的內建 XML 查詢引擎。
- [鍵值對](#key-value-pairs)不作為特殊功能顯式支援，因為鍵值對可以本機建模為兩清單。

  > [!Note]
  > 您可以在相同的 Transact-SQL 查詢中使用 JSON 路徑運算式、XQuery/XPath 運算式、空間函式和圖形查詢運算式，以存取您儲存在資料庫中的任何資料。 此外，任何執行 Transact-SQL 查詢的工具或程式設計語言，也可以使用該查詢介面來存取多模型資料。 相較於多模型資料庫 (例如可針對不同資料模型提供特製化 API 的 [Azure Cosmos DB](/azure/cosmos-db/))，這是主要差異。

在下列各節中，您可以了解 Azure SQL Database 的最重要多模型功能。

## <a name="graph-features"></a>Graph 功能

Azure SQL Database 會提供圖形資料庫功能，以將資料庫中的多對多關聯性模型化。 圖形是節點 (或頂點) 和邊緣 (或關聯性) 的集合。 節點代表實體 (例如，個人或組織)，而邊緣代表其所連接的兩個節點之間的關聯性 (例如，按讚數或朋友)。 以下是讓圖表資料庫變成唯一的一些功能：
- 邊緣或關聯性是圖形資料庫中的第一級實體，可以有與其相關聯的屬性。
- 單一邊緣可以彈性地連接圖形資料庫中的多個節點。
- 您可以輕鬆地表達模式比對和多躍點導覽查詢。
- 您可以輕鬆地表達遞移閉包和多型查詢。

圖形關聯性和圖形查詢功能都已整合到 Transact-SQL 中，並享有使用 SQL Server 作為基礎資料庫管理系統的優點。
[圖形處理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)是核心 SQL Server 資料庫引擎功能，可讓您可找到關於圖形處理的詳細資訊。

### <a name="when-to-use-a-graph-capability"></a>使用圖形功能的時機

使用關聯式資料庫可以達成圖形資料庫所能達成的一切。 不過，圖形資料庫可以輕鬆地表達某些查詢。 您可以根據下列因素來決定選擇哪一種：

- 一個節點可有多個父代的模型階層式資料，因此不能使用 HieararchyId
- 您的應用程式具有複雜多對多關聯性的模型；隨著應用程式發展，會加入新的關聯性。
- 您需要分析互連的資料和關聯性。

## <a name="json-features"></a>JSON 功能

Azure SQL Database 可讓您剖析及查詢以「JavaScript 物件標記法」 [(JSON)](https://www.json.org/) 格式表示的資料，然後將您的關聯式資料匯出成 JSON 文字。

JSON 是一種用於在新式的 Web 與行動應用程式中交換資料的常用資料格式。 JSON 也用於將半結構化的資料儲存在記錄檔或 NoSQL 資料庫 (例如 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)) 中。 許多 REST Web 服務會傳回已格式化為 JSON 文字的結果，或接受已格式化為 JSON 的資料。 大多數 Azure 服務（如[Azure 認知搜索](https://azure.microsoft.com/services/search/)[、Azure 存儲](https://azure.microsoft.com/services/storage/)和[Azure 宇宙資料庫](https://azure.microsoft.com/services/cosmos-db/)）具有返回或使用 JSON 的 REST 終結點。

Azure SQL Database 可讓您輕鬆使用 JSON 資料，並將資料庫與新式服務整合。 Azure SQL Database 提供下列可與 JSON 資料搭配使用的函數：

![JSON 函數](./media/sql-database-json-features/image_1.png)

如果您有 JSON 文字，您可以透過使用內建的函式 [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx)、[JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx) 及 [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx)，從 JSON 擷取資料或確認 JSON 的格式是否正確。 [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) 函式可讓您更新 JSON 文字內的值。 針對更進階的查詢和分析， [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) 函數可以將 JSON 物件陣列轉換成一組資料列。 您可以在傳回的結果集上執行任何 SQL 查詢。 最後，還有 [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) 子句，此子句可讓您將儲存在關聯式資料表中的資料格式化為 JSON 文字。

如需詳細資訊，請參閱[如何在 Azure SQL Database 中處理 JSON 資料](sql-database-json-features.md)。
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) 是核心 SQL Server 資料庫引擎功能，可讓您可找到關於 JSON 功能的詳細資訊。

### <a name="when-to-use-a-json-capability"></a>使用 JSON 功能的時機

在某些特定情況下，可以使用文件模型，而不是關聯式模型：
- 結構描述的高度正規化並不會帶來重大優點，因為您可一次存取物件的所有欄位，或您永遠不會更新物件的正規化組件。 不過，正規化模型會增加查詢的複雜度，因為您需要聯結大量資料表才能取得資料。
- 您正在使用以原生方式使用 JSON 文件的應用程式屬於通訊或資料模型，而且您不想引進可將關聯式資料轉換為 JSON (反之亦然) 的額外藏次。
- 您需要將子系資料表或實體物件值模式反正規化，以簡化您的資料模型。
- 您需要載入或匯出以 JSON 格式儲存的資料，而不需一些可剖析資料的其他工具。

## <a name="spatial-features"></a>空間功能

空間資料代表幾何物件的實體位置和圖形相關資訊。 這些物件可以是點位置或更複雜的物件，如國家/區域、道路或湖泊。

Azure SQL Database 支援兩種空間資料類型 - 幾何資料類型和地理資料類型。
- 幾何類型代表歐氏 (平面) 座標系統中的資料。
- 地理類型代表球形地球座標系統中的資料。

有許多可使用於 Azure SQL 資料庫中的空間物件，例如 [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point)、[LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring)、[Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon) 等。

Azure SQL Database 也會提供特製化[空間索引](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview)，以便改善空間查詢的效能。

[空間支援](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)是核心 SQL Server 資料庫引擎功能，可讓您可找到關於空間功能的詳細資訊。

## <a name="xml-features"></a>XML 功能

SQL Server 提供強大的平台，可供開發豐富的應用程式來進行半結構化資料管理。 XML 支援已整合到 SQL Server 中的所有元件，包括下列各項：

- Xml 資料類型。 XML 值可以原生方式儲存在 xml 資料類型資料行中，其可依照 XML 結構描述的集合設定類型或維持不具類型。 您可以編製 XML 資料行的索引。
- 針對資料行中儲存的 XML 資料和 xml 類型的變數指定 XQuery 查詢的能力。 XQuery 功能可使用於任何 Transact-SQL 查詢，以存取您在資料庫中使用的任何資料模型。
- 使用[主要 XML 索引](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)，自動為 XML 文件中的所有元素編製索引，或使用[次要 XML 索引](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)，指定應該編製索引的確切路徑。
- OPENROWSET 允許大量載入 XML 資料。
- 將關聯式資料轉換為 XML 格式。

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) 是核心 SQL Server 資料庫引擎功能，可讓您可找到關於 XML 功能的詳細資訊。

### <a name="when-to-use-an-xml-capability"></a>使用 XML 功能的時機

在某些特定情況下，可以使用文件模型，而不是關聯式模型：
- 結構描述的高度正規化並不會帶來重大優點，因為您可一次存取物件的所有欄位，或您永遠不會更新物件的正規化組件。 不過，正規化模型會增加查詢的複雜度，因為您需要聯結大量資料表才能取得資料。
- 您正在使用以原生方式使用 XML 文件的應用程式屬於通訊或資料模型，而且您不想引進可將關聯式資料轉換為 XML (反之亦然) 的額外藏次。
- 您需要將子系資料表或實體物件值模式反正規化，以簡化您的資料模型。
- 您需要載入或匯出以 XML 格式儲存的資料，而不需一些可剖析資料的其他工具。

## <a name="key-value-pairs"></a>索引鍵/值組

Azure SQL Database 沒有可支援索引鍵/值組的特製化類型或結構，因為索引鍵/值結構可以原生方式表現為標準關聯式資料表：

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

您可自訂此索引鍵/值結構以符合您的需求，沒有任何限制。 例如，此值可以是 XML 文件，而不是 `nvarchar(max)` 類型，如果值為 JSON 文件，您可以放置 `CHECK` 條件約束來確認 JSON 內容的有效性。 您可以在額外的資料行中放置某個索引鍵的任意多個相關值、新增計算的資料行和索引以簡化和最佳化資料存取、將資料表定義為僅限記憶體/最佳化結構描述的資料表以取得更佳效能等等。

請參閱 [BWin 如何使用記憶體內部 OLTP 來達到前所未有的效能和規模](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/)中達成每秒快取 1.200.000 個批次的 ASP.NET 快取解決方案，作為關聯式模型如何才能有效地實際作為索引鍵/值組解決方案的範例。

## <a name="next-steps"></a>後續步驟
Azure SQL Database 中的多模型功能也是 Azure SQL Database 與 SQL Server 之間共用的核心 SQL Server 資料庫引擎功能。 若要了解這些功能的詳細資訊，請瀏覽 SQL 關聯式資料庫文件頁面：

* [圖表處理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON 資料](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [空間支援](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML 資料](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
