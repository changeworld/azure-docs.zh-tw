---
title: 延伸 = 超縮放(Citus) - 用於後格雷SQL的 Azure 資料庫
description: 描述透過 Azure 資料庫中的延伸來擴充資料庫的功能,用於 PostgreSQL - 超大規模 (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d0798c77135b15e26c9787d9844cd9525cf12c5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532012"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 資料庫中的後格雷SQL 擴展,用於後格雷SQL和超大規模(Citus)

PostgreSQL 提供了透過使用擴展來擴展資料庫功能的功能。 擴充功能可在單一封裝中一併統合多個相關的 SQL 物件，其可使用單一命令從您的資料庫加以載入或移除。 在資料庫中載入後,擴展可以像內置功能一樣運行。 有關 PostgreSQL 擴充的詳細資訊,請參閱 [將相關物件來到延伸中](https://www.postgresql.org/docs/current/static/extend-extensions.html)。

## <a name="use-postgresql-extensions"></a>使用 PostgreSQL 擴充

您必須先在資料庫中安裝 PostgreSQL 擴充功能，然後才能使用它們。 要安裝特定擴展,請從 psql 工具執行 [CREATE 擴展](https://www.postgresql.org/docs/current/static/sql-createextension.html) 命令,將打包的物件載入到資料庫中。

用於 PostgreSQL - 超大規模 (Citus) 的 Azure 資料庫當前支援此處列出的密鑰擴展的子集。 不支援列出的擴展以外的擴展。 不能使用 Azure 資料庫為 PostgreSQL 創建自己的擴展。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫支援的擴充功能

下表列出適用於 PostgreSQL 的 Azure 資料庫目前支援的標準 PostgreSQL 擴充功能。 此資訊也可以藉由執行 `SELECT * FROM pg_available_extensions;` 來取得。

### <a name="data-types-extensions"></a>資料類型擴充功能

> [!div class="mx-tableFixed"]
> | **分機** | **說明** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | 提供不區分大小寫的字元字串類型。 |
> | [立方體](https://www.postgresql.org/docs/current/static/cube.html) | 提供多維度 Cube 的資料類型。 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | 提供用於存儲鍵值對集的數據類型。 |
> | [赫爾](https://github.com/citusdata/postgresql-hll) | 提供超日誌數據結構。 |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | 提供國際產品編號標準的資料類型。 |
> | [洛](https://www.postgresql.org/docs/current/lo.html) | 大型對象維護。 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | 提供階層式樹狀結構的資料類型。 |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | 用於表示線段或浮點間隔的數據類型。 |
> | [托普](https://github.com/citusdata/postgresql-topn/) | 類型為頂 n JSONB。 |

### <a name="full-text-search-extensions"></a>全文檢索搜尋擴充功能

> [!div class="mx-tableFixed"]
> | **分機** | **說明** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | 提供整數的文字搜尋字典範本。 |
> | [迪克特\_·辛](https://www.postgresql.org/docs/current/dict-xsyn.html) | 用於擴展同義詞處理的文本搜索字典範本。 |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | 文字搜尋字典，可從詞彙中移除重音符號 (變音符號)。 |

### <a name="functions-extensions"></a>函數擴充功能

> [!div class="mx-tableFixed"]
> | **分機** | **說明** |
> |---|---|
> | [自動公司](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | 用於自動遞增欄位的函數。 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | 提供方法來計算地球表面上的大圓距離。 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | 提供數個函數來判斷字串之間的相似性與距離。 |
> | [插入\_使用者名稱](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | 用於追蹤更改表的人員的功能。 |
> | [因塔格](https://www.postgresql.org/docs/current/intagg.html) | 整數聚合器和枚舉器(已過時)。 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | 提供函數和運算子來操作無 null 的整數陣列。 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | 用於追蹤上次修改時間的函數。 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | 提供密碼編譯函式。 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 依時間或識別碼管理分割的資料表。 |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | 提供可根據三併詞比對判斷英數文字相似度的函式和運算子。 |
> | [雷菲特](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | 實現參考完整性的功能(已過時)。 |
> | 工作階段\_分析 | 用於查詢 hstore 數位的函數。 |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | 提供操縱整個資料表 (包括交叉資料表) 的函式。 |
> | [Tcn](https://www.postgresql.org/docs/current/tcn.html) | 觸發的更改通知。 |
> | [時間旅行](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | 實現時間差的功能。 |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | 產生通用唯一識別碼 (UUID)。 |

### <a name="hyperscale-extensions"></a>超大規模擴展

> [!div class="mx-tableFixed"]
> | **分機** | **說明** |
> |---|---|
> | [西圖斯](https://github.com/citusdata/citus) | Citus 分散式資料庫。 |
> | 階層\_元再平衡器 | 在添加或刪除節點的情況下,安全地重新平衡伺服器組中的數據。 |

### <a name="index-types-extensions"></a>索引型態延伸

> [!div class="mx-tableFixed"]
> | **分機** | **說明** |
> |---|---|
> | [綻放](https://www.postgresql.org/docs/current/bloom.html) | 綻放存取方法 - 基於簽名檔的索引。 |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | 提供範例 GIN 運算元類,這些類為某些數據類型實現類似 B 樹的行為。 |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | 提供可實作 B 型樹狀結構的 GiST 索引運算子類別。 |

### <a name="language-extensions"></a>語言延伸模組

> [!div class="mx-tableFixed"]
> | **分機** | **說明** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL 可載入的程序性語言。 |

### <a name="miscellaneous-extensions"></a>其他擴充功能

> [!div class="mx-tableFixed"]
> | **分機** | **說明** |
> |---|---|
> | [管理員包](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL 的管理功能。 |
> | [阿姆切克](https://www.postgresql.org/docs/current/amcheck.html) | 用於驗證關係完整性的功能。 |
> | [檔案\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | 用於平面文件訪問的外數據包裝器。 |
> | [頁檢查](https://www.postgresql.org/docs/current/pageinspect.html) | 在較低級別檢查資料庫頁的內容。 |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | 提供一種方法，即時檢查共用緩衝區快取中發生的狀況。 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | PostgreSQL 的工作計畫程式。 |
> | [pg\_自由空間圖](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 檢查可用空間圖 (FSM)。 |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | 提供一種方式，來將關聯資料載入至緩衝區快取。 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | 提供一種方法，來追蹤伺服器所執行之所有 SQL 陳述式的執行統計資料。 有關此擴展的資訊,請參閱"pg_stat_statements"部分。 |
> | [pg\_可見度](https://www.postgresql.org/docs/current/pgvisibility.html) | 檢查可見性對應 (VM) 和頁面級可見性資訊。 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | 提供方法來顯示資料列層級鎖定的資訊。 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | 提供方法來顯示 Tuple 層級統計資料。 |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | 可用來存取儲存於外部 PostgreSQL 伺服器之資料的外部資料包裝函式。 有關此擴展的資訊,請參閱"閃爍和postgres_fdw"部分。|
> | [斯裡福林](https://www.postgresql.org/docs/current/sslinfo.html) | 有關 SSL 證書的資訊。 |
> | [tsm\_\_系統列](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE 方法,該方法接受行數作為限制。 |
> | [tsm\_\_系統時間](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE 方法,該方法接受以毫秒為單位的時間作為限制。 |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | 提供方法建立不使用 CPU 或磁碟的假設索引。 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 支援從資料庫工作階段連線至其他 PostgreSQL 資料庫的模組。 有關此擴展的資訊,請參閱"閃爍和postgres_fdw"部分。 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath 查詢和 XSLT。 |


### <a name="postgis-extensions"></a>PostGIS 擴充功能

> [!div class="mx-tableFixed"]
> | **分機** | **說明** |
> |---|---|
> | [PostGIS](https://www.postgis.net/)、postgis\_topology、postgis\_tiger\_geocoder、postgis\_sfcgal | 適用於 PostgreSQL 的空間與地理物件。 |
> | address\_standardizer、address\_standardizer\_data\_us | 用來將位址剖析為組成項目。 用來支援對位址進行地理編碼的正規化步驟。 |
> | 后吉斯\_斯夫格爾 | 後GIS SFCGAL功能。 |
> | 後吉斯\_\_老虎 地理編碼器 | 後GIS老虎地理編碼器和反向地理編碼器。 |
> | 後拓撲\_拓撲 | 後GIS拓撲空間類型和函數。 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[pg\_stat\_語句延伸](https://www.postgresql.org/docs/current/pgstatstatements.html)在 PostgreSQL 伺服器的每個 Azure 資料庫上預載入,為您提供追蹤 SQL 語句的執行統計資訊的方法。

該設置`pg_stat_statements.track`控制擴展計數的語句。 它預設為`top`,這意味著用戶端直接發佈的所有語句將被跟蹤。 其他兩個會追蹤層級 `none` 和 `all`。 這項設定是可透過 [Azure 入口網站](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 設定的伺服器參數。

pg_stat_statements提供的查詢執行資訊與記錄每個 SQL 語句時對伺服器性能的影響之間存在權衡。 如果您沒有主動使用pg_stat_statements延伸,我們建議您設定為`pg_stat_statements.track``none`。 某些第三方監視服務可能依賴pg_stat_statements來提供查詢性能見解,因此請確認是否屬於您。

## <a name="dblink-and-postgres_fdw"></a>閃爍和postgres_fdw
您可以使用 dblink 和 postgres_fdw從一個 PostgreSQL 伺服器連接到另一個伺服器,或連接到同一伺服器中的另一個資料庫。 接收端伺服器必須允許來自傳送端伺服器通過其防火牆的連線。 要使用這些延伸在 PostgreSQL 伺服器的 Azure 資料庫之間連線,請設定**允許對 ON 的 Azure 服務進行存取**。 如果要使用擴展迴圈回同一伺服器,還需要打開此設置。 **"允許存取 Azure 服務**"設定可以在**連接安全**下的 Postgres 伺服器的 Azure 門戶頁中找到。 在白名單中打開**允許訪問 Azure 服務**,將列出所有 Azure IP。

目前,除了與 PostgreSQL 伺服器的其他 Azure 資料庫的連接外,不支援來自 PostgreSQL 的 Azure 資料庫的出站連接。
