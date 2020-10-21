---
title: 使用於 postgresql 延伸模組
description: 使用於 postgresql 延伸模組
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3b9c3c66e58ae51773a959aba0b2c76d97b44445
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309504"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>在啟用 Azure Arc 的于 postgresql 超大規模伺服器群組中使用於 postgresql 延伸模組

當您將于 postgresql 與延伸模組搭配使用時，其效果最佳。 事實上，我們自己超大規模功能的重要元素是 Microsoft 提供的擴充功能， `citus` 預設會安裝此延伸模組，讓 Postgres 能夠在多個節點之間透明地分區資料。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>擴充功能清單
除了中的擴充 [`contrib`](https://www.postgresql.org/docs/12/contrib.html) 功能，Azure Arc 啟用的于 postgresql 超大規模伺服器群組之容器中的擴充功能清單如下：
- `citus`，v：9。4
- `pg_cron`，v：1。2
- `plpgsql`，v：1。0
- `postgis`，v：3.0。2
- `plv8`，v：2.3.14

這份清單會發展出加班時間，而且會在本檔中公佈更新。 您尚無法將擴充功能新增至上面所列的延伸模組。

本指南將採用下列兩種擴充功能的案例：
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>管理延伸模組

### <a name="enable-extensions"></a>啟用擴充功能
屬於的延伸模組不需要此步驟 `contrib` 。
啟用擴充功能的命令的一般格式為：

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>在伺服器群組的建立期間啟用延伸模組：
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>在已經存在的實例上啟用延伸模組：
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>取得已啟用的擴充功能清單：
執行下列其中一個命令。

##### <a name="with-azure-data-cli-azdata"></a>使用 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]
```console
azdata arc postgres server show -n <server group name>
```
在輸出中，請注意您伺服器群組規格中的 engine\extensions 區段。 例如：
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>使用 kubectl
```console
kubectl describe postgresql-12s/postgres02
```
在輸出中，請注意您伺服器群組規格中的 engine\extensions 區段。 例如：
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>建立延伸模組：
使用您選擇的用戶端工具連接到您的伺服器群組，並執行標準于 postgreSQL 查詢：
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>取得在您的伺服器群組中建立的擴充功能清單：
使用您選擇的用戶端工具連接到您的伺服器群組，並執行標準于 postgreSQL 查詢：
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>從您的伺服器群組中卸載延伸模組：
使用您選擇的用戶端工具連接到您的伺服器群組，並執行標準于 postgreSQL 查詢：
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>使用 PostGIS 和 Pg_cron 擴充功能

### <a name="the-postgis-extension"></a>PostGIS 延伸模組

我們可以在現有的伺服器群組上啟用 PostGIS 延伸模組，或建立已啟用擴充功能的新延伸模組：

**在伺服器群組的建立期間啟用延伸模組：**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**在已經存在的實例上啟用延伸模組：**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

若要確認已安裝的擴充功能，請在使用您慣用的于 postgresql 用戶端工具（例如 Azure Data Studio）連接到實例之後，使用下列標準于 postgresql 命令：
```console
select * from pg_extension;
```

針對 PostGIS 範例，請先從 MIT 的城市研究部門取得 [範例資料](http://duspviz.mit.edu/tutorials/intro-postgis/) ，& 規劃。 `apt-get install unzip`使用 VM 進行測試時，您可能需要執行以安裝解壓縮。

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

讓我們連接到資料庫，並建立 PostGIS 延伸模組：

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> 如果您想要使用封裝中的其中一個延伸模組 `postgis` (例如，、 `postgis_raster` `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` ... ) 您需要先建立 postgis 延伸模組，然後再建立另一個延伸模組。 例如： `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

並建立架構：

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

現在，我們可以藉由將 coffee_shops 資料表散發，將 PostGIS 與 scale out 功能結合：

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

讓我們載入一些資料：

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

並 `geom` 使用 PostGIS 資料類型中正確編碼的緯度和經度填滿欄位 `geometry` ：

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

現在，我們可以列出最接近 MIT 的咖啡廳 (77 麻塞諸塞州的42.359055、-71.093500) ：

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>Pg_cron 擴充功能

`pg_cron`除了 PostGIS 以外，讓我們在於 postgresql 伺服器群組上啟用：

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

請注意，這會重新開機節點並安裝額外的延伸模組，這可能需要 2-3 分鐘的時間。

我們現在可以再次連接，並建立 `pg_cron` 擴充功能：

```sql
CREATE EXTENSION pg_cron;
```

基於測試目的，讓我們建立一個資料表，以 `the_best_coffee_shop` 從我們的先前資料表中取得隨機名稱 `coffee_shops` ，並設定資料表內容：

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

我們可以使用 `cron.schedule` 加上幾個 SQL 語句來取得亂數據表名稱 (請注意，使用臨時表來儲存分散式查詢結果) ，然後將它儲存在 `the_best_coffee_shop` ：

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

現在每分鐘一次，我們會得到不同的名稱：

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

如需語法的完整詳細資訊，請參閱 [PG_CRON 讀我檔案](https://github.com/citusdata/pg_cron) 。

>[!NOTE]
>不支援卸載 `citus` 延伸模組。 `citus`需要有此延伸模組，才能提供超大規模體驗。

## <a name="next-steps"></a>後續步驟：
- 閱讀有關[plv8](https://plv8.github.io/)的檔
- 閱讀有關[PostGIS](https://postgis.net/)的檔
- 閱讀檔 [`pg_cron`](https://github.com/citusdata/pg_cron)
