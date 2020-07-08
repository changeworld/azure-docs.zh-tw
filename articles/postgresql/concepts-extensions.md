---
title: 延伸模組-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中可用的 Postgres 擴充功能
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77201266"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的于 postgresql 擴充功能-單一伺服器
PostgreSQL 提供下列功能：使用擴充功能來擴充您的資料庫功能。 延伸模組會將多個相關的 SQL 物件組合在單一封裝中，您可以使用單一命令從您的資料庫中載入或移除它們。 載入資料庫之後，擴充功能就像內建功能一樣。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 擴充功能
您必須先在資料庫中安裝 PostgreSQL 擴充功能，然後才能使用它們。 若要安裝特定的擴充功能，請從 psql 工具執行 [CREATE extension](https://www.postgresql.org/docs/current/sql-createextension.html)   命令，以將封裝的物件載入至您的資料庫。

適用於 PostgreSQL 的 Azure 資料庫支援如下所列的金鑰延伸子集。 此資訊也可以藉由執行 `SELECT * FROM pg_available_extensions;` 來取得。 不支援所列的延伸模組。 您無法在適用於 PostgreSQL 的 Azure 資料庫中建立自己的延伸模組。

## <a name="postgres-11-extensions"></a>Postgres 11 延伸模組

在 Postgres 版本11的適用於 PostgreSQL 的 Azure 資料庫伺服器中，可以使用下列延伸模組。 

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 支援 GIN 中的一般資料類型編制索引|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 支援 GiST 中的一般資料類型編制索引|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 不區分大小寫之字元字串的資料類型|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 從資料庫內連接到其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 在地球表面上計算大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似和距離|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 用於儲存（索引鍵、值）組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 一維整數陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 模擬商業 RDBMS 的函式和封裝子集的函數和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 顯示資料列層級的鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 依時間或識別碼管理資料分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 追蹤所有已執行之 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript （v8）信任的程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger geocoder 和 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 操作整個資料表的函數，包括交叉資料表|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | 啟用可調整的插入和複雜的時間序列資料查詢|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | 產生通用唯一識別碼（Uuid）|

## <a name="postgres-10-extensions"></a>Postgres 10 延伸模組 

在 Postgres 第10版的適用於 PostgreSQL 的 Azure 資料庫伺服器中，可以使用下列延伸模組。

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | 支援 GIN 中的一般資料類型編制索引|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | 支援 GiST 中的一般資料類型編制索引|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | 不區分大小寫之字元字串的資料類型|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | 從資料庫內連接到其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | 在地球表面上計算大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似和距離|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | 用於儲存（索引鍵、值）組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | 一維整數陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 模擬商業 RDBMS 的函式和封裝子集的函數和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | 顯示資料列層級的鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 依時間或識別碼管理資料分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | 追蹤所有已執行之 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript （v8）信任的程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tiger geocoder 和 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS 拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | 操作整個資料表的函數，包括交叉資料表|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 啟用可調整的插入和複雜的時間序列資料查詢|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | 產生通用唯一識別碼（Uuid）|

## <a name="postgres-96-extensions"></a>Postgres 9.6 延伸模組 

下列擴充功能適用于 Postgres 9.6 版的適用於 PostgreSQL 的 Azure 資料庫伺服器。

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Address Standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | 支援 GIN 中的一般資料類型編制索引|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | 支援 GiST 中的一般資料類型編制索引|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | 不區分大小寫之字元字串的資料類型|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | 從資料庫內連接到其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | 在地球表面上計算大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | 判斷字串之間的相似和距離|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | 用於儲存（索引鍵、值）組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | 一維整數陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | 階層式樹狀結構的資料類型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 模擬商業 RDBMS 的函式和封裝子集的函數和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | 顯示資料列層級的鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 依時間或識別碼管理資料分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | 追蹤所有已執行之 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript （v8）信任的程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tiger geocoder 和 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS 拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | 操作整個資料表的函數，包括交叉資料表|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 啟用可調整的插入和複雜的時間序列資料查詢|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | 產生通用唯一識別碼（Uuid）|

## <a name="postgres-95-extensions"></a>Postgres 9.5 延伸模組 

下列擴充功能適用于 Postgres 9.5 版的適用於 PostgreSQL 的 Azure 資料庫伺服器。

> [!div class="mx-tableFixed"]
> | **分機**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Address Standardizer US 資料集範例|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | 支援 GIN 中的一般資料類型編制索引|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | 支援 GiST 中的一般資料類型編制索引|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | 不區分大小寫之字元字串的資料類型|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | 多維度 cube 的資料類型|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | 從資料庫內連接到其他于 postgresql 資料庫|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | 整數的文字搜尋字典範本|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | 在地球表面上計算大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | 判斷字串之間的相似和距離|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | 用於儲存（索引鍵、值）組集合的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 于 postgresql 的假設索引|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | 一維整數陣列的函數、運算子和索引支援|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | 階層式樹狀結構的資料類型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 模擬商業 RDBMS 的函式和封裝子集的函數和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | 密碼編譯功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting 延伸模組|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | 顯示資料列層級的鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | 顯示元組層級統計資料|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | 檢查共用緩衝區快取|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 依時間或識別碼管理資料分割資料表的延伸模組|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | prewarm 關聯資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | 追蹤所有已執行之 SQL 語句的執行統計資料|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | 根據 trigrams 的文字相似性測量和索引搜尋|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL 程式語言|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometry、geography 和點陣空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL 函式|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tiger geocoder 和 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS 拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | 遠端于 postgresql 伺服器的外部資料包裝函式|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | 操作整個資料表的函數，包括交叉資料表|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | 移除重音的文字搜尋字典|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | 產生通用唯一識別碼（Uuid）|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements 擴充](https://www.postgresql.org/docs/current/pgstatstatements.html)功能會在每個適用於 PostgreSQL 的 Azure 資料庫伺服器上預先載入，以提供您追蹤 SQL 語句執行統計資料的方法。
控制延伸模組計入哪些陳述式的設定 `pg_stat_statements.track` 預設為 `top`，這表示會追蹤用戶端直接發出的所有陳述式。 其他兩個會追蹤層級 `none` 和 `all`。 這項設定是可透過 [Azure 入口網站](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 設定的伺服器參數。

pg_stat_statements 提供查詢執行資訊，不過會對於伺服器效能造成影響，因為它會記錄每個 SQL 陳述式。 如果您不主動使用 pg_stat_statements 延伸模組，我們建議您將 `pg_stat_statements.track` 設定為 `none`。 請注意，某些協力廠商監視服務可能會需要 pg_stat_statements 傳遞查詢效能深入解析，因此請確認這是否為您的情況。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html)和[postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html)可讓您從一個于 postgresql 伺服器連線到另一個，或連接到相同伺服器中的另一個資料庫。 接收端伺服器必須允許來自傳送端伺服器通過其防火牆的連線。 將這些延伸模組用於「適用於 PostgreSQL 的 Azure 資料庫」伺服器之間的連線時，可藉由將 [允許存取 Azure 服務] 設為 [開啟] 來執行此動作。 如果您想要使用延伸模組來回路到相同的伺服器，也需要執行此動作。 [允許存取 Azure 服務] 設定可在「適用於 PostgreSQL 的 Azure 資料庫」伺服器的 Azure 入口網站頁面中找到，位於 [連線安全性] 下方。 開啟 [允許存取 Azure 服務] 時，會將所有 Azure Ip 放在允許清單上。

目前不支援來自適用於 PostgreSQL 的 Azure 資料庫的輸出連線，但與其他適用於 PostgreSQL 的 Azure 資料庫伺服器的連接除外。

## <a name="uuid"></a>uuid
如果您打算 `uuid_generate_v4()` 從[uuid-ossp 擴充功能](https://www.postgresql.org/docs/current/uuid-ossp.html)中使用，請考慮與 pgcrypto 延伸模組的比較， `gen_random_uuid()` 以取得效能優勢。 [pgcrypto extension](https://www.postgresql.org/docs/current/pgcrypto.html)

## <a name="pgaudit"></a>pgAudit
[PgAudit 延伸](https://github.com/pgaudit/pgaudit/blob/master/README.md)模組提供會話和物件的 audit 記錄。 若要瞭解如何在適用於 PostgreSQL 的 Azure 資料庫中使用此延伸模組，請造訪[審核概念一文](concepts-audit.md)。 

## <a name="pg_prewarm"></a>pg_prewarm
Pg_prewarm 延伸模組會將關聯式資料載入快取中。 預先準備您的快取表示在重新開機之後，您的查詢在第一次執行時有更好的回應時間。 在 Postgres 10 和以下的中，預先準備是使用[prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)函式手動完成。

在 Postgres 11 和更新版本中，您可以將預先準備設定為[自動](https://www.postgresql.org/docs/current/pgprewarm.html)發生。 您必須在 `shared_preload_libraries` 參數的清單中包含 pg_prewarm，然後重新開機伺服器以套用變更。 您可以從 [ [Azure 入口網站](howto-configure-server-parameters-using-portal.md)]、[ [CLI](howto-configure-server-parameters-using-cli.md)]、[REST API] 或 [ARM] 範本設定參數。 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB 是一種時間序列資料庫，封裝為于 postgresql 的延伸模組。 TimescaleDB 針對時間序列工作負載提供時間導向的分析功能、優化和調整 Postgres。

[深入瞭解 TimescaleDB](https://docs.timescale.com/latest)，這是一項已註冊的[時間刻度，inc.](https://www.timescale.com/)的商標。 適用於 PostgreSQL 的 Azure 資料庫提供了開放原始碼版本的時間刻度。 若要瞭解此版本中有哪些可用的時間刻度功能，請參閱「[時間刻度」產品比較](https://www.timescale.com/products/)。

### <a name="installing-timescaledb"></a>安裝 TimescaleDB
若要安裝 TimescaleDB，您必須將它包含在伺服器的共用預先載入程式庫中。 變更 Postgres 的 `shared_preload_libraries` 參數需要**伺服器重新開機**才會生效。 您可以使用[Azure 入口網站](howto-configure-server-parameters-using-portal.md)或[Azure CLI](howto-configure-server-parameters-using-cli.md)來變更參數。

使用[Azure 入口網站](https://portal.azure.com/)：

1. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 在提要欄位上，選取 [**伺服器參數**]。

3. 搜尋 `shared_preload_libraries` 參數。

4. 選取 [ **TimescaleDB**]。

5. 選取 [**儲存**] 以保留您的變更。 儲存變更之後，您會收到通知。 

6. 在通知之後，請**重新開機**伺服器以套用這些變更。 若要了解如何重新啟動伺服器，請參閱[重新啟動適用於 PostgreSQL 的 Azure 資料庫伺服器](howto-restart-server-portal.md)。


您現在可以在 Postgres 資料庫中啟用 TimescaleDB。 連接到資料庫，併發出下列命令：
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 如果您看到錯誤，請確認您在儲存 shared_preload_libraries 之後[重新開機伺服器](howto-restart-server-portal.md)。 

您現在可以[從頭](https://docs.timescale.com/getting-started/creating-hypertables)開始建立 TimescaleDB hypertable，或[在於 postgresql 中遷移現有的時間序列資料](https://docs.timescale.com/getting-started/migrating-data)。

### <a name="restoring-a-timescale-database"></a>還原時間刻度資料庫
若要使用 pg_dump 和 pg_restore 還原時間刻度資料庫，您必須在目的地資料庫中執行兩個 helper 程式： `timescaledb_pre_restore()` 和 `timescaledb_post restore()` 。

先準備目的地資料庫：

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

現在，您可以在原始資料庫上執行 pg_dump，然後執行 pg_restore。 還原之後，請務必在還原的資料庫中執行下列命令：

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>後續步驟
如果您未看見想要使用的擴充功能，請讓我們知道。 在我們的[意見反應論壇](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)中投票現有要求或建立新的意見反應要求。
