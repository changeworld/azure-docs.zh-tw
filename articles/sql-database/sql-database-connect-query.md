---
title: 連線及查詢快速入門
description: Azure SQL Database 快速入門說明如何連線及查詢 Azure SQL 資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 05ba54c10cc693f81115f364d7a400e2275a3875
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80528223"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>快速入門：Azure SQL Database 連線和查詢

以下文件包含的連結可將您引導至 Azure 範例，以了解如何連線及查詢 Azure SQL 資料庫。 它也會提供傳輸層安全性的一些建議。

## <a name="quickstarts"></a>快速入門

| |  |
|---|---|
|[Transact-SQL](sql-database-connect-query-ssms.md)|此快速入門示範如何使用 SSMS 來連線至 Azure SQL 資料庫，然後使用 Transact-SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|此快速入門示範如何使用 Azure Data Studio 連線至 Azure SQL 資料庫，然後使用 Transact-SQL (T-SQL) 陳述式建立 Azure Data Studio 教學課程中使用的 TutorialDB。|
|[Azure 入口網站](sql-database-connect-query-portal.md)|此快速入門會示範如何使用查詢編輯器來連線至 SQL Database，然後使用 Transact-SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|此快速入門示範如何使用 Visual Studio Code 來連線至 Azure SQL 資料庫，然後使用 Transact-SQL 陳述式來查詢、插入、更新和刪除資料庫中的資料。|
|[.NET 搭配 Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|此快速入門示範如何搭配使用 .NET Framework 與 Visual Studio 建立 C# 程式以連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|此快速入門示範如何在 Windows/Linux/macOS 上使用 .NET Core 建立 C# 程式以連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。|
|[Go](sql-database-connect-query-go.md)|此快速入門示範如何使用 Go 連線至 Azure SQL 資料庫。 也會示範用以查詢及修改資料的 TRANSACT-SQL 陳述式。|
|[Java](sql-database-connect-query-java.md)|此快速入門示範如何使用 JAVA 來連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。|
|[Node.js](sql-database-connect-query-nodejs.md)|此快速入門示範如何使用 Node.js 建立程式以連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。|
|[PHP](sql-database-connect-query-php.md)|此快速入門示範如何使用 PHP 建立程式以連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。|
|[Python](sql-database-connect-query-python.md)|此快速入門示範如何使用 Python 來連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。 |
|[Ruby](sql-database-connect-query-ruby.md)|此快速入門示範如何使用 Ruby 建立程式以連線至 Azure SQL 資料庫，並使用 Transact-SQL 陳述式來查詢資料。|
|[R](sql-database-connect-query-r.md)|本快速入門示範如何搭配使用 R 與 Azure SQL Database 機器學習服務來建立連線至 Azure SQL 資料庫的程式，並使用 Transact-SQL 陳述式來查詢資料。|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>SQL Database 連線的 TLS 考量
Microsoft 針對連線至 Azure SQL Database 提供或支援的所有驅動程式都會使用傳輸層安全性 (TLS)。 不需要特別設定。 對於 SQL Server 或 Azure SQL Database 的所有連線，我們建議所有應用程式設定下列組態或其對等項目：

 - **Encrypt = 開啟**
 - **TrustServerCertificate = 關閉**

某些系統會對這些組態關鍵字使用不同但對等的關鍵字。 這些組態可確保用戶端驅動程式會驗證從伺服器接收的 TLS 憑證的身分識別。

如果您需要符合支付卡產業 - 資料安全性標準 (PCI-DSS)，我們也建議在用戶端上停用 TLS 1.1 和 1.0。

根據預設，非 Microsoft 驅動程式可能不會使用 TLS。 這可能是連線至 Azure SQL Database 時的一個因素。 具有內嵌驅動程式的應用程式可能無法讓您控制這些連線設定。 我們建議您在與敏感性資料互動的系統上使用這類驅動程式和應用程式之前，先檢查它們的安全性。

## <a name="libraries"></a>程式庫

您可以使用各種程式庫和架構來連線到 Azure SQL Database。 請查看我們的[快速入門教學課程](https://aka.ms/sqldev)來快速地開始使用程式設計語言，例如 C#、Java、Node.js、PHP 和 Python。 然後，在 Linux 或 Windows 上使用 SQL Server，或者在 macOS 上使用 Docker 建置應用程式。

下表列出連線程式庫或驅動程式  ，用戶端應用程式可以用來從各種不同的語言連線到內部部署或雲端上執行之 SQL Server 並使用。 您可以在 Linux、Windows 或 Docker 上使用它們，並使用它們來連線到 Azure SQL Database 和 Azure SQL 資料倉儲。 

| Language | 平台 | 其他資源 | 下載 | 開始使用 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Microsoft ADO.NET for SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [下載](https://www.microsoft.com/net/download/) | [開始使用](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [Microsoft JDBC Driver for SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [下載](https://go.microsoft.com/fwlink/?linkid=852460) |  [開始使用](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [PHP SQL Driver for SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [下載](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [開始使用](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows、Linux、macOS | [Node.js Driver for SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [安裝](https://msdn.microsoft.com/library/mt652094.aspx) |  [開始使用](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL 驅動程式](https://msdn.microsoft.com/library/mt652092.aspx) | 安裝選項： <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [開始使用](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [Ruby Driver for SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [安裝](https://msdn.microsoft.com/library/mt711041.aspx) | [開始使用](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [Microsoft ODBC driver for SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [下載](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

下表列出物件關聯式對應 (ORM) 架構和 Web 架構的範例，用戶端應用程式可搭配內部部署或雲端上執行之 SQL Server 使用。 您可以在 Linux、Windows 或 Docker 上使用架構，並使用它們來連線到 SQL Database 和 SQL 資料倉儲。 

| Language | 平台 | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows、Linux、macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>後續步驟

- 如需連線架構資訊，請參閱 [Azure SQL Database 連線架構](sql-database-connectivity-architecture.md)。
- 尋找 [SQL Server 驅動程式](https://msdn.microsoft.com/library/mt654049.aspx)，用於從用戶端應用程式連線
- 連線到 SQL Database：
  - [使用 .NET (C#) 連接到 SQL Database](sql-database-connect-query-dotnet.md) 
  - [使用 PHP 連接到 SQL Database](sql-database-connect-query-php.md) 
  - [使用 Node.js 連接到 SQL Database](sql-database-connect-query-nodejs.md) 
  - [使用 Java 連接到 SQL Database](sql-database-connect-query-java.md) 
  - [使用 Python 連接到 SQL Database](sql-database-connect-query-python.md)
  - [使用 Ruby 連接到 SQL Database](sql-database-connect-query-ruby.md)
- 重試邏輯程式碼範例：
  - [使用 ADO.NET 彈性地連接到 SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [使用 PHP 彈性地連接到 SQL][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
