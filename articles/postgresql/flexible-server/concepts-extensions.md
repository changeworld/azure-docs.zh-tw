---
title: 擴充功能-適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
description: 瞭解適用於 PostgreSQL 的 Azure 資料庫彈性伺服器中可用的 Postgres 延伸模組
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f38006e83be47142a6d7a6db25eefb3daccd0d92
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307580"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>適用於 PostgreSQL 的 Azure 資料庫-彈性伺服器中的于 postgresql 延伸模組

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

PostgreSQL 提供下列功能：使用擴充功能來擴充您的資料庫功能。 延伸模組會將多個相關的 SQL 物件組合在單一封裝中，以使用命令從您的資料庫載入或移除。 載入到資料庫之後，延伸模組就像內建功能一樣。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 擴充功能
您必須先在資料庫中安裝 PostgreSQL 擴充功能，然後才能使用它們。 若要安裝特定的擴充功能，請執行 [CREATE extension](https://www.postgresql.org/docs/current/sql-createextension.html) 命令。 此命令會將封裝的物件載入至您的資料庫。

適用於 PostgreSQL 的 Azure 資料庫支援如下所示的金鑰擴充功能子集。 此資訊也可以藉由執行 `SHOW azure.extensions;` 來取得。 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器不支援本檔中未列出的延伸模組。 您無法在適用於 PostgreSQL 的 Azure 資料庫中建立或載入您自己的擴充功能。


## <a name="postgres-12-extensions"></a>Postgres 12 擴充功能

下列擴充功能可在具有 Postgres 版本12的適用於 PostgreSQL 的 Azure 資料庫彈性伺服器中使用。 

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **說明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Address >standardizer US 資料集範例|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | 用來驗證關聯性完整性的函數|
> |[綻放](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | bloom 存取方法-以簽章為基礎的索引|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | 支援在 GIN 中編制一般資料類型的索引|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | 支援在 GiST 中編制一般資料類型的索引|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | 不區分大小寫字元字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | 從資料庫內連接至其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | 用於擴充同義文書處理的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | 計算地球表面上的大圓圈距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | 用於儲存 (索引鍵、值) 組集合的資料類型|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | 整數匯總工具和列舉值。  (淘汰) |
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | 整數的立體陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型（例如階層式樹狀結構）|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | 檢查低層級的資料庫頁面內容|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區快取|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | 檢查 (FSM 的可用空間對應) |
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | 追蹤執行的所有 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | 檢查 (VM) 和頁面層級可見度資訊的可見度地圖|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | 顯示資料列層級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | 顯示元組層級統計資料|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS geometry、geography |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | PostGIS 點陣類型和函式| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS tiger 地理編碼器和 reverse 地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | PostGIS 拓撲空間類型和函式|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | SSL 憑證的相關資訊|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  將資料列數限制為限制的 TABLESAMPLE 方法|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE 方法，接受以毫秒為單位的時間限制|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             |  (Uuid 產生通用唯一識別碼) |

## <a name="postgres-11-extensions"></a>Postgres 11 擴充功能

下列擴充功能可在具有 Postgres 11 版的適用於 PostgreSQL 的 Azure 資料庫彈性伺服器中使用。 

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **說明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address >standardizer US 資料集範例|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | 用來驗證關聯性完整性的函數|
> |[綻放](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | bloom 存取方法-以簽章為基礎的索引|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 支援在 GIN 中編制一般資料類型的索引|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 支援在 GiST 中編制一般資料類型的索引|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 不區分大小寫字元字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 從資料庫內連接至其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | 用於擴充同義文書處理的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 計算地球表面上的大圓圈距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 用於儲存 (索引鍵、值) 組集合的資料類型|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | 整數匯總工具和列舉值。  (淘汰) |
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 整數的立體陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型（例如階層式樹狀結構）|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | 檢查低層級的資料庫頁面內容|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區快取|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | 檢查 (FSM 的可用空間對應) |
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 追蹤執行的所有 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | 檢查 (VM) 和頁面層級可見度資訊的可見度地圖|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 顯示資料列層級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 顯示元組層級統計資料|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger 地理編碼器和 reverse 地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 拓撲空間類型和函式|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | SSL 憑證的相關資訊|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 操作整個資料表的函式，包括交叉資料表|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  將資料列數限制為限制的 TABLESAMPLE 方法|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  TABLESAMPLE 方法，接受以毫秒為單位的時間限制|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             |  (Uuid 產生通用唯一識別碼) |


## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) 和 [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) 可讓您從一個于 postgresql 伺服器連接到另一個伺服器，或連接到同一部伺服器中的另一個資料庫。 傳送端伺服器必須允許接收伺服器的輸出連接。 接收端伺服器必須允許來自傳送伺服器的連接。

如果您打算使用這兩個擴充功能，建議您使用 [VNet 整合](concepts-networking.md) 來部署伺服器。 根據預設，VNet 整合允許在 VNET 中的伺服器之間進行連線。 您也可以選擇使用 [VNet 網路安全性群組](../../virtual-network/manage-network-security-group.md) 來自訂存取。


## <a name="pg_prewarm"></a>pg_prewarm

Pg_prewarm 擴充功能會將關聯式資料載入快取中。 預先準備快取表示在重新開機後，您的查詢在第一次執行時有更好的回應時間。 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器目前無法使用自動 prewarm 功能。

## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements 的延伸](https://www.postgresql.org/docs/current/pgstatstatements.html)模組會在每個適用於 PostgreSQL 的 Azure 資料庫的彈性伺服器上預先載入，以提供一種方式來追蹤 SQL 語句的執行統計資料。
控制延伸模組計入哪些陳述式的設定 `pg_stat_statements.track` 預設為 `top`，這表示會追蹤用戶端直接發出的所有陳述式。 其他兩個會追蹤層級 `none` 和 `all`。 這項設定可設定為伺服器參數。

pg_stat_statements 提供查詢執行資訊，不過會對於伺服器效能造成影響，因為它會記錄每個 SQL 陳述式。 如果您不主動使用 pg_stat_statements 延伸模組，我們建議您將 `pg_stat_statements.track` 設定為 `none`。 請注意，某些協力廠商監視服務可能會需要 pg_stat_statements 傳遞查詢效能深入解析，因此請確認這是否為您的情況。


## <a name="next-steps"></a>後續步驟

如果您未看見想要使用的擴充功能，請讓我們知道。 投票給現有的要求，或在我們的 [意見反應論壇](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)中建立新的意見反應要求。