---
title: 連接和查詢-單一伺服器于 postgresql
description: Azure My SQL Database 快速入門的連結，顯示如何連線至您的伺服器並執行查詢。
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 924dbadc07f57e5928ecc63a24bf5e57d6213670
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934199"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>適用于于 postgresql 的 Azure 資料庫的連線和查詢總覽-單一伺服器

下列檔包含範例的連結，示範如何使用適用於 PostgreSQL 的 Azure 資料庫單一伺服器進行連接和查詢。 本指南也包含 TLS 建議和延伸模組，可讓您用來連接到下列支援語言的伺服器。

## <a name="quickstarts"></a>快速入門

| 快速入門 | 描述 |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|您可以使用 pgadmin 來連接到伺服器，並簡化資料庫物件的建立、維護和使用。|
|[Azure Cloud Shell 中的 psql](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|本文說明如何在[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)中執行[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) ，以連接到您的伺服器，然後執行語句來查詢、插入、更新和刪除資料庫中的資料。如果已安裝在開發環境中，您可以執行**psql**|
|[VS Code 的于 postgresql](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|適用于 VS Code (Preview) 的 Azure 資料庫擴充功能可讓您使用 scrapbooks 搭配豐富的 Intellisense，在本機和雲端中流覽和查詢于 postgresql 伺服器。 |
|[PHP](connect-php.md)|本快速入門示範如何使用 PHP 來建立程式，以連接到資料庫，並使用工作與資料庫物件來查詢資料。|
|[Java](connect-java.md)|本快速入門示範如何使用 JAVA 來連接到資料庫，然後使用 [處理資料庫物件] 來查詢資料。|
|[Node.js](connect-nodejs.md)|本快速入門示範如何使用 Node.js 來建立程式，以連接到資料庫，並使用工作與資料庫物件來查詢資料。|
|[.NET (C # ) ](connect-csharp.md)|本快速入門示範如何 use.NET (c # ) 來建立 c # 程式，以連接到資料庫，並使用工作與資料庫物件來查詢資料。|
|[Go](connect-go.md)|此快速入門示範如何使用「移至連接」進入資料庫。 也會示範用以查詢及修改資料的 TRANSACT-SQL 陳述式。|
|[Python](connect-python.md)|本快速入門示範如何使用 Python 來連接到資料庫，並使用工作與資料庫物件來查詢資料。 |
|[Ruby](connect-ruby.md)|本快速入門示範如何使用 Ruby 來建立程式，以連接到資料庫，並使用工作與資料庫物件來查詢資料。|


## <a name="tls-considerations-for-database-connectivity"></a>資料庫連線的 TLS 考量

 (TLS) 的傳輸層安全性，由 Microsoft 所提供或支援的所有驅動程式用來連接到適用於 PostgreSQL 的 Azure 資料庫中的資料庫。 不需要特殊設定，但會針對新建立的伺服器強制使用 TLS 1.2。 如果您使用 TLS 1.0 和1.1，則建議您補救伺服器的 TLS 版本。 瞭解 [ 如何設定 TLS](howto-tls-configurations.md)


## <a name="postgresql-extensions"></a>于 postgresql 延伸模組
PostgreSQL 提供下列功能：使用擴充功能來擴充您的資料庫功能。 延伸模組會將多個相關 SQL 物件統合在同一個套件之中，而且只要一個命令，就能載入或從資料庫移除。 載入到資料庫之後，延伸模組就像內建功能一樣。

- [Postgres 11 擴充功能](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-11-extensions)
- [Postgres 10 擴充功能](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-10-extensions)
- [Postgres 9.6 擴充功能](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-96-extensions)
- [Postgres 9.5 擴充功能](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-95-extensions)

如需詳細資訊，請參閱 [如何在單一伺服器上使用於 postgresql 延伸](concepts-extensions.md)模組。

## <a name="next-steps"></a>後續步驟 

- [使用傾印和還原來遷移資料](howto-migrate-using-dump-and-restore.md)
- [使用匯入和匯出來遷移資料](howto-migrate-using-export-and-import.md)
