---
title: 使用 PHP 查詢
description: 如何使用 PHP 建立可連線到 Azure SQL Database 資料庫或 Azure SQL 受控執行個體的程式，並使用 T-SQL 陳述式加以查詢。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2 
ms.openlocfilehash: e8b9f12c57b2e4bf943e5fc9a9ebc2bbadef9ee9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "86504967"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>快速入門：使用 PHP 查詢 Azure SQL Database 中的資料庫
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文示範如何使用 [PHP](https://php.net/manual/en/intro-whatis.php) 連線至 Azure SQL Database 資料庫或 Azure SQL 受控執行個體。 您可以接著使用 T-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure SQL Database 或 Azure SQL 受控執行個體中的資料庫。 您可以使用其中一個快速入門，以建立資料庫並加以設定：

  | 動作 | SQL Database | SQL 受控執行個體 | Azure VM 上的 SQL Server |
  |:--- |:--- |:---|:---|
  | 建立| [入口網站](single-database-create-quickstart.md) | [入口網站](../managed-instance/instance-create-quickstart.md) | [入口網站](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 設定 | [伺服器層級 IP 防火牆規則](firewall-create-server-level-portal-quickstart.md)| [VM 的連線能力](../managed-instance/connect-vm-instance-configure.md)|
  |||[來自內部部署的連線](../managed-instance/point-to-site-p2s-configure.md) | [連線到 SQL Server 執行個體](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |載入資料|每個快速入門載入的 Adventure Works|[還原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [還原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 中的 [BACPAC](database-import.md) 檔案還原或匯入 Adventure Works| 從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的 [BACPAC](database-import.md) 檔案，還原或匯入 Adventure Works|
  |||




  > [!IMPORTANT]
  > 本文中已撰寫的指令碼會使用 Adventure Works 資料庫。 對於 SQL 受控執行個體，您必須將 Adventure Works 資料庫匯入執行個體資料庫中，或將本文中的指令碼修改為使用 Wide World Importers 資料庫。

- 針對您的作業系統安裝的 PHP 相關軟體：

  - **MacOS**：安裝 PHP、ODBC 驅動程式，然後安裝適用於 SQL Server 的 PHP 驅動程式。 請參閱[步驟 1、2 和 3](/sql/connect/php/installation-tutorial-linux-mac)。

  - **Linux**：安裝 PHP、ODBC 驅動程式，然後安裝 PHP Driver for SQL Server。 請參閱[步驟 1、2 和 3](/sql/connect/php/installation-tutorial-linux-mac)。

  - **Windows**：安裝 PHP for IIS Express 和 Chocolatey，然後安裝 ODBC 驅動程式和 SQLCMD。 請參閱[步驟 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/)。

## <a name="get-server-connection-information"></a>取得伺服器連線資訊

取得連線到 Azure SQL Database 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 巡覽至 [SQL 資料庫] 或 [SQL 受控執行個體] 頁面。

3. 在 [概觀] 頁面上，針對 Azure SQL Database 中的資料庫檢閱 [伺服器名稱] 旁的完整伺服器名稱，若為 Azure SQL 受控執行個體或 Azure VM 中的 SQL Server，則檢閱 [主機] 旁的完整伺服器名稱 (或 IP 位址)。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製] 圖示。

> [!NOTE]
> 如需 Azure VM 上的 SQL Server 連線資訊，請參閱[連線至 SQL Server 執行個體](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)。

## <a name="add-code-to-query-the-database"></a>新增程式碼以查詢資料庫

1. 在您慣用的文字編輯器中，建立新的檔案 *sqltest.php*。  

1. 以下列程式碼取代其內容。 然後，為您的伺服器、資料庫、使用者和密碼新增適當的值。

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行應用程式。

   ```bash
   php sqltest.php
   ```

1. 請確認前 20 個資料列已傳回，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- [設計您在 Azure SQL Database 的第一個資料庫](design-first-database-tutorial.md)
- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/)
- [回報問題或發問](https://github.com/Microsoft/msphpsql/issues)
- [重試邏輯範例：使用 PHP 復原連線至 Azure SQL](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
