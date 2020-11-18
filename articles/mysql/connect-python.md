---
title: 快速入門：使用 Python 連線 - 適用於 MySQL 的 Azure 資料庫
description: 本快速入門提供數個 Python 程式碼範例，您可用於從 Azure Database for MySQL 連線和查詢資料。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 12452367de0e8f936d30387df709d5d2779bfcb1
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427627"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>快速入門：使用 Python 連線和查詢適用於 MySQL 的 Azure 資料庫中的資料

在本快速入門中，您將使用 Python 連線至適用於 MySQL 的 Azure 資料庫。 接著，您可以使用 SQL 陳述式來查詢、插入、更新和刪除 Mac、Ubuntu Linux 和 Windows 平台中的資料庫所含的資料。 

## <a name="prerequisites"></a>Prerequisites
在本快速入門中，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free)。
- 使用 [Azure 入口網站](./quickstart-create-mysql-server-database-using-azure-portal.md)建立適用於 MySQL 的 Azure 資料庫單一伺服器 <br/> 如果沒有，請使用 [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)。
- 根據您使用的是公用或私人存取，完成下列 **其中一項** 動作以啟用連線。

   |動作| 連線方法|操作指南|
   |:--------- |:--------- |:--------- |
   | **設定防火牆規則** | 公用 | [入口網站](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **設定服務端點** | 公用 | [入口網站](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **設定私人連結** | 私人 | [入口網站](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [建立資料庫和非管理使用者](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>安裝 Python 和 MySQL 連接器

使用下列步驟，在您的電腦上安裝 Python 和適用於 Python 的 MySQL 連接器： 

> [!NOTE]
> 本快速入門使用 [MySQL 連接器/Python 開發人員指南](https://dev.mysql.com/doc/connector-python/en/)。

1. 為您的作業系統下載並安裝 [Python 3.7 或更新版本](https://www.python.org/downloads/)。 請務必將 Python 新增至您的 `PATH`，因為 MySQL 連接器有此需要。
   
2. 開啟命令提示字元或 `bash` 殼層，並以大寫 V 參數執行 `python -V`，以檢查您的 Python 版本。
   
3. 最新版的 Python 中會包含 `pip` 套件安裝程式。 請執行 `pip install -U pip`，以將 `pip` 更新為最新版本。 
   
   若未安裝 `pip`，您可以使用 `get-pip.py` 加以下載並安裝。 如需詳細資訊，請參閱[安裝](https://pip.pypa.io/en/stable/installing/)。 
   
4. 使用 `pip` 安裝 Python 的 MySQL 連接器及其相依性：
   
   ```bash
   pip install mysql-connector-python
   ```
   
[有任何問題嗎？請告訴我們](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>取得連線資訊

從 Azure 入口網站取得連線至適用於 MySQL 的 Azure 資料庫所需的連線資訊。 您需要伺服器名稱、資料庫名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
   
1. 在入口網站的搜尋列中，搜尋並選取您所建立的「適用於 MySQL 的 Azure 資料庫」伺服器，例如 **mydemoserver**。
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL 伺服器名稱":::
   
1. 在伺服器的 [概觀] 頁面上，記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。 如果您忘記密碼，您也可以從此頁面重設密碼。
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="適用於 MySQL 的 Azure 資料庫伺服器名稱 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>步驟 1：建立資料表及插入資料

使用下列程式碼搭配 **INSERT** SQL 陳述式連線至伺服器和資料庫、建立資料表，並載入資料。此程式碼會匯入 mysql.connector 程式庫，並使用下列方法：
- [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) 函式，可使用 config 集合中的[引數](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html)連線至適用於 MySQL 的 Azure 資料庫。 
- [cursor.execute](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 方法會對 MySQL 資料庫執行 SQL 查詢。 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) (當您的游標使用完畢時)。
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html)，用以關閉連線。

> [!IMPORTANT]
> - 依預設會啟用 SSL。 您可能需要下載 [DigiCertGlobalRootG2 SSL 憑證](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)，才能從本機環境連線。
> - 請將 `<mydemoserver>`、`<myadmin>`、`<mypassword>` 和 `<mydatabase>` 預留位置取代為您的 MySQL 伺服器和資料庫的值。

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [ClientFlag.SSL],
  'ssl_cert': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[有任何問題嗎？請告訴我們](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>步驟 2:讀取資料

使用下列程式碼搭配 **SELECT** SQL 陳述式來連線和讀取資料。 此程式碼會匯入 mysql.connector 程式庫，並使用 [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 方法對 MySQL 資料庫執行 SQL 查詢。 

此程式碼會使用 [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) 方法來讀取資料列，並將結果集保存在集合資料列中，然後使用 `for` 迭代器對資料列執行迴圈。

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>步驟 3：更新資料

使用下列程式碼搭配 **UPDATE** SQL 陳述式來連線和更新資料。 此程式碼會匯入 mysql.connector 程式庫，並使用 [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 方法對 MySQL 資料庫執行 SQL 查詢。 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>步驟 4：刪除資料

使用下列程式碼搭配 **DELETE** SQL 陳述式來連線和移除資料。 此程式碼會匯入 mysql.connector 程式庫，並使用 [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) 方法對 MySQL 資料庫執行 SQL 查詢。 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

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
> [使用 CLI 管理適用於 MySQL 的 Azure 資料庫伺服器](./how-to-manage-single-server-cli.md)

[找不到您要找的項目嗎？請告訴我們。](https://aka.ms/mysql-doc-feedback)
