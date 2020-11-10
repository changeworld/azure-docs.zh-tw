---
title: 快速入門：使用 Python 連線 - 適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
description: 本快速入門提供 Python 程式碼範例，供您在連線至「適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器」及查詢其資料時使用。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331772"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>快速入門：使用 Python 來連線和查詢適用於 PostgreSQL 的 Azure 資料庫中的資料 - 單一伺服器

在本快速入門中，您將了解如何連線到適用於 PostgreSQL 的 Azure 資料庫單一伺服器上的資料庫，並執行 SQL 陳述式在 macOS、Ubuntu Linux 或 Windows 上使用 Python 查詢。

> [!TIP]
> 如果您想要使用 PostgreSQL 建置 Django 應用程式，請查看[使用 PostgreSQL 部署 Django Web 應用程式](../app-service/tutorial-python-postgresql-app.md)教學課程。


## <a name="prerequisites"></a>Prerequisites
在本快速入門中，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free)。
- 使用 [Azure 入口網站](./quickstart-create-server-database-portal.md)建立適用於 PostgreSQL 的 Azure 資料庫單一伺服器 <br/> 如果沒有，請使用 [Azure CLI](./quickstart-create-server-database-azure-cli.md)。
- 根據您使用的是公用或私人存取，完成下列 **其中一項** 動作以啟用連線。

  |動作| 連線方法|操作指南|
  |:--------- |:--------- |:--------- |
  | **設定防火牆規則** | 公用 | [入口網站](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **設定服務端點** | 公用 | [入口網站](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **設定私人連結** | 私人 | [入口網站](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9+ 或 3.4+。

- 最新 [pip](https://pip.pypa.io/en/stable/installing/) 套件安裝程式。
- 在終端機或命令提示字元視窗中使用 `pip install psycopg2`，安裝 [psycopg2](https://pypi.python.org/pypi/psycopg2/)。 如需詳細資訊，請參閱[安裝方式`psycopg2`](http://initd.org/psycopg/docs/install.html)。

## <a name="get-database-connection-information"></a>取得資料庫連線資訊
連線到適用於 PostgreSQL 的 Azure 資料庫需要完整的伺服器名稱和登入認證。 您可以從 Azure 入口網站中取得此資訊。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，搜尋並選取適用於 PostgreSQL 的 Azure 資料庫伺服器名稱。
1. 在伺服器的 [概觀] 頁面上，複製完整的 **伺服器名稱** 和 **管理使用者名稱** 。 完整的 **伺服器名稱** 的格式一律是 *\<my-server-name>. postgres.database.azure.com* ，而 **管理員使用者名稱** 的格式一律是 *\<my-admin-username>@\<my-server-name>* 。

   也需要您的管理員密碼。 如果您忘記密碼，可以在此頁面中重設密碼。

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="適用於 PostgreSQL 的 Azure 資料庫伺服器名稱":::

> [!IMPORTANT]
>  取代下列值：
>   - 將 `<server-name>` 和 `<admin-username>` 取代為您從 Azure 入口網站中複製的值。
>   - 將 `<admin-password>` 取代為伺服器密碼。
>   - 當您建立伺服器時，系統已自動建立名為 postgres *`<database-name>` 的預設資料庫* 。 您可以使用 SQL 命令來重新命名該資料庫或[建立新的資料庫](https://www.postgresql.org/docs/9.0/sql-createdatabase.html)。

## <a name="step-1-connect-and-insert-data"></a>步驟 1：連線和插入資料
下列程式碼範例會連線到您的「適用於 PostgreSQL 的 Azure 資料庫」資料庫，方式如下：
-  [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 函式，並使用 SQL **INSERT** 陳述式載入資料。
- [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函式會針對資料庫執行 SQL 查詢。

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

程式碼會在成功執行後產生下列輸出：

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="命令列輸出":::


[有任何問題嗎？請告訴我們](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>步驟 2:讀取資料
下列程式碼範例會連線到您的「適用於 PostgreSQL 的 Azure 資料庫」資料庫，方式如下：
- [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 搭配 SQL **SELECT** 陳述式讀取資料。
- [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) 會接受查詢並使用下列方式傳回用於反覆查詢的結果集：

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[有任何問題嗎？請告訴我們](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>步驟 3：更新資料
下列程式碼範例會使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 搭配 SQL **UPDATE** 陳述式以更新資料。

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[有任何問題嗎？請告訴我們](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>步驟 5：刪除資料
下列程式碼使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 搭配 SQL **DELETE** 陳述式來刪除您先前插入的詳細目錄項目。

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[有任何問題嗎？請告訴我們](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>清除資源

若要清除在此快速入門期間使用的所有資源，請使用下列命令刪除資源群組：

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用入口網站管理適用於 MySQL 的 Azure 資料庫伺服器](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [使用 CLI 管理適用於 MySQL 的 Azure 資料庫伺服器](./how-to-manage-server-cli.md)<br/>

[找不到您要找的項目嗎？請告訴我們。](https://aka.ms/postgres-doc-feedback)
