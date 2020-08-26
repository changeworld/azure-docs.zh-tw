---
title: 使用 Node.js 查詢資料庫
description: 如何使用 Node.js 來建立程式，以連線到 Azure SQL Database 資料庫或 Azure SQL 受控執行個體，並使用 T-SQL 陳述式進行查詢。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-javascript
ms.openlocfilehash: c079d9bad15de6dac93cd9be23631786673bd009
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756379"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入門：使用 Node.js 在 Azure SQL Database 或 Azure SQL 受控執行個體中，查詢資料庫
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

在本快速入門中，您將使用 Node.js 連接到資料庫並查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

  | 動作 | SQL Database | SQL 受控執行個體 | Azure VM 上的 SQL Server |
  |:--- |:--- |:---|:---|
  | 建立| [入口網站](single-database-create-quickstart.md) | [入口網站](../managed-instance/instance-create-quickstart.md) | [入口網站](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 設定 | [伺服器層級 IP 防火牆規則](firewall-create-server-level-portal-quickstart.md)| [VM 的連線能力](../managed-instance/connect-vm-instance-configure.md)|
  |||[來自內部部署的連線](../managed-instance/point-to-site-p2s-configure.md) | [連線到 SQL Server 執行個體](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |載入資料|每個快速入門載入的 Adventure Works|[還原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [還原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的 [BACPAC](database-import.md) 檔案，還原或匯入 Adventure Works| 從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的 [BACPAC](database-import.md) 檔案，還原或匯入 Adventure Works|
  |||


- [Node.js](https://nodejs.org) 相關軟體

  # <a name="macos"></a>[macOS](#tab/macos)

  安裝 Homebrew 與 Node.js，然後使用[在 macOS 上使用 SQL Server 建立 Node.js 應用程式](https://www.microsoft.com/sql-server/developer-get-started/node/mac/) (英文) 中的步驟 **1.2** 和 **1.3**，安裝 ODBC 驅動程式和 SQLCMD。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  安裝 Node.js，然後使用[在 Ubuntu 上使用 SQL Server 建立 Node.js 應用程式](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/) (英文) 中的步驟 **1.2** 和 **1.3**，安裝 ODBC 驅動程式與 SQLCMD。

  # <a name="windows"></a>[Windows](#tab/windows)

  安裝 Chocolatey 和 Node.js，然後使用[在 Windows 上使用 SQL Server 建立 Node.js 應用程式](https://www.microsoft.com/sql-server/developer-get-started/node/windows/) (英文) 中的步驟 **1.2** 和 **1.3**，安裝 ODBC 驅動程式與 SQLCMD。

  ---

> [!IMPORTANT]
> 本文中已撰寫的指令碼會使用 **Adventure Works** 資料庫。

> [!NOTE]
> 您也可選擇使用 Azure SQL 受控執行個體。
>
> 若要建立及設定，請使用 [Azure 入口網站](../managed-instance/instance-create-quickstart.md)、[PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然後設定[內部部署](../managed-instance/point-to-site-p2s-configure.md)或 [VM](../managed-instance/connect-vm-instance-configure.md) 連線能力。
>
> 若要載入資料，請參閱[使用 BACPAC 檔案還原](database-import.md) (搭配 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works))，或參閱[還原 Wide World Importers 資料庫](../managed-instance/restore-sample-database-quickstart.md)。

## <a name="get-server-connection-information"></a>取得伺服器連線資訊

取得連線到 Azure SQL Database 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 前往 [SQL 資料庫] 或 [SQL 受控執行個體] 頁面。

3. 在 [概觀] 頁面上，針對 Azure SQL Database 中的資料庫檢閱 [伺服器名稱] 旁的完整伺服器名稱，若為 Azure SQL 受控執行個體或 Azure VM 上的 SQL Server，則檢閱 [主機] 旁的完整伺服器名稱 (或 IP 位址)。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製] 圖示。

> [!NOTE]
> 如需 Azure VM 上 SQL Server 的連線資訊，請參閱[連線到 SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server) (英文)。

## <a name="create-the-project"></a>建立專案

開啟命令提示字元，並建立名為 sqltest 的資料夾。 開啟您建立的資料夾，然後執行下列命令：

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>新增查詢資料庫的程式碼

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
> 程式碼範例會使用 Azure SQL Database 中的 **AdventureWorksLT** 範例資料庫。

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行程式。

    ```bash
    node sqltest.js
    ```

1. 請確認前 20 個資料列已傳回，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- [Microsoft Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- 使用 [.NET core](connect-query-dotnet-core.md)、[Visual Studio Code](connect-query-vscode.md) 或 [SSMS](connect-query-ssms.md) (僅 Windows 適用)，在 Windows/Linux/macOS 上進行連線和查詢

- [使用命令列以開始使用 Windows/Linux/macOS 上的 .NET Core](/dotnet/core/tutorials/using-with-xplat-cli)

- 使用 [.NET](design-first-database-csharp-tutorial.md) 或 [SSMS](design-first-database-tutorial.md)，在 Azure SQL Database 中設計您的第一個資料庫
