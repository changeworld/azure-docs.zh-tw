---
title: 保護資料庫
description: 在 SQL 分析的 SQL 池資源中保護資料庫和開發解決方案的提示。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 6ae9580803563971764eec4bd18fcc2430cee06d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350194"
---
# <a name="secure-a-database-in-azure-synapse"></a>在 Azure 同步器中保護資料庫
> [!div class="op_single_selector"]
> * [安全概述](sql-data-warehouse-overview-manage-security.md)
> * [驗證][](sql-data-warehouse-authentication.md)
> * [加密 (入口網站)](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

本文將介紹在 SQL Analytics 中保護您的 SQL 池的基礎知識。 特別是，本文將介紹使用使用 SQL 池預配的資料庫上的訪問、保護資料和監視活動的資源。

## <a name="connection-security"></a>連接安全性
「連線安全性」是指如何使用防火牆規則和連線加密，限制和保護資料庫的連線。

伺服器和資料庫都使用防火牆規則來拒絕未明確白名單的 IP 位址的連接嘗試。 若要允許來自應用程式或用戶端機器之公用 IP 位址的連線，您必須先使用 Azure 入口網站、REST API 或 PowerShell 建立伺服器層級的防火牆規則。 

最好的作法是，您應該盡可能限制允許穿透您伺服器防火牆的 IP 位址範圍。  要從本地電腦訪問 SQL 池，請確保網路上的防火牆和本地電腦上的防火牆允許在 TCP 埠 1433 上進行傳出通信。  

Azure 同步分析使用伺服器級 IP 防火牆規則。 它不支援資料庫級 IP 防火牆規則。 有關詳細資訊，請參閱 Azure [SQL 資料庫防火牆規則](../../sql-database/sql-database-firewall-configure.md)

預設情況下，與 SQL 池的連接是加密的。  停用加密的修改連線設定會被忽略。

## <a name="authentication"></a>驗證
「驗證」是指連線到資料庫時如何證明身分識別。 SQL 池當前支援 SQL 伺服器身份驗證，使用使用者名和密碼以及 Azure 活動目錄。 

當您為資料庫建立邏輯伺服器時，採取使用者名稱和密碼指定了「伺服器管理員」登入。 使用這些認證，您就可以透過 SQL Server 驗證，使用資料庫擁有者或 "dbo" 的身分驗證該伺服器上的任何資料庫。

不過，最佳作法是，貴組織的使用者應該使用不同的帳戶來驗證。 因為萬一應用程式的程式碼容易受到 SQL 插入式攻擊，您就可以限制授與應用程式的權限，並降低惡意活動的風險。 

若要建立 SQL Server 驗證使用者，請使用伺服器管理員登入連接到您伺服器上的 **master** 資料庫，並建立新的伺服器登入。  最好在主資料庫中創建使用者。 在主要資料庫中建立使用者，可讓使用者使用類似 SSMS 的工具登入，而不用指定資料庫名稱。  它也可讓使用者使用物件總管來檢視 SQL Server 上的所有資料庫。

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

然後，使用伺服器管理員登錄連接到**SQL 池資料庫**，並根據您創建的伺服器登錄創建資料庫使用者。

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

若要提供使用者執行其他作業 (例如建立登入或建立新資料庫) 的權限，請為他們指派主要資料庫中的 `Loginmanager` 和 `dbmanager` 角色。 

如需有關這些額外角色及向 SQL Database 驗證的詳細資訊，請參閱[管理 Azure SQL Database 的資料庫和登入](../../sql-database/sql-database-manage-logins.md)。  有關使用 Azure 活動目錄進行連接的詳細資訊，請參閱[使用 Azure 活動目錄身份驗證進行連接](sql-data-warehouse-authentication.md)。

## <a name="authorization"></a>授權
授權是指在進行身份驗證並連接後，可以在資料庫中執行哪些操作。 授權權限取決於角色成員資格和權限。 最好的作法是，您應該授與使用者所需的最低權限。 若要管理角色，您可以使用下列預存程序：

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

您所連線的伺服器管理員帳戶是 db_owner 的成員，有權限在資料庫中執行任何動作。 請儲存此帳戶，以便部署結構描述升級及其他管理作業。 請使用具更多有限權限的 "ApplicationUser" 帳戶，從應用程式連線到具應用程式所需之最低權限的資料庫。

有一些方法可以進一步限制使用者在資料庫中可以做什麼：

* 細微的[權限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15)可讓您控制可對資料庫中的個別資料行、資料表、檢視、結構描述、程序和其他物件執行哪些作業。 使用細微權限，以擁有最大控制權，並授與所需的最小權限。 
* 除了 db_datareader 和 db_datawriter 以外，[資料庫角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15)均可以用來建立權力較大的應用程式使用者帳戶，或權力較小的管理帳戶。 內建固定資料庫角色提供簡單的方式來授與權限，但可能會導致授與的權限多於所需的權限。
* [預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) 可用來限制對資料庫可採取的動作。

下列範例會針對使用者定義結構描述授予讀取權限。
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

從 Azure 入口網站或使用 Azure Resource Manager API 管理資料庫和邏輯伺服器的能力，是由您入口網站使用者帳戶的角色指派所控制。 如需詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure)。

## <a name="encryption"></a>加密
透明資料加密 （TDE） 通過加密和解密靜態資料，有助於防止惡意活動的威脅。 當您加密資料庫時，相關聯的備份和交易記錄檔就會加密，完全不需要變更您的應用程式。 TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。 

在 SQL Database 中，資料庫加密金鑰是由內建伺服器憑證保護。 內建伺服器憑證對每個 SQL Database 伺服器都是唯一的。 Microsoft 會每隔 90 天自動輪換這些憑證。 使用的加密演算法為 AES-256。 如需 TDE 的一般描述，請參閱 [透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15)。

您可以使用 [Azure 入口網站](sql-data-warehouse-encryption-tde.md)或 [T-SQL](sql-data-warehouse-encryption-tde-tsql.md) 將資料庫加密。

## <a name="next-steps"></a>後續步驟
有關使用不同協定連接到倉庫的詳細資訊和示例，請參閱連接到 SQL[池](sql-data-warehouse-connect-overview.md)。
