---
title: 遷移資料庫 - 用於 PostgreSQL 的 Azure 資料庫 - 單個伺服器
description: 描述如何將 PostgreSQL 資料庫擷取到指令碼檔案，並從該檔案將資料匯入目標資料庫。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770198"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>使用匯出和匯入來移轉 PostgreSQL 資料庫
您可以使用 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 將 PostgreSQL 資料庫擷取到指令碼檔案，並使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 從該檔案將資料匯入目標資料庫。

## <a name="prerequisites"></a>Prerequisites
若要逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)，而且防火牆規則要允許存取其中的資料庫。
- 安裝 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 命令列公用程式
- 安裝 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 命令列公用程式

請遵循下列步驟來匯出和匯入 PostgreSQL 資料庫。

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>使用 pg_dump 建立指令碼檔案，其中包含要載入的資料
若要將內部部署或 VM 中的現有 PostgreSQL 資料庫匯出至 sql 指令碼檔，請在您現有的環境中執行下列命令︰
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
例如，如果其中有一個本機伺服器和一個稱為**testdb**的資料庫：
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>在目標「適用於 PostgreSQL 的 Azure 資料庫」上匯入資料
您可以使用 psql 命令列和 --dbname 參數 (-d)，將資料匯入適用於 PostgreSQL 的 Azure 資料庫 ，並從 sql 檔案載入資料。
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
此範例會使用 psql 公用程式和上一個步驟中名為 **testdb.sql** 的指令碼檔案，將資料匯入目標伺服器 **mydemoserver.postgres.database.azure.com** 上的資料庫 **mypgsqldb** 中。
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>後續步驟
- 要使用轉儲和還原遷移 PostgreSQL 資料庫，請參閱[使用轉儲遷移 PostgreSQL 資料庫並還原](howto-migrate-using-dump-and-restore.md)。
- 如需有關將資料庫移轉至適用於 PostgreSQL 的 Azure 資料庫詳細資訊，請參閱[資料庫移轉指南](https://aka.ms/datamigration)。 
