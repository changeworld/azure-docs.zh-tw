---
title: 使用 Node.js 查詢資料庫
description: 如何使用 Node.js 來建立可連線到 Azure SQL 資料庫的程式，並使用 T-SQL 陳述式加以查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768610"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>快速入門：使用 Node.js 查詢 Azure SQL 資料庫

在本快速入門中，您會使用 Node.js 來連線至 Azure SQL 資料庫，並使用 T-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL 資料庫](sql-database-single-database-get-started.md)
- [Node.js](https://nodejs.org) 相關軟體

  # <a name="macos"></a>[macOS](#tab/macos)

  安裝 Homebrew 和 Node.js，然後使用**在 macOS 上使用 SQL Server 上建立 Node.js 應用程式**中的 **1.2** 和 [1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/) 步驟來安裝 ODBC 驅動程式和 SQLCMD。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  安裝 Node.js，然後使用**在 Ubuntu 上使用 SQL Server 上建立 Node.js 應用程式**中的 **1.2** 和 [1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/) 步驟來安裝 ODBC 驅動程式和 SQLCMD。

  # <a name="windows"></a>[Windows](#tab/windows)

  安裝 Chocolatey 和 Node.js，然後使用**在 Windows 上使用 SQL Server 上建立 Node.js 應用程式**中的 **1.2** 和 [1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/) 步驟來安裝 ODBC 驅動程式和 SQLCMD。

  ---

> [!IMPORTANT]
> 本文中已撰寫的指令碼會使用 **Adventure Works** 資料庫。

> [!NOTE]
> 您也可以視情況選擇使用 Azure SQL 受控執行個體。
>
> 若要建立和設定，請使用 [Azure 入口網站](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然後設定[現場](sql-database-managed-instance-configure-p2s.md)或 [VM](sql-database-managed-instance-configure-vm.md) 連線能力。
>
> 若要載入資料，請參閱[使用 BACPAC 檔案還原](sql-database-import.md) (搭配 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works))，或參閱[還原 Wide World Importers 資料庫](sql-database-managed-instance-get-started-restore.md)。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線到 Azure SQL 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 移至 [SQL 資料庫]  或 [SQL 受控執行個體]  頁面。

3. 在 [概觀]  頁面上，針對單一資料庫檢閱 [伺服器名稱]  旁的完整伺服器名稱，若為受控執行個體，則檢閱 [主機]  旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製]  圖示。 

## <a name="create-the-project"></a>建立專案

開啟命令提示字元，並建立名為 sqltest  的資料夾。 開啟您建立的資料夾，然後執行下列命令：

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>新增程式碼以查詢資料庫

1. 在您慣用的文字編輯器中，建立新的檔案 *sqltest.js*。

1. 以下列程式碼取代其內容。 然後，為您的伺服器、資料庫、使用者和密碼新增適當的值。

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> 程式碼範例會使用適用於 Azure SQL 的 **AdventureWorksLT** 範例資料庫。

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行程式。

    ```bash
    node sqltest.js
    ```

1. 請確認前 20 個資料列已傳回，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- [Microsoft Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- 使用 [.NET core](sql-database-connect-query-dotnet-core.md)、[Visual Studio Code](sql-database-connect-query-vscode.md) 或 [SSMS](sql-database-connect-query-ssms.md) (僅 Windows 適用)，在 Windows/Linux/macOS 上進行連線和查詢

- [使用命令列以開始使用 Windows/Linux/macOS 上的 .NET Core](/dotnet/core/tutorials/using-with-xplat-cli)

- 使用 [.NET](sql-database-design-first-database-csharp.md) 或 [SSMS](sql-database-design-first-database.md) 設計您的第一個 Azure SQL 資料庫
