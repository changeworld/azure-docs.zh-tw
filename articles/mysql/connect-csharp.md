---
title: 快速入門：使用 C# 連線 - 適用於 MySQL 的 Azure 資料庫
description: 本快速入門提供 C# (.NET) 程式碼範例，您可用於從 Azure Database for MySQL 連線及查詢資料。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 96a32b615da9b9e5549233489ba74bf859236d9a
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427950"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql"></a>快速入門：使用 .NET (C#) 來連線及查詢適用於 MySQL 的 Azure 資料庫中的資料

本快速入門示範如何使用 C# 應用程式來連線到適用於 MySQL 的 Azure 資料庫。 它會顯示如何使用 SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。 

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

[有任何問題嗎？請告訴我們](https://aka.ms/mysql-doc-feedback)

## <a name="create-a-c-project"></a>建立 C# 專案
在命令提示字元中執行︰

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>取得連線資訊
取得連線到 Azure Database for MySQL 所需的連線資訊。 您需要完整的伺服器名稱和登入認證。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後搜尋您所建立的伺服器 (例如 **mydemoserver**)。
3. 按一下伺服器名稱。
4. 從伺服器的 [概觀] 面板，記下 [伺服器名稱] 和 [伺服器管理員登入名稱]。 如果您忘記密碼，您也可以從此面板重設密碼。
 :::image type="content" source="./media/connect-csharp/1_server-overview-name-login.png" alt-text="Azure Database for MySQL 伺服器名稱":::

## <a name="step-1-connect-and-insert-data"></a>步驟 1：連線和插入資料
使用下列程式碼搭配 `CREATE TABLE` 和 `INSERT INTO` SQL 陳述式來連線及載入資料。 程式碼會使用 `MySqlConnection` 類別的方法：
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync)，用以建立 MySQL 的連線。
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand)，可設定 CommandText 屬性
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync)，用以執行資料庫命令。 

將 `Server`、`Database`、`UserID` 和 `Password` 參數取代為您在建立伺服器和資料庫時所指定的值。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[有任何問題嗎？請告訴我們](https://aka.ms/mysql-doc-feedback)


## <a name="step-2-read-data"></a>步驟 2:讀取資料

使用下列程式碼搭配 `SELECT` SQL 陳述式來連線和讀取資料。 程式碼會使用 `MySqlConnection` 類別搭配下列方法：
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync)，用以建立 MySQL 的連線。
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand)，用以設定 CommandText 屬性。
- [ExecuteReaderAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync)，用以執行資料庫命令。 
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync)，用以前往結果中的記錄。 接下來程式碼會使用 GetInt32 和 GetString 來剖析記錄中的值。


將 `Server`、`Database`、`UserID` 和 `Password` 參數取代為您在建立伺服器和資料庫時所指定的值。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[有任何問題嗎？請告訴我們](https://aka.ms/mysql-doc-feedback)

## <a name="step-3-update-data"></a>步驟 3：更新資料
使用下列程式碼搭配 `UPDATE` SQL 陳述式來連線和讀取資料。 程式碼會使用 `MySqlConnection` 類別搭配下列方法：
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync)，用以建立 MySQL 的連線。 
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand)，用以設定 CommandText 屬性
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync)，用以執行資料庫命令。 


將 `Server`、`Database`、`UserID` 和 `Password` 參數取代為您在建立伺服器和資料庫時所指定的值。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[有任何問題嗎？請告訴我們](https://aka.ms/mysql-doc-feedback)

## <a name="step-4-delete-data"></a>步驟 4：刪除資料
使用下列程式碼搭配 `DELETE` SQL 陳述式來連線及刪除資料。 

程式碼會使用 `MySqlConnection` 類別搭配下列方法
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync)，用以建立 MySQL 的連線。
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand)，用以設定 CommandText 屬性。
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync)，用以執行資料庫命令。 


將 `Server`、`Database`、`UserID` 和 `Password` 參數取代為您在建立伺服器和資料庫時所指定的值。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
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
