---
title: '保護先前 SQL DW (專用的 SQL 集區) '
description: 保護專用 SQL 集區的秘訣 (先前為 SQL DW) ，以及在 Azure Synapse Analytics 中開發解決方案。
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: ce09488e2323aada5f99494ef3920681b685ec0b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453635"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中 (先前的 SQL DW) 保護專用的 SQL 集區

> [!div class="op_single_selector"]
>
> * [安全性概觀](sql-data-warehouse-overview-manage-security.md)
> * [驗證](sql-data-warehouse-authentication.md)
> * [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

本文將逐步引導您瞭解如何 (先前的 SQL DW) 保護您專用的 SQL 集區。 特別是，本文會讓您開始使用資源，以使用專用的 SQL 集區 (先前的 SQL DW) 來限制存取、保護資料，以及監視活動。

## <a name="connection-security"></a>連接安全性

「連線安全性」是指如何使用防火牆規則和連線加密，限制和保護資料庫的連線。

[邏輯 SQL server](../../azure-sql/database/logical-servers.md)及其資料庫都會使用防火牆規則，來拒絕未明確核准的 IP 位址的連線嘗試。 若要允許來自應用程式或用戶端機器之公用 IP 位址的連線，您必須先使用 Azure 入口網站、REST API 或 PowerShell 建立伺服器層級的防火牆規則。

最佳做法是，您應該盡可能限制可透過伺服器層級防火牆所允許的 IP 位址範圍。  若要從本機電腦存取專屬的 SQL 集區 (先前的 SQL DW) ，請確定您的網路和本機電腦上的防火牆允許 TCP 埠1433上的連出通訊。  

專用的 SQL 集區 (先前的 SQL DW) 會使用伺服器層級 IP 防火牆規則。 它不支援資料庫層級 IP 防火牆規則。 如需詳細資訊，請參閱 [Azure SQL Database 防火牆規則](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

預設會將您專用 SQL 集區的連接 (先前的 SQL DW) 加密。  停用加密的修改連線設定會被忽略。

## <a name="authentication"></a>驗證

「驗證」是指連線到資料庫時如何證明身分識別。 專用的 SQL 集區 (先前的 SQL DW) 目前支援使用使用者名稱和密碼 SQL Server 驗證，以及使用 Azure Active Directory。

當您為資料庫建立伺服器時，您已使用使用者名稱和密碼指定了「伺服器管理員」登入。 使用這些認證，您就可以透過 SQL Server 驗證，使用資料庫擁有者或 "dbo" 的身分驗證該伺服器上的任何資料庫。

不過，最佳作法是貴組織的使用者應該使用不同的帳戶來進行驗證。 因為萬一應用程式的程式碼容易受到 SQL 插入式攻擊，您就可以限制授與應用程式的權限，並降低惡意活動的風險。

若要建立 SQL Server 驗證使用者，請使用伺服器管理員登入連接到您伺服器上的 **master** 資料庫，並建立新的伺服器登入。  也最好在 master 資料庫中建立使用者。 在主要資料庫中建立使用者，可讓使用者使用類似 SSMS 的工具登入，而不用指定資料庫名稱。  它也可讓他們使用 [物件瀏覽器] 來查看伺服器上的所有資料庫。

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

然後，使用您的伺服器管理員登入，連接到您 **專用的 sql 集區 (先前的 SQL DW)** ，並根據您所建立的伺服器登入建立資料庫使用者。

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

若要提供使用者執行其他作業 (例如建立登入或建立新資料庫) 的權限，請為他們指派主要資料庫中的 `Loginmanager` 和 `dbmanager` 角色。

如需有關這些額外角色及向 SQL Database 驗證的詳細資訊，請參閱[管理 Azure SQL Database 的資料庫和登入](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。  如需使用 Azure Active Directory 連接的詳細資訊，請參閱 [使用 Azure Active Directory 驗證進行連接](sql-data-warehouse-authentication.md)。

## <a name="authorization"></a>授權

授權是指您在驗證和連接之後，可以在資料庫中進行的動作。 授權權限取決於角色成員資格和權限。 最好的作法是，您應該授與使用者所需的最低權限。 若要管理角色，您可以使用下列預存程序：

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

您所連線的伺服器管理員帳戶是 db_owner 的成員，有權限在資料庫中執行任何動作。 請儲存此帳戶，以便部署結構描述升級及其他管理作業。 請使用具更多有限權限的 "ApplicationUser" 帳戶，從應用程式連線到具應用程式所需之最低權限的資料庫。

有幾種方式可以進一步限制使用者可在資料庫中執行的動作：

* 細微的[權限](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)可讓您控制可對資料庫中的個別資料行、資料表、檢視、結構描述、程序和其他物件執行哪些作業。 使用細微權限，以擁有最大控制權，並授與所需的最小權限。
* 除了 db_datareader 和 db_datawriter 以外，[資料庫角色](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)均可以用來建立權力較大的應用程式使用者帳戶，或權力較小的管理帳戶。 內建固定資料庫角色提供簡單的方式來授與權限，但可能會導致授與的權限多於所需的權限。
* [預存程序](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 可用來限制對資料庫可採取的動作。

下列範例會針對使用者定義結構描述授予讀取權限。

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

從 Azure 入口網站或使用 Azure Resource Manager API 管理資料庫和伺服器，是由入口網站使用者帳戶的角色指派所控制。 如需詳細資訊，請參閱[使用 Azure 入口網站新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

## <a name="encryption"></a>加密

透明資料加密 (TDE) 可透過加密和解密待用資料來協助防止惡意活動的威脅。 當您加密資料庫時，相關聯的備份和交易記錄檔就會加密，完全不需要變更您的應用程式。 TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。

在 SQL Database 中，資料庫加密金鑰是由內建伺服器憑證保護。 每個伺服器的內建伺服器憑證都是唯一的。 Microsoft 會每隔 90 天自動輪換這些憑證。 使用的加密演算法是 AES-256。 如需 TDE 的一般描述，請參閱 [透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

您可以使用 [Azure 入口網站](sql-data-warehouse-encryption-tde.md)或 [T-SQL](sql-data-warehouse-encryption-tde-tsql.md) 將資料庫加密。

## <a name="next-steps"></a>後續步驟

如需使用不同通訊協定連接到您的倉儲的詳細資訊和範例，請參閱 [ (先前的 SQL DW) 連接到專用的 sql 集 ](sql-data-warehouse-connect-overview.md)區。
