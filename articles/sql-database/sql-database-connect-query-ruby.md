---
title: 使用 Ruby 查詢
description: 本主題說明如何使用 Ruby 來建立連線到 Azure SQL Database 的程式，並使用 Transact-SQL 陳述式查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 4bef55b049ee542efdb9d72d13fa196c989c75ec
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826917"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>快速入門：使用 Ruby 查詢 Azure SQL 資料庫

此快速入門示範如何使用 [Ruby](https://www.ruby-lang.org) 連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您必須符合下列必要條件：

- Azure SQL 資料庫。 您可以使用其中一個快速入門，在 Azure SQL Database 中建立資料庫並加以設定：

  || 單一資料庫 | 受控執行個體 |
  |:--- |:--- |:---|
  | 建立| [入口網站](sql-database-single-database-get-started.md) | [入口網站](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 設定 | [伺服器層級 IP 防火牆規則](sql-database-server-level-firewall-rule.md)| [VM 的連線能力](sql-database-managed-instance-configure-vm.md)|
  |||[現場的連線能力](sql-database-managed-instance-configure-p2s.md)
  |載入資料|每個快速入門載入的 Adventure Works|[還原 Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 中的 [BACPAC](sql-database-import.md) 檔案還原或匯入 Adventure Works|
  |||

  > [!IMPORTANT]
  > 本文中已撰寫的指令碼會使用 Adventure Works 資料庫。 對於受控執行個體，您必須將 Adventure Works 資料庫匯入執行個體資料庫中，或將本文中的指令碼修改為使用 Wide World Importers 資料庫。
  
- 您的作業系統適用的 Ruby 與相關軟體：
  
  - **MacOS**：安裝 Homebrew、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS。 請參閱[在 macOS 上使用 SQL Server 建立 Ruby 應用程式](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)中的步驟 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Ubuntu**：安裝 Ruby、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS 的必要項目。 請參閱[在 Ubuntu 上使用 SQL Server 建立 Ruby 應用程式](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)中的步驟 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Windows**：安裝 Ruby、Ruby Devkit 和 TinyTDS。 請參閱[設定 Ruby 開發的開發環境](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線到 Azure SQL 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 瀏覽至 [SQL 資料庫]  或 [SQL 受控執行個體]  頁面。

3. 在 [概觀]  頁面上，針對單一資料庫檢閱 [伺服器名稱]  旁的完整伺服器名稱，若為受控執行個體，則檢閱 [主機]  旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製]  圖示。 

## <a name="create-code-to-query-your-sql-database"></a>建立程式碼以查詢您的 SQL Database

1. 在文字或程式碼編輯器中，建立名為 *sqltest.rb* 的新檔案。
   
1. 加入下列程式碼。 將 `<server>`、`<database>`、`<username>` 和 `<password>` 取代為您 Azure SQL 資料庫中的值。
   
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
- [設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database.md)。
- [適用於 TinyTDS 的 GitHub 存放庫](https://github.com/rails-sqlserver/tiny_tds)。
- [回報或發問有關 TinyTDS 的問題](https://github.com/rails-sqlserver/tiny_tds/issues)。
- [Ruby Driver for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)。
