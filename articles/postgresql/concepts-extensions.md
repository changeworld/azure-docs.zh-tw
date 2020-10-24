---
title: 擴充功能-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中可用的 Postgres 延伸模組
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 78395873457f9fe53d45dfbfd94aa9ccdccd614d
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485455"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的 PostgreSQL 延伸模組 - 單一伺服器
PostgreSQL 提供下列功能：使用擴充功能來擴充您的資料庫功能。 延伸模組會將多個相關 SQL 物件統合在同一個套件之中，而且只要一個命令，就能載入或從資料庫移除。 載入到資料庫之後，延伸模組就像內建功能一樣。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 擴充功能
您必須先在資料庫中安裝 PostgreSQL 擴充功能，然後才能使用它們。 若要安裝特定的擴充功能，請從 psql 工具執行 [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) 命令，以將封裝的物件載入至您的資料庫。

適用於 PostgreSQL 的 Azure 資料庫支援如下所示的金鑰擴充功能子集。 此資訊也可以藉由執行 `SELECT * FROM pg_available_extensions;` 來取得。 不支援所列的延伸模組以外的副檔名。 您無法在適用於 PostgreSQL 的 Azure 資料庫中建立自己的擴充功能。

## <a name="postgres-11-extensions"></a>Postgres 11 擴充功能

下列擴充功能可在 Postgres 11 版的適用於 PostgreSQL 的 Azure 資料庫伺服器中使用。 

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address >standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 支援在 GIN 中編制一般資料類型的索引|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 支援在 GiST 中編制一般資料類型的索引|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 不區分大小寫字元字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 從資料庫內連接至其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 計算地球表面上的大圓圈距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 用於儲存 (索引鍵、值) 組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 整數的立體陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型（例如階層式樹狀結構）|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 從商業 RDBMS 模擬函數和套件子集的函式和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 顯示資料列層級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 依時間或識別碼管理分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 追蹤執行的所有 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) 信任的程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger 地理編碼器和 reverse 地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 拓撲空間類型和函式|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 操作整個資料表的函式，包括交叉資料表|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | 為時間序列資料啟用可調整的插入和複雜查詢|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             |  (Uuid 產生通用唯一識別碼) |

## <a name="postgres-10-extensions"></a>Postgres 10 擴充功能 

下列擴充功能可在 Postgres 第10版的適用於 PostgreSQL 的 Azure 資料庫伺服器中使用。

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address >standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | 支援在 GIN 中編制一般資料類型的索引|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | 支援在 GiST 中編制一般資料類型的索引|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | 不區分大小寫字元字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | 從資料庫內連接至其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | 計算地球表面上的大圓圈距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | 用於儲存 (索引鍵、值) 組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | 整數的立體陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型（例如階層式樹狀結構）|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 從商業 RDBMS 模擬函數和套件子集的函式和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | 顯示資料列層級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 依時間或識別碼管理分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | 追蹤執行的所有 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) 信任的程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tiger 地理編碼器和 reverse 地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS 拓撲空間類型和函式|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | 操作整個資料表的函式，包括交叉資料表|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 為時間序列資料啟用可調整的插入和複雜查詢|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             |  (Uuid 產生通用唯一識別碼) |

## <a name="postgres-96-extensions"></a>Postgres 9.6 擴充功能 

下列擴充功能可在 Postgres 9.6 版的適用於 PostgreSQL 的 Azure 資料庫伺服器中使用。

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Address >standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | 支援在 GIN 中編制一般資料類型的索引|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | 支援在 GiST 中編制一般資料類型的索引|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | 不區分大小寫字元字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | 從資料庫內連接至其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | 計算地球表面上的大圓圈距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | 用於儲存 (索引鍵、值) 組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | 整數的立體陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型（例如階層式樹狀結構）|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 從商業 RDBMS 模擬函數和套件子集的函式和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | 顯示資料列層級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 依時間或識別碼管理分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | 追蹤執行的所有 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) 信任的程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tiger 地理編碼器和 reverse 地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS 拓撲空間類型和函式|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | 操作整個資料表的函式，包括交叉資料表|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 為時間序列資料啟用可調整的插入和複雜查詢|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             |  (Uuid 產生通用唯一識別碼) |

## <a name="postgres-95-extensions"></a>Postgres 9.5 擴充功能 

下列擴充功能可在 Postgres 9.5 版的適用於 PostgreSQL 的 Azure 資料庫伺服器中使用。

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Address >standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | 支援在 GIN 中編制一般資料類型的索引|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | 支援在 GiST 中編制一般資料類型的索引|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | 不區分大小寫字元字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | 從資料庫內連接至其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | 計算地球表面上的大圓圈距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | 判斷字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | 用於儲存 (索引鍵、值) 組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | 整數的立體陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | 階層式樹狀結構的資料類型（例如階層式樹狀結構）|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 從商業 RDBMS 模擬函數和套件子集的函式和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | 顯示資料列層級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 依時間或識別碼管理分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | 追蹤執行的所有 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tiger 地理編碼器和 reverse 地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS 拓撲空間類型和函式|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | 操作整個資料表的函式，包括交叉資料表|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             |  (Uuid 產生通用唯一識別碼) |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements 的延伸](https://www.postgresql.org/docs/current/pgstatstatements.html)模組會在每個適用於 PostgreSQL 的 Azure 資料庫伺服器上預先載入，以提供您追蹤 SQL 語句執行統計資料的方法。
控制延伸模組計入哪些陳述式的設定 `pg_stat_statements.track` 預設為 `top`，這表示會追蹤用戶端直接發出的所有陳述式。 其他兩個會追蹤層級 `none` 和 `all`。 這項設定是可透過 [Azure 入口網站](./howto-configure-server-parameters-using-portal.md)或 [Azure CLI](./howto-configure-server-parameters-using-cli.md) 設定的伺服器參數。

pg_stat_statements 提供查詢執行資訊，不過會對於伺服器效能造成影響，因為它會記錄每個 SQL 陳述式。 如果您不主動使用 pg_stat_statements 延伸模組，我們建議您將 `pg_stat_statements.track` 設定為 `none`。 請注意，某些協力廠商監視服務可能會需要 pg_stat_statements 傳遞查詢效能深入解析，因此請確認這是否為您的情況。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) 和 [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) 可讓您從一個于 postgresql 伺服器連接到另一個伺服器，或連接到同一部伺服器中的另一個資料庫。 接收端伺服器必須允許來自傳送端伺服器通過其防火牆的連線。 將這些延伸模組用於「適用於 PostgreSQL 的 Azure 資料庫」伺服器之間的連線時，可藉由將 [允許存取 Azure 服務] 設為 [開啟] 來執行此動作。 如果您想要使用延伸模組來回路到相同的伺服器，也需要執行此動作。 [允許存取 Azure 服務] 設定可在「適用於 PostgreSQL 的 Azure 資料庫」伺服器的 Azure 入口網站頁面中找到，位於 [連線安全性] 下方。 開啟 [允許存取 Azure 服務] 時，會將所有 Azure Ip 放在允許清單上。

目前並不支援來自適用於 PostgreSQL 的 Azure 資料庫的輸出連線，除了與相同區域中其他適用於 PostgreSQL 的 Azure 資料庫伺服器的連線。

## <a name="uuid"></a>uuid
如果您打算使用 `uuid_generate_v4()` [ossp 延伸](https://www.postgresql.org/docs/current/uuid-ossp.html)模組，請考慮與 pgcrypto 擴充功能的比較， `gen_random_uuid()` 以[pgcrypto extension](https://www.postgresql.org/docs/current/pgcrypto.html)獲得效能優勢。

## <a name="pgaudit"></a>pgAudit
[PgAudit 延伸](https://github.com/pgaudit/pgaudit/blob/master/README.md)模組會提供會話和物件審核記錄。 若要瞭解如何在適用於 PostgreSQL 的 Azure 資料庫中使用此延伸模組，請造訪「 [審核概念](concepts-audit.md)」一文。 

## <a name="pg_prewarm"></a>pg_prewarm
Pg_prewarm 擴充功能會將關聯式資料載入快取中。 預先準備快取表示在重新開機後，您的查詢在第一次執行時有更好的回應時間。 在 Postgres 10 和以下的中，預先準備是使用 [prewarm 函數](https://www.postgresql.org/docs/10/pgprewarm.html)手動完成。

在 Postgres 11 和更新版本中，您可以將預先準備設定為 [自動](https://www.postgresql.org/docs/current/pgprewarm.html)發生。 您必須在 `shared_preload_libraries` 參數的清單中包含 pg_prewarm，然後重新開機伺服器以套用變更。 您可以從 [Azure 入口網站](howto-configure-server-parameters-using-portal.md)、 [CLI](howto-configure-server-parameters-using-cli.md)、REST API 或 ARM 範本設定參數。 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB 是一種時間序列資料庫，封裝為于 postgresql 的擴充功能。 TimescaleDB 提供時間序列工作負載的時間導向分析功能、優化和調整 Postgres。

[深入瞭解 TimescaleDB](https://docs.timescale.com/latest)，這是一份已註冊的 [時間刻度，inc.](https://www.timescale.com/)的商標。 適用於 PostgreSQL 的 Azure 資料庫提供 TimescaleDB 的 [Apache-2 版本](https://www.timescale.com/legal/licenses)。

### <a name="installing-timescaledb"></a>安裝 TimescaleDB
若要安裝 TimescaleDB，您必須將它包含在伺服器的共用預先載入程式庫中。 變更 Postgres 的 `shared_preload_libraries` 參數需要 **伺服器重新開機** 才會生效。 您可以使用 [Azure 入口網站](howto-configure-server-parameters-using-portal.md) 或 [Azure CLI](howto-configure-server-parameters-using-cli.md)來變更參數。

使用 [Azure 入口網站](https://portal.azure.com/)：

1. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 在側邊欄上選取 [ **伺服器參數**]。

3. 搜尋 `shared_preload_libraries` 參數。

4. 選取 [ **TimescaleDB**]。

5. 選取 [ **儲存** ] 以保留您的變更。 儲存變更之後，您會收到通知。 

6. 通知之後，請 **重新開機** 伺服器以套用這些變更。 若要了解如何重新啟動伺服器，請參閱[重新啟動適用於 PostgreSQL 的 Azure 資料庫伺服器](howto-restart-server-portal.md)。


您現在可以在 Postgres 資料庫中啟用 TimescaleDB。 連接到資料庫併發出下列命令：
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 如果您看到錯誤，請確認您在儲存 shared_preload_libraries 之後， [重新開機伺服器](howto-restart-server-portal.md) 。 

您現在可以 [從頭](https://docs.timescale.com/getting-started/creating-hypertables) 開始建立 TimescaleDB hypertable，或 [在於 postgresql 中遷移現有的時間序列資料](https://docs.timescale.com/getting-started/migrating-data)。

### <a name="restoring-a-timescale-database"></a>還原刻度資料庫
若要使用 pg_dump 和 pg_restore 來還原時間刻度資料庫，您必須在目的地資料庫中執行兩個 helper 程式： `timescaledb_pre_restore()` 和 `timescaledb_post restore()` 。

先準備目的地資料庫：

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

現在您可以在原始資料庫上執行 pg_dump，然後執行 pg_restore。 還原之後，請務必在還原的資料庫中執行下列命令：

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>後續步驟
如果您未看見想要使用的擴充功能，請讓我們知道。 投票給現有的要求，或在我們的 [意見反應論壇](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)中建立新的意見反應要求。