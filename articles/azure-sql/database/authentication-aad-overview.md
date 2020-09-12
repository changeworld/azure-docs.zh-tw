---
title: Azure Active Directory 驗證
description: 瞭解如何使用 Azure Active Directory Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 進行驗證
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 04/23/2020
ms.openlocfilehash: 943569a16101ac170d01d08250b31ba67c0e590e
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434277"
---
# <a name="use-azure-active-directory-authentication"></a>使用 Azure Active Directory authentication

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory (Azure AD) authentication 是一種機制，可讓您使用 Azure SQL Database 中的身分識別來連線至 [受控執行個體](sql-database-paas-overview.md)、 [Azure SQL Azure Synapse Analytics](../managed-instance/sql-managed-instance-paas-overview.md)和 [ () 先前的 SQL 資料倉儲 ](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Azure AD。

> [!NOTE]
> 本文適用于 Azure SQL Database、SQL 受控執行個體和 Azure Synapse Analytics。

您可以使用 Azure AD 驗證，在單一中央位置集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 中央識別碼管理提供單一位置以管理資料庫使用者並簡化權限管理。 包括以下優點：

- 它提供 SQL Server 驗證的替代方案。
- 它有助於停止跨伺服器的使用者身分識別激增。
- 允許在單一位置變換密碼。
- 客戶可以使用外部 (Azure AD) 群組來管理資料庫權限。
- 它可以藉由啟用整合式 Windows 驗證和 Azure Active Directory 支援的其他形式驗證來避免儲存密碼。
- Azure AD 驗證會使用自主資料庫使用者，在資料庫層級驗證身分。
- Azure AD 針對連接至 SQL Database 和 SQL 受控執行個體的應用程式支援以權杖為基礎的驗證。
- Azure AD authentication 支援：
  - Azure AD 僅限雲端的身分識別。
  - Azure AD 支援的混合式身分識別：
    - 具有兩個選項的雲端驗證，結合無縫單一登入 (SSO) **傳遞** 驗證和 **密碼雜湊** 驗證。
    - 同盟驗證。
  - 如需 Azure AD 驗證方法以及要選擇哪一種方法的詳細資訊，請參閱下列文章：
    - [針對 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD 支援來自 SQL Server Management Studio 的連線，其中使用包含 Multi-Factor Authentication 的 Active Directory 通用驗證。 Multi-Factor Authentication 包含強大的驗證，包括一系列簡單的驗證選項，包括電話、文字訊息、具有 pin 的智慧卡或行動代理程式更新。 如需詳細資訊，請參閱 [使用 Azure SQL Database、SQL 受控執行個體和 Azure Synapse Azure AD Multi-Factor Authentication 的 SSMS 支援](authentication-mfa-ssms-overview.md)

- Azure AD 支援從 SQL Server Data Tools (SSDT) 使用 Active Directory 互動式驗證的類似連線。 如需詳細資訊，請參閱 [SQL Server Data Tools (SSDT 中的 Azure Active Directory 支援) ](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> 使用 Azure Active Directory 帳戶，不支援連接至在 Azure 虛擬機器上執行的 SQL Server 實例 (VM) 。 請改用 Active Directory 網域帳戶。  

設定步驟包括以下設定和使用 Azure Active Directory 驗證的程序。

1. 建立和填入 Azure AD。
2. 選用：和目前與您的 Azure 訂用帳戶相關聯的 Active Directory 產生關聯並加以變更
3. 建立 Azure Active Directory 系統管理員。
4. 設定用戶端電腦。
5. 在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者。
6. 使用 Azure AD 身分識別連接到您的資料庫。

> [!NOTE]
> 若要瞭解如何建立和填入 Azure AD，然後使用 Azure SQL Database、SQL 受控執行個體和 Azure Synapse 來設定 Azure AD，請參閱 [使用 Azure AD 設定 Azure SQL Database](authentication-aad-configure.md)。

## <a name="trust-architecture"></a>信認架構

- 只有 Azure AD、SQL Database、SQL 受控執行個體和 Azure Synapse 的雲端部分會被視為支援 Azure AD 原生使用者密碼。
- 若要支援 Windows 單一登入認證 (或 Windows 認證) 的使用者/密碼，請使用針對傳遞和密碼雜湊驗證設定無縫單一登入的同盟或受控網域 Azure Active Directory 認證。 如需詳細資訊，請參閱 [Azure Active Directory 無縫單一登入](../../active-directory/hybrid/how-to-connect-sso.md)。
- 若要支援同盟驗證 (或 Windows 認證的使用者/密碼)，需要與 ADFS 區塊進行通訊。

如需 Azure AD 混合式身分識別、設定和同步處理的詳細資訊，請參閱下列文章：

- 密碼雜湊驗證- [使用 Azure AD Connect 同步處理來執行密碼雜湊同步處理](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- 傳遞驗證- [Azure Active Directory 傳遞驗證](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- 同盟驗證- [在 Azure 中部署 Active Directory 同盟服務](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) 以及 [Azure AD Connect 和同盟](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

如需搭配 ADFS 基礎結構的同盟驗證範例 (或 Windows 認證) 的使用者/密碼，請參閱下圖。 箭頭表示通訊路徑。

![aad 驗證圖表][1]

下圖表示允許用戶端藉由提交權杖連線到資料庫的同盟、信任和主控關聯性。 此權杖是由 Azure AD 所驗證，並受到資料庫信任。 客戶 1 可以代表具有原生使用者的 Azure AD 或具有同盟使用者的 Azure Active Directory。 客戶2代表可能的解決方案，包括匯入的使用者，在此範例中，來自具有 ADFS 與 Azure Active Directory 同步處理的同盟 Azure Active Directory。 請務必了解使用 Azure AD 驗證存取資料庫的必要條件是裝載訂用帳戶要與 Azure AD 相關聯。 您必須使用相同的訂用帳戶來建立 Azure SQL Database、SQL 受控執行個體或 Azure Synapse 資源。

![訂用帳戶關聯性][2]

## <a name="administrator-structure"></a>系統管理員結構

使用 Azure AD 驗證時，有兩個系統管理員帳戶：原始 Azure SQL Database 系統管理員和 Azure AD 系統管理員。 相同的概念適用于 Azure Synapse。 只有以 Azure AD 帳戶為基礎的系統管理員可以在使用者資料庫中建立第一個 Azure AD 自主資料庫使用者。 Azure AD 系統管理員登入可以是 Azure AD 使用者或 Azure AD 群組。 當系統管理員是群組帳戶時，任何群組成員都可以使用它，以啟用伺服器的多個 Azure AD 系統管理員。 使用群組帳戶做為系統管理員，可讓您在 Azure AD 集中新增和移除群組成員，而不需要變更 SQL Database 或 Azure Synapse 中的使用者或許可權，以增強管理能力。 一律只能設定一個 Azure AD 系統管理員 (使用者或群組)。

![系統管理員結構][3]

## <a name="permissions"></a>權限

若要建立新的使用者，您必須具有資料庫中的 `ALTER ANY USER` 權限。 任何資料庫使用者皆可授與 `ALTER ANY USER` 權限。 `ALTER ANY USER` 權限的擁有者還包括伺服器系統管理員帳戶、具備資料庫之 `CONTROL ON DATABASE` 或 `ALTER ON DATABASE` 權限的資料庫使用者，以及 `db_owner` 資料庫角色的成員。

若要在 Azure SQL Database、SQL 受控執行個體或 Azure Synapse 中建立自主資料庫使用者，您必須使用 Azure AD 身分識別來連接到資料庫或實例。 若要建立第一個自主資料庫使用者，您必須使用 Azure AD 系統管理員 (資料庫的擁有者) 連接到資料庫。 您可以 [使用 SQL Database 或 Azure Synapse 來設定及管理 Azure Active Directory 驗證](authentication-aad-configure.md)來示範這一點。 只有在 Azure AD 系統管理員是針對 Azure SQL Database、SQL 受控執行個體或 Azure Synapse 所建立的情況下，才可以 Azure AD 驗證。 如果已從伺服器移除 Azure Active Directory 系統管理員，則先前在 SQL Server 內建立的現有 Azure Active Directory 使用者便無法再使用其 Azure Active Directory 認證連線到資料庫。

## <a name="azure-ad-features-and-limitations"></a>Azure AD 功能和限制

- 您可以針對 Azure SQL Database 布建下列 Azure AD 成員：

  - 原生成員：在受控網域或客戶網域的 Azure AD 中建立的成員。 如需詳細資訊，請參閱 [將您自己的網域名稱新增至 Azure AD](../../active-directory/fundamentals/add-custom-domain.md)。
  - 使用傳遞或密碼雜湊驗證設定無縫單一登入的受控網域上，與 Azure Active Directory 同盟的 Active Directory 網域成員。 如需詳細資訊，請參閱 [Microsoft Azure 現在支援與 Windows Server 的同盟 Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) 和 [Azure Active Directory 無縫單一登入](../../active-directory/hybrid/how-to-connect-sso.md)。
  - 從其他 Azure AD 匯入，且為原生網域或同盟網域成員者。
  - 建立 Active Directory 群組作為安全性群組。

- Azure AD 屬於具有伺服器角色之群組的使用者，將 `db_owner` 無法針對 Azure SQL Database 和 Azure Synapse 使用 **[CREATE DATABASE 範圍認證](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** 語法。 您會看到下列錯誤︰

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    直接將 `db_owner` 角色授與給個別 Azure AD 使用者，以減輕 **CREATE DATABASE SCOPED CREDENTIAL** 問題。

- 下列系統函式在 Azure AD 主體下執行時會傳回 NULL：

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>SQL 受控執行個體

- Azure AD 伺服器主體 (登入) 和支援 [SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)的使用者。
- 將 Azure AD 伺服器主體 (登入) 對應至 Azure AD 群組的登入，因為 [SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)不支援資料庫擁有者。
  - 其中一個延伸模組是當群組加入成為伺服器角色的一部分時 `dbcreator` ，此群組的使用者可以連接到 SQL 受控執行個體並建立新的資料庫，但無法存取資料庫。 這是因為新的資料庫擁有者是 SA，而不是 Azure AD 使用者。 如果將個別使用者新增至 `dbcreator` 伺服器角色，則不會顯示這個問題。
-  (登入) 的 Azure AD 伺服器主體都支援 SQL Agent 管理和作業執行。
- Azure AD 伺服器主體 (登入) 可以執行資料庫備份和還原作業。
- 支援稽核與 Azure AD 伺服器主體 (登入) 和驗證事件相關的所有陳述式。
- 屬於 sysadmin 伺服器角色成員的 Azure AD 伺服器主體 (登入) 支援專用管理員連線。
  - 透過 SQLCMD 公用程式和 SQL Server Management Studio 支援。
- 來自 Azure AD 伺服器主體 (登入) 的登入事件支援登入觸發程序。
- Service Broker 和 DB 電子郵件可使用 Azure AD 伺服器主體 (登入) 來設定。

## <a name="connect-by-using-azure-ad-identities"></a>使用 Azure AD 身分識別連接

Azure Active Directory 驗證支援下列方法，使用 Azure AD 身分識別連接至資料庫：

- Azure Active Directory 密碼
- Azure Active Directory 整合式
- 使用 Multi-Factor Authentication Azure Active Directory 通用
- 使用應用程式權杖驗證

 (登入) 的 Azure AD 伺服器主體支援下列驗證方法：

- Azure Active Directory 密碼
- Azure Active Directory 整合式
- 使用 Multi-Factor Authentication Azure Active Directory 通用

### <a name="additional-considerations"></a>其他考量

- 若要增強管理性，建議您以系統管理員身分佈建專用的 Azure AD 群組。
- 只有一個 Azure AD 系統管理員 (可以在 SQL Database 或 Azure Synapse 中設定伺服器的使用者或群組) 。
  - Azure AD 伺服器主體 (登入) 的 SQL 受控執行個體，可讓您建立多個 Azure AD 伺服器主體 (可新增至角色的登入) `sysadmin` 。
- 只有伺服器的 Azure AD 系統管理員可以一開始使用 Azure Active Directory 帳戶連接到伺服器或受控實例。 Active Directory 系統管理員可以設定後續的 Azure AD 資料庫使用者。
- 建議將連接逾時設定為 30 秒。
- SQL Server 2016 Management Studio 和 SQL Server Data Tools for Visual Studio 2015 (版本 14.0.60311.1 (2016 年 4 月) 或更新版本) 支援 Azure Active Directory 驗證。 ( **.NET Framework Data Provider for SqlServer** 支援 Azure AD 驗證，最低版本 .NET Framework 4.6)。 因此，這些工具和資料層應用程式的最新版本 (DAC 和 BACPAC) 可以使用 Azure AD 驗證。
- 從版本15.0.1 版開始， [sqlcmd 公用程式](/sql/tools/sqlcmd-utility) 和 [bcp 公用程式](/sql/tools/bcp-utility) 支援 Active Directory Multi-Factor Authentication 的互動式驗證。
- SQL Server Data Tools for Visual Studio 2015 至少需要 2016 年 4 月版本的 Data Tools (版本 14.0.60311.1)。 Azure AD 的使用者目前不會顯示在 SSDT 物件總管中。 解決方法是在 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) 中檢視使用者。
- [Microsoft JDBC Driver 6.0 for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) 支援 Azure AD 驗證。 此外，請參閱 [設定連接屬性](/sql/connect/jdbc/setting-the-connection-properties)。
- PolyBase 無法使用 Azure AD 驗證進行驗證。
- 使用 Azure 入口網站匯 **入資料庫** 和 **匯出資料庫** blade，可支援 Azure SQL Database 和 Azure Synapse 的 Azure AD 驗證。 PowerShell 命令也支援使用 Azure AD 驗證的匯入和匯出。
- 使用 CLI 可支援 SQL Database、SQL 受控執行個體和 Azure Synapse 的 Azure AD 驗證。 如需詳細資訊，請參閱使用 SQL Database 或 Azure Synapse 和[SQL Server-az SQL Server](https://docs.microsoft.com/cli/azure/sql/server)來[設定和管理 Azure AD authentication](authentication-aad-configure.md) 。

## <a name="next-steps"></a>接下來的步驟

- 若要瞭解如何建立和填入 Azure AD 的實例，然後使用 Azure SQL Database、SQL 受控執行個體或 Azure Synapse 進行設定，請參閱 [使用 SQL Database、sql 受控執行個體或 Azure Synapse 來設定及管理 Azure Active Directory 驗證](authentication-aad-configure.md)。
- 如需使用 Azure AD 伺服器主體 (使用 SQL 受控執行個體) 登入的教學課程，請參閱 Azure AD[使用 sql 的](../managed-instance/aad-security-configure-tutorial.md)登入 (伺服器主體) 
- 如需 SQL Database 中的登入、使用者、資料庫角色和許可權的總覽，請參閱登入 [、使用者、資料庫角色和許可權](logins-create-manage.md)。
- 如需資料庫主體的詳細資訊，請參閱[主體](https://msdn.microsoft.com/library/ms181127.aspx)。
- 如需資料庫角色的詳細資訊，請參閱[資料庫角色](https://msdn.microsoft.com/library/ms189121.aspx)。
- 如需為 SQL 受控執行個體建立 Azure AD 伺服器主體 (登入) 的語法，請參閱  [建立登](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)入。
- 如需 SQL Database 中防火牆規則的詳細資訊，請參閱 [SQL Database 防火牆規則](firewall-configure.md)。

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
