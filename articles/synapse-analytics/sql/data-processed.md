---
title: 使用無伺服器 SQL 集區處理的資料
description: 本檔說明當您在 data lake 中查詢資料時，如何計算資料處理量。
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a108e5fdd30c21cdb7771e3f683dad22773653a4
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381196"
---
# <a name="data-processed-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用無伺服器 SQL 集區處理的資料

*處理的資料* 是系統在執行查詢時暫時儲存的資料量。 處理的資料包含下列數量：

- 從儲存體讀取的資料量。 此數量包括：
  - 讀取資料時讀取的資料。
  - 讀取中繼資料時讀取的資料 (適用于包含中繼資料的檔案格式，例如 Parquet) 。
- 中繼結果中的資料量。 這項資料會在查詢執行時，在節點之間傳輸。 它包含以未壓縮格式將資料傳輸至您的端點。 
- 寫入儲存體的資料量。 如果您使用 CETAS 將結果集匯出至儲存體，則會將寫出的資料量新增至針對 CETAS 的 SELECT 部分所處理的資料量。

從儲存體讀取檔案已高度優化。 此程式會使用：

- 預先提取可能會對讀取的資料量增加額外負荷。 如果查詢讀取整個檔案，就不會產生額外負荷。 如果檔案已部分讀取（例如，在前 N 個查詢中），則會使用預先提取來讀取更多資料。
-  (CSV) 剖析器的優化逗號分隔值。 如果您使用 PARSER_VERSION = ' 2.0 ' 來讀取 CSV 檔案，則從儲存體讀取的資料量會稍微增加。 優化 CSV 剖析器會以相同大小的區塊，以平行方式讀取檔案。 區塊不一定包含完整的資料列。 為了確保所有資料列都經過剖析，優化的 CSV 剖析器也會讀取較少的相鄰區塊片段。 此程式會增加少量的負擔。

## <a name="statistics"></a>統計資料

無伺服器 SQL 集區查詢最佳化工具會依賴統計資料來產生最佳的查詢執行計畫。 您可以手動建立統計資料。 否則，無伺服器 SQL 集區會自動建立它們。 無論何種方式，都會執行個別的查詢，以在提供的取樣率傳回特定資料行來建立統計資料。 此查詢會處理相關聯的資料量。

如果您執行的是相同或任何其他可受益于所建立之統計資料的查詢，則會盡可能重複使用統計資料。 建立統計資料時，並不會處理任何其他資料。

當針對 Parquet 資料行建立統計資料時，只會從檔案讀取相關的資料行。 針對 CSV 資料行建立統計資料時，會讀取和剖析整個檔案。

## <a name="rounding"></a>四捨五入

處理的資料量會舍入為每個查詢最接近的 MB。 每個查詢最少處理 10 MB 的資料。

## <a name="what-data-processed-doesnt-include"></a>處理的資料不包含

- 伺服器層級的中繼資料 (例如登入、角色，以及伺服器層級的認證) 。
- 您在端點中建立的資料庫。 這些資料庫只包含 (的中繼資料，例如使用者、角色、架構、視圖、內嵌資料表值函式 [Tvf]、預存程式、資料庫範圍認證、外部資料源、外部檔案格式，以及外部資料表) 。
  - 如果您使用架構推斷，則會讀取檔案片段以推斷資料行名稱和資料類型，並將讀取的資料量新增至處理的資料量。
- 資料定義語言 (DDL) 語句，除了 CREATE STATISTICS 語句，因為它會根據指定的取樣百分比處理儲存體中的資料。
- 僅中繼資料的查詢。

## <a name="reducing-the-amount-of-data-processed"></a>減少處理的資料量

您可以將資料分割並轉換成壓縮的資料行格式（例如 Parquet），以優化每個查詢處理的資料量並改善效能。

## <a name="examples"></a>範例

想像一下三個數據表。

- Population_csv 資料表是由 5 TB 的 CSV 檔案所支援。 這些檔案會組織成五個大小相同的資料行。
- Population_parquet 資料表具有與 population_csv 資料表相同的資料。 它是由 1 TB 的 Parquet 檔案所支援。 此資料表小於前一個資料表，因為資料會以 Parquet 格式壓縮。
- Very_small_csv 資料表支援 100 KB 的 CSV 檔案。

**查詢 1** ：從 population_csv 中選取 [SUM (人口) 

此查詢會讀取及剖析整個檔案，以取得人口資料行的值。 節點會處理此資料表的片段，而每個片段的人口總和會在節點之間傳輸。 最後一個總和會轉移至您的端點。 

此查詢會處理 5 TB 的資料，加上少量的額外負荷來傳送片段的總和。

**查詢 2** ：從 population_parquet 中選取 [SUM (人口) 

當您查詢壓縮和以資料行為基礎的格式（例如 Parquet）時，讀取的資料會比查詢1中的資料少。 因為無伺服器 SQL 集區會讀取單一壓縮的資料行，而不是整個檔案，所以您會看到此結果。 在此情況下，會讀取 0.2 TB。  (五個大小相同的資料行都是 0.2 TB。 ) 節點會處理此資料表的片段，而每個片段的人口總和會在節點之間傳輸。 最後一個總和會轉移至您的端點。 

此查詢會處理 0.2 TB 加上少量的額外負荷，以傳送片段的總和。

**查詢 3** ： SELECT * FROM population_parquet

此查詢會讀取所有資料行，並以未壓縮的格式傳送所有資料。 如果壓縮格式為5:1，則查詢會處理 6 TB，因為它會讀取 1 TB 並傳輸 5 TB 的未壓縮資料。

**查詢 4** ：從 VERY_SMALL_CSV 選取 COUNT ( * ) 

此查詢會讀取整個檔案。 此資料表之儲存體中的檔案大小總計為 100 KB。 節點會處理此資料表的片段，而每個片段的總和會在節點之間傳輸。 最後一個總和會轉移至您的端點。 

此查詢會處理稍微超過 100 KB 的資料。 此查詢所處理的資料量會進位到 10 MB （如本文的 [舍入](#rounding) 區段中所指定）。

## <a name="next-steps"></a>後續步驟

若要瞭解如何優化您的查詢效能，請參閱 [無伺服器 SQL 集區的最佳做法](best-practices-sql-on-demand.md)。
