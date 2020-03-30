---
title: 擴展 - Azure 資料庫，用於 PostgreSQL - 單個伺服器
description: 瞭解 Azure 資料庫中用於 PostgreSQL - 單伺服器的可用 Postgres 擴展
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201266"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>後資料庫中的後格雷SQL 擴展為 PostgreSQL - 單個伺服器
PostgreSQL 提供下列功能：使用擴充功能來擴充您的資料庫功能。 擴展將多個相關的 SQL 物件捆綁在一個包中，這些包可以使用單個命令從資料庫載入或刪除。 在資料庫中載入後，擴展的功能類似于內置功能。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 擴充功能
您必須先在資料庫中安裝 PostgreSQL 擴充功能，然後才能使用它們。 要安裝特定的擴展，請從 psql 工具運行 [CREATE 擴展](https://www.postgresql.org/docs/current/sql-createextension.html) 命令，將打包的物件載入到資料庫中。

PostgreSQL 的 Azure 資料庫支援如下所示的金鑰擴展子集。 此資訊也可以藉由執行 `SELECT * FROM pg_available_extensions;` 來取得。 不支援超出列出的擴展。 您不能在 Azure 資料庫中為 PostgreSQL 創建自己的擴展。

## <a name="postgres-11-extensions"></a>Postgres 11 擴展

以下擴展在 Azure 資料庫中提供，用於具有 Postgres 版本 11 的 PostgreSQL 伺服器。 

> [!div class="mx-tableFixed"]
> | **擴展**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | 位址標準化美國資料集示例|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 支援在 GIN 中索引常見資料類型|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 支援在 GiST 中索引常見資料類型|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 不區分大小寫的字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 多維多維多維資料集的資料類型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 從資料庫中連接到其他 PostgreSQL 資料庫|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 用於整數的文本搜索字典範本|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 計算地球表面的大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 確定字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 用於存儲（鍵、值）對集的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | 後資料庫的假設索引|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 函數、運算子和索引支援一維整數陣列|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 分層樹狀結構的資料類型|
> |[奧奧亞佩](https://github.com/orafce/orafce)                       | 3.7             | 類比商業 RDBMS 的功能和包子集的功能和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 加密功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting 擴展|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 顯示行級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 顯示元組級統計資訊|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區緩存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 按時間或 ID 管理分區表的擴展|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | 預熱關係資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 跟蹤執行的所有 SQL 語句的執行統計資訊|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 基於三圖的文本相似性測量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript （v8） 受信任的程式語言|
> |[郵後](https://www.postgis.net/)                      | 2.5.1           | 後GIS幾何、地理和柵格空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | 後GIS SFCGAL功能|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | 後GIS老虎地理編碼器和反向地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | 後GIS拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 用於遠端 PostgreSQL 伺服器的外資料包裝器|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 操作整個表的函數，包括交叉表|
> |[時間縮放b](https://docs.timescale.com/latest)                    | 1.3.2             | 支援可擴展的插入和複雜查詢的時間序列資料|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 刪除重音的文本搜索詞典|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | 生成普遍唯一識別碼 （UUD）|

## <a name="postgres-10-extensions"></a>Postgres 10 擴展 

以下擴展在 Azure 資料庫中提供，用於具有 Postgres 版本 10 的 PostgreSQL 伺服器。

> [!div class="mx-tableFixed"]
> | **擴展**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | 位址標準化美國資料集示例|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | 支援在 GIN 中索引常見資料類型|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | 支援在 GiST 中索引常見資料類型|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | 不區分大小寫的字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | 多維多維多維資料集的資料類型|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | 從資料庫中連接到其他 PostgreSQL 資料庫|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | 用於整數的文本搜索字典範本|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | 計算地球表面的大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | 確定字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | 用於存儲（鍵、值）對集的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 後資料庫的假設索引|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | 函數、運算子和索引支援一維整數陣列|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | 分層樹狀結構的資料類型|
> |[奧奧亞佩](https://github.com/orafce/orafce)                       | 3.7             | 類比商業 RDBMS 的功能和包子集的功能和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | 加密功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting 擴展|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | 顯示行級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | 顯示元組級統計資訊|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | 檢查共用緩衝區緩存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 按時間或 ID 管理分區表的擴展|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | 預熱關係資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | 跟蹤執行的所有 SQL 語句的執行統計資訊|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | 基於三圖的文本相似性測量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript （v8） 受信任的程式語言|
> |[郵後](https://www.postgis.net/)                      | 2.4.3           | 後GIS幾何、地理和柵格空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | 後GIS SFCGAL功能|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | 後GIS老虎地理編碼器和反向地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | 後GIS拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | 用於遠端 PostgreSQL 伺服器的外資料包裝器|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | 操作整個表的函數，包括交叉表|
> |[時間縮放b](https://docs.timescale.com/latest)                    | 1.1.1             | 支援可擴展的插入和複雜查詢的時間序列資料|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | 刪除重音的文本搜索詞典|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | 生成普遍唯一識別碼 （UUD）|

## <a name="postgres-96-extensions"></a>Postgres 9.6 擴展 

以下擴展在 Azure 資料庫中提供，用於具有 Postgres 版本 9.6 的 PostgreSQL 伺服器。

> [!div class="mx-tableFixed"]
> | **擴展**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | 位址標準化美國資料集示例|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | 支援在 GIN 中索引常見資料類型|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | 支援在 GiST 中索引常見資料類型|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | 不區分大小寫的字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | 多維多維多維資料集的資料類型|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | 從資料庫中連接到其他 PostgreSQL 資料庫|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | 用於整數的文本搜索字典範本|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | 計算地球表面的大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | 確定字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | 用於存儲（鍵、值）對集的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 後資料庫的假設索引|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | 函數、運算子和索引支援一維整數陣列|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | 分層樹狀結構的資料類型|
> |[奧奧亞佩](https://github.com/orafce/orafce)                       | 3.7             | 類比商業 RDBMS 的功能和包子集的功能和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | 加密功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting 擴展|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | 顯示行級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | 顯示元組級統計資訊|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | 檢查共用緩衝區緩存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 按時間或 ID 管理分區表的擴展|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | 預熱關係資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | 跟蹤執行的所有 SQL 語句的執行統計資訊|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | 基於三圖的文本相似性測量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL程式語言|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript （v8） 受信任的程式語言|
> |[郵後](https://www.postgis.net/)                      | 2.3.2           | 後GIS幾何、地理和柵格空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | 後GIS SFCGAL功能|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | 後GIS老虎地理編碼器和反向地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | 後GIS拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | 用於遠端 PostgreSQL 伺服器的外資料包裝器|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | 操作整個表的函數，包括交叉表|
> |[時間縮放b](https://docs.timescale.com/latest)                    | 1.1.1             | 支援可擴展的插入和複雜查詢的時間序列資料|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | 刪除重音的文本搜索詞典|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | 生成普遍唯一識別碼 （UUD）|

## <a name="postgres-95-extensions"></a>Postgres 9.5 擴展 

以下擴展在 Azure 資料庫中提供，用於具有 Postgres 版本 9.5 的 PostgreSQL 伺服器。

> [!div class="mx-tableFixed"]
> | **擴展**| **延伸模組版本** | **描述** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | 用來將位址剖析為組成項目。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | 位址標準化美國資料集示例|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | 支援在 GIN 中索引常見資料類型|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | 支援在 GiST 中索引常見資料類型|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | 自動加密密碼的資料類型|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | 不區分大小寫的字串的資料類型|
> |[立方體](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | 多維多維多維資料集的資料類型|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | 從資料庫中連接到其他 PostgreSQL 資料庫|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | 用於整數的文本搜索字典範本|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | 計算地球表面的大圓距離|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | 確定字串之間的相似性和距離|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | 用於存儲（鍵、值）對集的資料類型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | 後資料庫的假設索引|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | 函數、運算子和索引支援一維整數陣列|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | 國際產品編號標準的資料類型|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | 分層樹狀結構的資料類型|
> |[奧奧亞佩](https://github.com/orafce/orafce)                       | 3.7             | 類比商業 RDBMS 的功能和包子集的功能和運算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | 提供審核功能|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | 加密功能|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting 擴展|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | 顯示行級鎖定資訊|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | 顯示元組級統計資訊|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | 檢查共用緩衝區緩存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 按時間或 ID 管理分區表的擴展|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | 預熱關係資料|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | 跟蹤執行的所有 SQL 語句的執行統計資訊|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | 基於三圖的文本相似性測量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL程式語言|
> |[郵後](https://www.postgis.net/)                      | 2.3.0           | 後GIS幾何、地理和柵格空間類型和函數|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | 後GIS SFCGAL功能|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | 後GIS老虎地理編碼器和反向地理編碼器|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | 後GIS拓撲空間類型和函數|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | 用於遠端 PostgreSQL 伺服器的外資料包裝器|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | 操作整個表的函數，包括交叉表|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | 刪除重音的文本搜索詞典|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | 生成普遍唯一識別碼 （UUD）|


## <a name="pg_stat_statements"></a>pg_stat_statements
[pg_stat_statements擴展](https://www.postgresql.org/docs/current/pgstatstatements.html)在 PostgreSQL 伺服器的每個 Azure 資料庫上預載入，以為您提供跟蹤 SQL 語句的執行統計資訊的方法。
控制延伸模組計入哪些陳述式的設定 `pg_stat_statements.track` 預設為 `top`，這表示會追蹤用戶端直接發出的所有陳述式。 其他兩個會追蹤層級 `none` 和 `all`。 這項設定是可透過 [Azure 入口網站](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 設定的伺服器參數。

pg_stat_statements 提供查詢執行資訊，不過會對於伺服器效能造成影響，因為它會記錄每個 SQL 陳述式。 如果您不主動使用 pg_stat_statements 延伸模組，我們建議您將 `pg_stat_statements.track` 設定為 `none`。 請注意，某些協力廠商監視服務可能會需要 pg_stat_statements 傳遞查詢效能深入解析，因此請確認這是否為您的情況。

## <a name="dblink-and-postgres_fdw"></a>閃爍和postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html)和[postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html)允許您從一個 PostgreSQL 伺服器連接到另一個伺服器，或連接到同一伺服器中的另一個資料庫。 接收端伺服器必須允許來自傳送端伺服器通過其防火牆的連線。 將這些延伸模組用於「適用於 PostgreSQL 的 Azure 資料庫」伺服器之間的連線時，可藉由將 [允許存取 Azure 服務] 設為 [開啟] 來執行此動作。 如果您想要使用延伸模組來回路到相同的伺服器，也需要執行此動作。 [允許存取 Azure 服務] 設定可在「適用於 PostgreSQL 的 Azure 資料庫」伺服器的 Azure 入口網站頁面中找到，位於 [連線安全性] 下方。 打開"允許訪問 Azure 服務"，將所有 Azure IP 放在允許清單中。

目前，除了與 PostgreSQL 伺服器的其他 Azure 資料庫的連接外，不支援來自 PostgreSQL 的 Azure 資料庫的出站連接。

## <a name="uuid"></a>uuid
如果您計畫使用`uuid_generate_v4()` [uuid-ossp 擴展](https://www.postgresql.org/docs/current/uuid-ossp.html)，請考慮與[pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) `gen_random_uuid()`擴展進行比較，以獲得性能優勢。

## <a name="pgaudit"></a>pgAudit
[pgAudit 擴展](https://github.com/pgaudit/pgaudit/blob/master/README.md)提供會話和物件稽核記錄記錄。 要瞭解如何在 PostgreSQL 的 Azure 資料庫中使用此擴展，請訪問[審核概念一文](concepts-audit.md)。 

## <a name="pg_prewarm"></a>pg_prewarm
pg_prewarm擴展將關係資料載入到緩存中。 預預熱緩存意味著查詢在重新開機後首次運行時具有更好的回應時間。 在 Postgres 10 及以下，預熱是使用[預熱函數](https://www.postgresql.org/docs/10/pgprewarm.html)手動完成的。

在 Postgres 11 及以上，您可以將預熱配置為[自動](https://www.postgresql.org/docs/current/pgprewarm.html)發生。 您需要在`shared_preload_libraries`參數清單中包含pg_prewarm，然後重新開機伺服器以應用更改。 可以從[Azure 門戶](howto-configure-server-parameters-using-portal.md)[、CLI、REST](howto-configure-server-parameters-using-cli.md)API 或 ARM 範本設置參數。 

## <a name="timescaledb"></a>時尺度DB
時平DB是一個時間序列資料庫，打包為 PostgreSQL 的擴展。 TimescaleDB 提供面向時間的分析功能、優化，並針對時間序列工作負載擴展 Postgres。

[瞭解更多關於](https://docs.timescale.com/latest)TimescaleDB，Timescale，Inc.的[Timescale, Inc.](https://www.timescale.com/)注冊商標。 用於 PostgreSQL 的 Azure 資料庫提供時間刻度的開源版本。 要瞭解此版本中提供哪些時間刻度功能，請參閱[時標產品比較](https://www.timescale.com/products/)。

### <a name="installing-timescaledb"></a>安裝時量DB
要安裝時間級DB，您需要將其包含在伺服器的共用預載入庫中。 對 Postgres 參數的`shared_preload_libraries`更改需要**重新開機伺服器**才能生效。 可以使用[Azure 門戶](howto-configure-server-parameters-using-portal.md)或[Azure CLI](howto-configure-server-parameters-using-cli.md)更改參數。

使用[Azure 門戶](https://portal.azure.com/)：

1. 選取適用於 PostgreSQL 的 Azure 資料庫伺服器。

2. 在側邊欄上，選擇**伺服器參數**。

3. 搜尋 `shared_preload_libraries` 參數。

4. 選擇**時間刻度 DB**。

5. 選擇 **"保存"** 以保留更改。 保存更改後，您會收到通知。 

6. 通知後，**重新開機**伺服器以應用這些更改。 若要了解如何重新啟動伺服器，請參閱[重新啟動適用於 PostgreSQL 的 Azure 資料庫伺服器](howto-restart-server-portal.md)。


您現在可以在 Postgres 資料庫中啟用時間級 DB。 連接到資料庫併發出以下命令：
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 如果發現錯誤，請確認在保存shared_preload_libraries後[重新開機伺服器](howto-restart-server-portal.md)。 

您現在可以[從頭開始](https://docs.timescale.com/getting-started/creating-hypertables)創建時間尺度DB超表，或在[PostgreSQL 中遷移現有的時間序列資料](https://docs.timescale.com/getting-started/migrating-data)。

### <a name="restoring-a-timescale-database"></a>還原時間刻度資料庫
要使用pg_dump和pg_restore還原時間刻度資料庫，需要在目標資料庫中運行兩個協助程式過程：`timescaledb_pre_restore()`和`timescaledb_post restore()`。

首先準備目標資料庫：

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

現在，您可以在原始資料庫上運行pg_dump，然後執行pg_restore。 還原後，請確保在還原的資料庫中運行以下命令：

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>後續步驟
如果您未看見想要使用的擴充功能，請讓我們知道。 在我們的[回饋論壇](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)中投票請求或創建新的回饋請求。
