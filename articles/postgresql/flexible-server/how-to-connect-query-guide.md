---
title: 連接和查詢彈性的伺服器于 postgresql
description: 快速入門的連結，示範如何連接到您的適用於 PostgreSQL 的 Azure 資料庫彈性伺服器並執行查詢。
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364556"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>適用于于 postgresql 的 Azure 資料庫連線及查詢總覽-彈性伺服器

下列檔包含範例的連結，示範如何使用適用於 PostgreSQL 的 Azure 資料庫單一伺服器進行連接和查詢。 本指南也包含 TLS 建議和延伸模組，可讓您用來連接到下列支援語言的伺服器。

>[!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器處於 **預覽階段**。

## <a name="quickstarts"></a>快速入門

| 快速入門 | 描述 |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|您可以使用 pgadmin 來連接到伺服器，並簡化資料庫物件的建立、維護和使用。|
|[Azure Cloud Shell 中的 psql](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|本文說明如何在 [Azure Cloud Shell](../../cloud-shell/overview.md)中執行 [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) ，以連接到您的伺服器，然後執行語句來查詢、插入、更新和刪除資料庫中的資料。如果已安裝在開發環境中，您可以執行 **psql**|
|[Python](connect-python.md)|本快速入門示範如何使用 Python 來連接到資料庫，並使用工作與資料庫物件來查詢資料。 |
|[App Service 的 Django](tutorial-django-app-service-postgres.md)|本教學課程示範如何使用 Ruby 來建立程式，以連接到資料庫，並使用工作與資料庫物件來查詢資料。|

## <a name="tls-considerations-for-database-connectivity"></a>資料庫連線的 TLS 考量

 (TLS) 的傳輸層安全性，由 Microsoft 所提供或支援的所有驅動程式用來連接到適用於 PostgreSQL 的 Azure 資料庫中的資料庫。 不需要特殊設定，但會針對新建立的伺服器強制使用 TLS 1.2。 如果您使用 TLS 1.0 和1.1，則建議您補救伺服器的 TLS 版本。 瞭解 [如何設定 TLS](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>PostgreSQL 擴充功能

PostgreSQL 提供下列功能：使用擴充功能來擴充您的資料庫功能。 延伸模組會將多個相關 SQL 物件統合在同一個套件之中，而且只要一個命令，就能載入或從資料庫移除。 載入到資料庫之後，延伸模組就像內建功能一樣。

- [Postgres 12 擴充功能](./concepts-extensions.md#postgres-12-extensions)
- [Postgres 11 擴充功能](./concepts-extensions.md#postgres-11-extensions)
- [dblink 和 postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

如需詳細資訊，請參閱 [如何在彈性的伺服器上使用於 postgresql 延伸](concepts-extensions.md)模組。

## <a name="next-steps"></a>後續步驟

- [使用傾印和還原來遷移資料](../howto-migrate-using-dump-and-restore.md)
- [使用匯入和匯出來遷移資料](../howto-migrate-using-export-and-import.md)
