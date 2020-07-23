---
title: 使用 Ruby 查詢
description: 本主題說明如何使用 Ruby 來建立連線到資料庫的程式，並使用 Transact-SQL 陳述式加以查詢。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 57e364d8cbd3cb9e1e2996786ee6414b7d7e04a8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504950"
---
# <a name="quickstart-use-ruby-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入門：使用 Ruby 在 Azure SQL Database 或 Azure SQL 受控執行個體中查詢資料庫
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

此快速入門示範如何使用 [Ruby](https://www.ruby-lang.org) 連線至資料庫，並使用 Transact-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您必須符合下列必要條件：

- 資料庫。 您可以使用其中一個快速入門，以建立資料庫並加以設定：

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
  
- 您的作業系統適用的 Ruby 與相關軟體：
  
  - **macOS**：安裝 Homebrew、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS。 請參閱[在 macOS 上使用 SQL Server 建立 Ruby 應用程式](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)中的步驟 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Ubuntu**：安裝 Ruby、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS 的必要項目。 請參閱[在 Ubuntu 上使用 SQL Server 建立 Ruby 應用程式](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)中的步驟 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Windows**：安裝 Ruby、Ruby Devkit 和 TinyTDS。 請參閱[設定 Ruby 開發的開發環境](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)。

## <a name="get-server-connection-information"></a>取得伺服器連線資訊

取得連線到 Azure SQL Database 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 瀏覽至 [SQL 資料庫] 或 [SQL 受控執行個體] 頁面。

3. 在 [概觀] 頁面上，針對 Azure SQL Database 中的資料庫檢閱 [伺服器名稱] 旁的完整伺服器名稱，若為 Azure SQL 受控執行個體或 Azure VM 上的 SQL Server，則檢閱 [主機] 旁的完整伺服器名稱 (或 IP 位址)。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製] 圖示。

> [!NOTE]
> 如需 Azure VM 上的 SQL Server 連線資訊，請參閱[連線到 SQL Server 執行個體](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)。

## <a name="create-code-to-query-your-database-in-azure-sql-database"></a>建立程式碼以查詢您在 Azure SQL Database 中的資料庫

1. 在文字或程式碼編輯器中，建立名為 *sqltest.rb* 的新檔案。

1. 加入下列程式碼。 將 `<server>`、`<database>`、`<username>` 和 `<password>` 取代為您 Azure SQL Database 資料庫中的值。

   >[!IMPORTANT]
   >此範例中的程式碼使用範例 AdventureWorksLT 資料，您可以在建立資料庫時選擇這些範例資料作為來源。 如果您的資料庫中有不同的資料，請在 SELECT 查詢中使用來自您自己資料庫的資料表。 

   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行下列命令：

   ```bash
   ruby sqltest.rb
   ```
   
1. 確認已從您的資料庫傳回前 20 個「類別/產品」資料列。 

## <a name="next-steps"></a>後續步驟
- [設計您在 Azure SQL Database 的第一個資料庫](design-first-database-tutorial.md)
- [適用於 TinyTDS 的 GitHub 存放庫](https://github.com/rails-sqlserver/tiny_tds)
- [回報或發問有關 TinyTDS 的問題](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby Driver for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
