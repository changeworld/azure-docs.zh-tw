---
title: 使用無伺服器 SQL 集區處理的資料
description: 本檔說明在使用無伺服器 SQL 集區查詢 Azure 儲存體中的資料時，如何計算資料的處理方式。
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424030"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用無伺服器 SQL 集區處理的資料

處理的資料是執行查詢時，在系統中暫時儲存的資料量，包含：

- 從儲存體讀取的資料量–包括：
  - 讀取資料時讀取的資料量
  - 讀取中繼資料時讀取的資料量， (包含中繼資料的檔案格式，例如 Parquet) 
- 中繼結果中的資料量–在查詢執行期間于節點之間傳輸的資料，包括以未壓縮格式將資料傳送至您的端點。 
- 寫入儲存體的資料量–如果您使用 CETAS 將結果集匯出至儲存體，則會向您收取所寫位元組的費用，以及為 CETAS 的 SELECT 部分處理的資料量。

從儲存體讀取檔案已高度優化，而且會使用：

- 預先提取-這可能會對讀取的資料量增加少量的負擔。 如果查詢讀取整個檔案，就不會產生額外負荷。 如果檔案已部分讀取（例如在前 N 個查詢中），則會使用預先提取來讀取更多資料。
- 優化 CSV 剖析器–如果您使用 PARSER_VERSION = ' 2.0 ' 來讀取 CSV 檔案，則會導致從儲存體讀取的資料量稍微增加。  優化 CSV 剖析器會以相同大小的區塊平行讀取檔案。 不保證區塊會包含完整的資料列。 為了確保所有資料列都經過剖析，也會一併讀取較少的相鄰區塊片段，並增加少量的額外負荷。

## <a name="statistics"></a>統計資料

無伺服器 SQL 集區查詢最佳化工具會依賴統計資料來產生最佳的查詢執行計畫。 您可以手動建立統計資料，或由無伺服器 SQL 集區自動建立。 無論何種方式，都會執行個別查詢來建立統計資料，以在提供的取樣率傳回特定資料行。 此查詢會處理相關聯的資料量。

如果您執行的是相同或任何其他可受益于所建立之統計資料的查詢，則會盡可能重複使用統計資料，而且不會處理統計資料建立的其他資料。

建立 Parquet 資料行的統計資料將會導致只從檔案讀取相關的資料行。 建立 CSV 資料行的統計資料將會導致讀取和剖析整個檔案。

## <a name="rounding"></a>四捨五入

處理的資料量將會舍入為每個查詢最接近的 MB，每個查詢最少處理 10 MB 的資料。

## <a name="what-is-not-included-in-data-processed"></a>處理的資料中未包含的內容

- 伺服器層級的中繼資料 (例如登入、角色、伺服器層級認證) 
- 您在端點中所建立的資料庫，因為這些資料庫只包含 (的中繼資料，例如使用者、角色、架構、視圖、內嵌 Tvf、預存程式、資料庫範圍認證、外部資料源、外部檔案格式、外部資料表) 
  - 如果您使用架構推斷，則會讀取檔案的片段以推斷資料行名稱和資料類型。
- DDL 語句（除了建立統計資料，因為它會根據指定的取樣百分比處理儲存體中的資料）
- 僅中繼資料查詢

## <a name="reduce-amount-of-data-processed"></a>減少處理的資料量

您可以將每個查詢的資料量優化，並將您的資料分割並轉換成壓縮的單欄式格式（例如 Parquet），以取得更佳的效能。

## <a name="examples"></a>範例

假設有兩個數據表，每個資料表都有相同的資料列在五個相同的資料行中：

- population_csv 資料表支援 5 TB 的 CSV 檔案
- population_parquet 資料表支援 1 TB 的 Parquet 檔案-此資料表小於上一個，因為 Parquet 包含壓縮的資料
- 支援 100 KB CSV 檔案的 very_small_csv 資料表

**查詢 #1** ：從 POPULATION_CSV 選取 SUM (人口) 

此查詢會讀取及剖析整個檔案，以取得人口資料行的值。 節點將會處理此資料表的片段，每個片段的人口數會在節點之間傳輸，而最後的總和將會轉移至您的端點。 此查詢將會處理 5 TB 的資料，再加上傳送片段總和的少量負擔。

**查詢 #2** ：從 POPULATION_PARQUET 選取 SUM (人口) 

查詢壓縮和資料行導向格式（例如 Parquet）會導致讀取的資料比上一個查詢中的資料少，因為無伺服器 SQL 集區會讀取單一壓縮的資料行，而不是整個檔案。 在此情況下，將會讀取 0.2 TB (五個相同大小的資料行，每個) 0.2 TB。 節點將會處理此資料表的片段，每個片段的人口數會在節點之間傳輸，而最後的總和將會轉移至您的端點。 此查詢將會處理 0.2 TB，再加上傳輸片段總和的少量負擔。

**查詢 #3** ： SELECT * FROM population_parquet

此查詢會讀取所有資料行，並以未壓縮的格式傳送所有資料。 如果壓縮格式為5:1，則會處理 6 TB，因為它會讀取 1 TB + 傳輸 5 TB 的未壓縮資料。

**查詢 #4** ： SELECT COUNT ( * ) FROM very_small_csv

此查詢會讀取整個檔案。 此資料表之儲存體中的檔案大小總計為 100 KB。 節點將會處理此資料表的片段，每個片段的總和會在節點之間傳輸，而最後的總和將會轉移至您的端點。 此查詢將會處理稍微超過 100 KB 的資料。 針對此查詢所處理的資料量將會舍入為 10 MB，如 [四捨五入](#rounding)中所指定。

## <a name="next-steps"></a>後續步驟

若要瞭解如何優化您的查詢效能，請檢查 [無伺服器 SQL 集區的最佳做法](best-practices-sql-on-demand.md)。
