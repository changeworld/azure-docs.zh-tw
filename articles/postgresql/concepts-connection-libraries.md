---
title: 連接程式庫-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文描述數個程式庫和驅動程式，可供您在撰寫應用程式來連接及查詢適用於 PostgreSQL 的 Azure 資料庫單一伺服器時使用。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74768889"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫的連接程式庫-單一伺服器
本文所列出開發人員可用來開發應用程式的程式庫和驅動程式，以連線及查詢適用於 PostgreSQL 的 Azure 資料庫。

## <a name="client-interfaces"></a>用戶端介面
大部分用來連線到 PostgreSQL 伺服器的語言用戶端程式庫都是外部專案，而且是獨立發佈的。 Windows、Linux 和 Mac 平台支援列出的程式庫，以連接適用於 PostgreSQL 的 Azure 資料庫。 在「後續步驟」一節中將會列出幾個快速入門範例。

| **語言** | **用戶端介面** | **其他資訊** | **下載** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 相容 | [下載](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | 資料庫擴充功能 | [安裝](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm 封裝](https://www.npmjs.com/package/pg) | 單純的 JavaScript 非封鎖用戶端 | [安裝](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Type 4 JDBC 驅動程式 | [下載](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby 介面 | [下載](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 單純的 Go postgres 驅動程式 | [安裝](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET 資料提供者 | [下載](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC 驅動程式 | [下載](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | 主要的 C 語言介面 | 內含 |
| C++ | [libpqxx](http://pqxx.org/) | 新樣式的 C++ 介面 | [下載](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>後續步驟
請閱讀這些快速入門，以了解如何使用您選擇的語言來連線及查詢適用於 PostgreSQL 的 Azure 資料庫：

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md)  |  [Go](./connect-go.md) [ ](./connect-php.md) [ ](./connect-java.md)  |  [ ](./connect-csharp.md)Python node.js JAVA Ruby | PHP .net （c #） Go | 
