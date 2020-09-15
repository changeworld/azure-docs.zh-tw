---
title: 連線和查詢
description: 連線至 Azure SQL Database 快速入門，說明如何連線至 Azure SQL Database 和 Azure SQL 受控執行個體，及進行查詢。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 2a6aa0aa22fce5ba0f763f98fec37f607d0ce8a1
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441761"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Azure SQL Database 與 Azure SQL 受控執行個體連線及查詢的文章
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

以下文件包含的連結，可引導您進入 Azure 範例，了解如何連線至 Azure SQL Database 及 Azure SQL 受控執行個體，及進行查詢。 如需傳輸層安全性的相關建議，請參閱[資料庫連線的 TLS 考量](#tls-considerations-for-database-connectivity) (英文)。

## <a name="quickstarts"></a>快速入門

| 快速入門 | 描述 |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|此快速入門示範如何使用 SSMS 連線至資料庫，然後使用 Transact-SQL 陳述式來查詢、插入、更新及刪除資料庫中的資料。|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|此快速入門示範如何使用 Azure Data Studio 連線至資料庫，然後使用 Transact-SQL (T-SQL) 陳述式來建立 Azure Data Studio 教學課程中所使用的 TutorialDB。|
|[Azure 入口網站](connect-query-portal.md)|此快速入門會示範如何使用查詢編輯器連線至資料庫 (僅限 Azure SQL Database)，然後使用 Transact-SQL 陳述式來查詢、插入、更新及刪除資料庫中的資料。|
|[Visual Studio Code](connect-query-vscode.md)|此快速入門示範如何使用 Visual Studio Code 連線至資料庫，然後使用 Transact-SQL 陳述式來查詢、插入、更新及刪除資料庫中的資料。|
|[.NET 搭配 Visual Studio](connect-query-dotnet-visual-studio.md)|此快速入門示範如何搭配使用 .NET Framework 與 Visual Studio 來建立 C# 程式，以連線至資料庫，並使用 Transact-SQL 陳述式查詢資料。|
|[.NET Core](connect-query-dotnet-core.md)|此快速入門示範如何在 Windows/Linux/macOS 上使用 .NET Core 來建立 C# 程式，以連線至資料庫，並使用 Transact-SQL 陳述式查詢資料。|
|[Go](connect-query-go.md)|此快速入門示範如何使用「移至連接」進入資料庫。 也會示範用以查詢及修改資料的 TRANSACT-SQL 陳述式。|
|[Java](connect-query-java.md)|此快速入門示範如何使用 JAVA 連線至資料庫，然後使用 Transact-SQL 陳述式查詢資料。|
|[Node.js](connect-query-nodejs.md)|此快速入門示範如何使用 Node.js 建立程式，以連線至資料庫，並使用 Transact-SQL 陳述式查詢資料。|
|[PHP](connect-query-php.md)|此快速入門示範如何使用 PHP 建立程式，以連線至資料庫，並使用 Transact-SQL 陳述式查詢資料。|
|[Python](connect-query-python.md)|此快速入門示範如何使用 Python 連線至資料庫，並使用 Transact-SQL 陳述式查詢資料。 |
|[Ruby](connect-query-ruby.md)|此快速入門示範如何使用 Ruby 建立程式，以連線至資料庫，並使用 Transact-SQL 陳述式查詢資料。|
|[R](connect-query-r.md)|本快速入門示範如何搭配使用 R 與 Azure SQL Database 機器學習服務來建立程式，以連線至 Azure SQL Database 資料庫，並使用 Transact-SQL 陳述式查詢資料。|
|||

## <a name="tls-considerations-for-database-connectivity"></a>資料庫連線的 TLS 考量

Microsoft 針對連線至 Azure SQL Database 資料庫或 Azure SQL 受控執行個體所提供或支援的所有驅動程式，都使用傳輸層安全性 (TLS)。 不需要特別設定。 對於 SQL Server 執行個體、Azure SQL Database 資料庫或 Azure SQL 受控執行個體之執行個體的所有連線，都建議為所有應用程式設定下列組態或功能相同的設定：

- **Encrypt = 開啟**
- **TrustServerCertificate = 關閉**

某些系統會對這些組態關鍵字使用不同但對等的關鍵字。 這些組態可確保用戶端驅動程式會驗證從伺服器接收的 TLS 憑證的身分識別。

如果您需要符合支付卡產業 - 資料安全性標準 (PCI-DSS)，我們也建議在用戶端上停用 TLS 1.1 和 1.0。

根據預設，非 Microsoft 驅動程式可能不會使用 TLS。 這可能是連線至 Azure SQL Database 或 Azure SQL 受控執行個體時的一項因素。 具有內嵌驅動程式的應用程式可能無法讓您控制這些連線設定。 我們建議您在與敏感性資料互動的系統上使用這類驅動程式和應用程式之前，先檢查它們的安全性。

## <a name="libraries"></a>程式庫

您可以使用各種程式庫和架構，連線到 Azure SQL Database 或 Azure SQL 受控執行個體。 請查看我們的[快速入門教學課程](https://aka.ms/sqldev)來快速地開始使用程式設計語言，例如 C#、Java、Node.js、PHP 和 Python。 然後，在 Linux 或 Windows 上使用 SQL Server，或者在 macOS 上使用 Docker 建置應用程式。

下表列出連線程式庫或驅動程式，用戶端應用程式可以用來從各種不同的語言連線到內部部署或雲端上執行之 SQL Server 並使用。 您可以在 Linux、Windows 或 Docker 上使用它們，並將其用於連線到 Azure SQL Database、Azure SQL 受控執行個體及 Azure Synapse Analytics (前稱 SQL 資料倉儲)。

| Language | 平台 | 其他資源 | 下載 | 開始使用 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Microsoft ADO.NET for SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [下載](https://www.microsoft.com/net/download/) | [開始使用](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [下載](https://go.microsoft.com/fwlink/?linkid=852460) |  [開始使用](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [PHP SQL Driver for SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [下載](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [開始使用](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows、Linux、macOS | [Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server/) | [安裝](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [開始使用](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL 驅動程式](/sql/connect/python/python-driver-for-sql-server/) | 安裝選項： <br/> \* [pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \* [pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [開始使用](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [Ruby Driver for SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/) | [安裝](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [開始使用](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [Microsoft ODBC driver for SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [下載](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

下表列出物件關聯式對應 (ORM) 架構和 Web 架構的範例，用戶端應用程式可搭配 SQL Server、Azure SQL Database、Azure SQL 受控執行個體及 Azure Synapse Analytics 使用。 您可以在 Linux、Windows 或 Docker 上使用這些架構。

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

- 如需連線架構資訊，請參閱 [Azure SQL Database 連線架構](connectivity-architecture.md)。
- 尋找 [SQL Server 驅動程式](/sql/connect/sql-connection-libraries/)，用於從用戶端應用程式進行連線。
- 連線到 Azure SQL Database 或 Azure SQL 受控執行個體：
  - [使用 .NET (C#) 進行連線和查詢](connect-query-dotnet-core.md)
  - [使用 PHP 進行連線和查詢](connect-query-php.md)
  - [使用 Node.js 進行連線和查詢](connect-query-nodejs.md)
  - [使用 JAVA 進行連線和查詢](connect-query-java.md)
  - [使用 Python 進行連線和查詢](connect-query-python.md)
  - [使用 Ruby 進行連線和查詢](connect-query-ruby.md)
- 重試邏輯程式碼範例：
  - [使用 ADO.NET 復原連線][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [使用 PHP 復原連線][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
