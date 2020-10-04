---
title: 建立使用者-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明如何建立新的使用者帳戶，以與適用於 PostgreSQL 的 Azure 資料庫單一伺服器互動。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: 1dbbdde03d1c24882be298d8c81362744debeecf
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704934"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>在適用於 PostgreSQL 的 Azure 資料庫中建立使用者-單一伺服器

本文說明如何在適用於 PostgreSQL 的 Azure 資料庫伺服器內建立使用者。

如果您想要瞭解如何建立和管理 Azure 訂用帳戶使用者及其許可權，您可以造訪 [azure 角色型存取控制 (AZURE RBAC) 一文](../role-based-access-control/built-in-roles.md) ，或複習 [如何自訂角色](../role-based-access-control/custom-roles.md)。

## <a name="the-server-admin-account"></a>伺服器系統管理員帳戶

當您第一次建立「適用於 PostgreSQL 的 Azure 資料庫」時，您提供了伺服器管理使用者名稱和密碼。 如需詳細資訊，您可以遵循[快速入門](quickstart-create-server-database-portal.md)，查看逐步方法。 由於伺服器管理使用者名稱是自訂名稱，您可以從 Azure 入口網站找到所選的伺服器管理使用者名稱。

在適用於 PostgreSQL 的 Azure 資料庫伺服器建立時，系統會定義 3 個預設角色。 若要查看這些角色，可執行命令：`SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- 您伺服器的管理使用者

您伺服器的管理使用者是 azure_pg_admin 角色的成員。 不過，伺服器管理帳戶不屬於 azure_superuser 角色。 由於此服務是受控 PaaS 服務，只有 Microsoft 屬於進階使用者角色。

PostgreSQL 引擎會使用權限來控制資料庫物件的存取，如 [PostgreSQL 產品文件](https://www.postgresql.org/docs/current/static/sql-createrole.html)中所述。 在適用於 PostgreSQL 的 Azure 資料庫中，伺服器管理使用者會獲得這些權限：LOGIN、NOSUPERUSER、INHERIT、CREATEDB、CREATEROLE、NOREPLICATION

伺服器管理使用者帳戶可用來建立其他使用者，並同意這些使用者加入 azure_pg_admin 角色。 此外，伺服器管理帳戶可以用來建立較低權限的使用者和角色，以存取個別資料庫和結構描述。

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>如何在適用於 PostgreSQL 的 Azure 資料庫中建立其他管理使用者

1. 取得連線資訊和管理員使用者名稱。
   若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您可以從 Azure 入口網站的伺服器 [概觀]**** 或 [屬性]**** 頁面輕鬆尋找伺服器名稱和登入資訊。

2. 使用系統管理員帳戶和密碼來連線到資料庫伺服器。 使用您慣用的用戶端工具，例如 pgAdmin 或 psql。
   如果您不確定如何連線，請參閱[快速入門](./quickstart-create-server-database-portal.md)

3. 編輯並執行下列 SQL 程式碼。 以您新的使用者名稱取代預留位置值 < new_user >，並以您自己的強式密碼取代密碼預留位置。 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>如何在適用於 PostgreSQL 的 Azure 資料庫中建立資料庫使用者

1. 取得連線資訊和管理員使用者名稱。
   若要連線到您的資料庫伺服器，您需要完整伺服器名稱和系統管理員登入認證。 您可以從 Azure 入口網站的伺服器 [概觀]**** 或 [屬性]**** 頁面輕鬆尋找伺服器名稱和登入資訊。

2. 使用系統管理員帳戶和密碼來連線到資料庫伺服器。 使用您慣用的用戶端工具，例如 pgAdmin 或 psql。

3. 編輯並執行下列 SQL 程式碼。 將預留位置值 `<db_user>` 替換為您預定的新使用者名稱，並將預留位置值 `<newdb>` 替換為您自己的資料庫名稱。 以您自己的強式密碼取代密碼預留位置。

   這個 sql 程式碼語法會建立名為 testdb 的新資料庫作為範例。 然後在 PostgreSQL 服務中建立新的使用者，並將連線權限授與給該使用者的新資料庫。

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. 由於使用管理帳戶，您可能需要授與其他權限來保護資料庫中的物件。 請參閱 [PostgreSQL 文件](https://www.postgresql.org/docs/current/static/ddl-priv.html)，以深入了解資料庫角色和權限。 例如：

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. 指定委派的資料庫，使用新的使用者名稱和密碼來登入伺服器。 此範例會顯示 psql 命令列。 執行此命令時，系統會提示您輸入使用者名稱的密碼。 取代您自己的伺服器名稱、資料庫名稱和使用者名稱。

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>後續步驟

針對新使用者機器的 IP 位址開啟防火牆，讓使用者能夠連線：[使用 Azure 入口網站建立及管理適用於 PostgreSQL 的 Azure 資料庫防火牆規則](howto-manage-firewall-using-portal.md)或 [Azure CLI](howto-manage-firewall-using-cli.md)。

如需有關使用者帳戶管理的詳細資訊，請參閱 PostgreSQL 產品文件中的[資料庫角色與權限](https://www.postgresql.org/docs/current/static/user-manag.html)、[GRANT 語法](https://www.postgresql.org/docs/current/static/sql-grant.html)和[權限](https://www.postgresql.org/docs/current/static/ddl-priv.html)。
