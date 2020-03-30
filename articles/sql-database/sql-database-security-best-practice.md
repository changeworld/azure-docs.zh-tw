---
title: 解決常見安全要求的劇本 |微軟文檔
titleSuffix: Azure SQL Database
description: 本文在 Azure SQL 資料庫中提供了常見的安全要求和最佳實踐。
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: c18e1b1a1feba5c528a692b7d63287b3751b62cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77506222"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>使用 Azure SQL 資料庫解決常見安全要求的劇本

> [!NOTE]
> 本文檔提供了有關如何解決常見安全要求的最佳做法。 並非所有要求都適用于所有環境，您應該諮詢資料庫和安全團隊，以便實現哪些功能。

## <a name="solving-common-security-requirements"></a>解決常見的安全要求

本文檔提供有關如何使用 Azure SQL 資料庫解決新或現有應用程式的常見安全要求的指導。 它由高級安全區域組織。 有關特定威脅，請參閱[共同安全威脅和潛在緩解部分](#common-security-threats-and-potential-mitigations)。 儘管在將應用程式從本地遷移到 Azure 時，某些建議適用，但遷移方案不是本文檔的重點。

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>本指南仲介紹的 Azure SQL 資料庫部署產品

- [SQL 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-single-index)[：Azure SQL 資料庫伺服器](sql-database-servers.md)中的[單個資料庫](sql-database-single-database.md)和[彈性池](sql-database-elastic-pool.md)
- [受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>本指南未介紹的 SQL 部署功能

- Azure SQL 資料倉儲
- Azure SQL VM （IaaS）
- 內部部署的 SQL Server

### <a name="audience"></a>適用對象

本指南的預期受眾是面臨有關如何保護 Azure SQL 資料庫的問題的客戶。 對本最佳實踐文章感興趣的角色包括但不限於：

- 安全架構師
- 安全經理
- 合規官
- 隱私官
- 安全工程師

### <a name="using-this-guide"></a><a id="using"></a>使用本指南

本文檔旨在作為我們現有的 Azure SQL[資料庫安全](sql-database-security-overview.md)文檔的配套。

除非另有說明，我們建議您遵循每個部分中列出的所有最佳實踐，以實現相應的目標或要求。 為滿足特定的安全合規性標準或最佳實踐，在適用的情況下，在"要求"或"目標"部分下列出了重要的法規遵從性控制。 以下是本文中引用的安全標準和法規：

- [FedRAMP](https://www.fedramp.gov/documents/)： AC-04， AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html)： CM-3， SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html)： 存取控制， 加密
- [微軟操作安全保障 （OSA） 實踐](https://www.microsoft.com/en-us/securityengineering/osa/practices)： 練習#1-6 和#9
- [NIST 特別出版物 800-53 安全控制](https://nvd.nist.gov/800-53)： AC-5， AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library)： 6.3.2， 6.4.2

### <a name="feedback"></a>意見反應

我們計畫繼續更新此處列出的建議和最佳實踐。 使用本文底部**的回饋**連結為本文檔提供輸入或任何更正。

## <a name="authentication"></a>驗證

驗證是證明使用者宣告身分的程序。 Azure SQL Database 支援兩種驗證類型：

- SQL 驗證
- Azure Active Directory 驗證

> [!NOTE]
> 並非所有工具和協力廠商應用程式都支援 Azure 活動目錄身份驗證。

### <a name="central-management-for-identities"></a>身份的中央管理

中央身份管理提供以下好處：

- 管理群組帳戶並控制使用者許可權，而無需跨 Azure SQL 資料庫伺服器和資料庫複製登錄名。
- 簡化和靈活的版權管理。
- 大規模管理應用程式。

**如何實現**：

- 使用 Azure 活動目錄 （Azure AD） 身份驗證進行集中式標識管理。

**最佳實踐**：

- 創建 Azure AD 租戶並[創建使用者](../active-directory/fundamentals/add-users-azure-active-directory.md)以表示人工使用者並創建[服務主體](../active-directory/develop/app-objects-and-service-principals.md)以表示應用、服務和自動化工具。 服務主體等效于 Windows 和 Linux 中的服務帳戶。 

- 通過組分配向 Azure AD 主體分配資源存取權限：創建 Azure AD 組、授予對組的許可權以及向組添加單個成員。 在資料庫中，創建映射 Azure AD 組的包含資料庫使用者。 要在資料庫中分配許可權，請將使用者置於具有相應許可權的資料庫角色中。
  - 請參閱文章，[使用 SQL 配置和管理 Azure 活動目錄身份驗證](sql-database-aad-authentication-configure.md)，[並使用 Azure AD 進行 SQL 身份驗證](sql-database-aad-authentication.md)。
  > [!NOTE]
  > 在託管實例中，還可以創建映射到主資料庫中的 Azure AD 主體的登錄名。 請參閱[創建登錄（轉用 SQL）。](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)

- 使用 Azure AD 組可簡化版權管理和組擁有者，並且資源擁有者可以添加/刪除組成員。 

- 為 SQL DB 伺服器為 Azure AD 管理員創建單獨的組。

  - 請參閱文章"[為 Azure SQL 資料庫伺服器預配 Azure 活動目錄管理員](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)"。

- 使用 Azure AD 審核活動報告監視 Azure AD 組成員身份更改。 

- 對於託管實例，創建 Azure AD 管理員需要單獨的步驟。 
  - 請參閱文章"[為託管實例預配 Azure 活動目錄管理員](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)"。 

> [!NOTE]
> - Azure AD 身份驗證記錄在 Azure SQL 稽核記錄中，但不記錄在 Azure AD 登錄日誌中。
> - 在 Azure 中授予的 RBAC 許可權不適用於 Azure SQL DB 許可權。 此類許可權必須在 SQL DB 中使用現有 SQL 許可權手動創建/映射。
> - 在用戶端 Azure AD 身份驗證上，需要訪問 Internet 或通過使用者定義的路由 （UDR） 訪問 VNet。
> - Azure AD 訪問權杖緩存在用戶端上，其存留期取決於權杖配置。 請參閱文章["Azure 活動目錄中的可配置權杖存留期](../active-directory/develop/active-directory-configurable-token-lifetimes.md)"
> - 有關解決 Azure AD 身份驗證問題的解決方案，請參閱以下博客：<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> 在： OSA 實踐#2， ISO 存取控制 （AC）

Azure 多重要素驗證 （MFA） 通過需要多種形式的身份驗證説明提供其他安全性。

**如何實現**：

- 使用條件訪問在 Azure AD 中[啟用 MFA](../active-directory/authentication/concept-mfa-howitworks.md)並使用互動式身份驗證。 

- 另一種方法是為整個 Azure AD 或 AD 域啟用 MFA。

**最佳實踐**：

- 在 Azure AD 中啟動條件訪問（需要高級訂閱）。 
  - 請參閱文章["Azure AD 中的條件訪問](../active-directory/conditional-access/overview.md)"。  

- 使用 Azure AD 條件訪問創建 Azure AD 組並為選定的組啟用 MFA 策略。 
  - 請參閱文章"[計畫條件訪問部署](../active-directory/conditional-access/plan-conditional-access.md)"。 

- 可以為整個 Azure AD 或與 Azure AD 聯合的整個活動目錄啟用 MFA。 

- 在 SQL DB 中使用 Azure AD 互動式身份驗證模式，其中以對話模式請求密碼，然後進行 MFA 身份驗證：      
  - 在 SSMS 中使用通用身份驗證。 請參閱本文，[將多重 AAD 身份驗證與 Azure SQL 資料庫和 Azure SQL 資料倉儲（對 MFA 的 SSMS 支援）結合](sql-database-ssms-mfa-authentication.md)使用。
  - 使用 SQL 伺服器資料工具 （SSDT） 中支援的互動式身份驗證。 請參閱 SQL[伺服器資料工具 （SSDT） 中的 Azure 活動目錄支援](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)一文。
  - 使用支援 MFA 的其他 SQL 工具。 
    - SSMS 嚮導對匯出/提取/部署資料庫的支援  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)： 選項 "/ua" 
    - [sqlcmd 實用程式](https://docs.microsoft.com/sql/tools/sqlcmd-utility)： 選項 -G （互動式）
    - [bcp 實用程式](https://docs.microsoft.com/sql/tools/bcp-utility)： 選項 -G （互動式） 

- 實現應用程式，使用 MFA 支援的互動式身份驗證連接到 Azure SQL 資料庫。 
  - 請參閱文章"[使用 Azure 多重要素驗證連接到 Azure SQL 資料庫](active-directory-interactive-connect-azure-sql-db.md)"。 
  > [!NOTE]
  > 此身份驗證模式需要基於使用者的身份。 如果使用的受信任標識模型繞過單個 Azure AD 使用者身份驗證（例如，對 Azure 資源使用託管標識），則 MFA 不適用。

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>儘量減少使用者使用基於密碼的身份驗證

> 在： OSA 實踐#4， ISO 存取控制 （AC）

基於密碼的身份驗證方法是一種較弱的身份驗證形式。 憑據可能會洩露或誤贈送。

**如何實現**：

- 使用 Azure AD 集成身份驗證，禁止使用密碼。

**最佳實踐**：

- 使用 Windows 憑據使用單一登入身份驗證。 將本地 AD 域與 Azure AD 聯合使用，並使用集成 Windows 身份驗證（對於具有 Azure AD 的域聯接電腦）。
  - 請參閱文章[，SSMS 支援 Azure AD 集成身份驗證](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication)。

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>儘量減少對應用程式使用基於密碼的身份驗證 

> 在： OSA 實踐#4， ISO 存取控制 （AC）

**如何實現**：

- 啟用 Azure 託管標識。 您還可以使用集成身份驗證或基於證書的身份驗證。 

**最佳實踐**：

- [對 Azure 資源使用託管標識](../active-directory/managed-identities-azure-resources/overview.md)。
  - [系統分配的託管標識](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [使用者分配的託管標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [使用具有託管標識的應用服務（不更改代碼）的應用服務中使用 Azure SQL 資料庫](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- 對應用程式使用基於證書的身份驗證。 
  - 請參閱此[代碼示例](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)。 

- 對集成的聯合域和域聯接電腦使用 Azure AD 身份驗證（請參閱上面的部分）。
  - 有關[集成身份驗證，請參閱應用程式範例](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)。

### <a name="protect-passwords-and-secrets"></a>保護密碼和機密

對於密碼不可避免的情況，請確保密碼是安全的。

**如何實現**：

- 使用 Azure 金鑰保存庫存儲密碼和機密。 如果適用，請使用 MFA 進行 Azure SQL 資料庫與 Azure AD 使用者。

**最佳實踐**：

- 如果無法避免密碼或機密，請將使用者密碼和應用程式機密存儲在 Azure 金鑰保存庫中，並通過金鑰保存庫訪問策略管理訪問。 

- 各種應用開發框架也可能提供特定于框架的機制來保護應用中的秘密。 例如[：ASP.NET核心應用程式](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows)。

### <a name="use-sql-authentication-for-legacy-applications"></a>對繼承應用程式使用 SQL 身份驗證 

SQL 身份驗證是指使用使用者名和密碼連接到 Azure SQL 資料庫時使用者的身份驗證。 需要在每個 SQL 資料庫伺服器或託管實例中創建登錄名，並在每個資料庫中創建一個使用者。

**如何實現**：

- 使用 SQL 身份驗證。

**最佳實踐**：

- 作為伺服器管理員，創建登錄名和使用者。 除非使用包含的資料庫使用者使用密碼，否則所有密碼都存儲在主資料庫中。
  - 請參閱文章，[控制和授予資料庫對 SQL 資料庫和 SQL 資料倉儲的訪問](sql-database-manage-logins.md)。

## <a name="access-management"></a>存取管理

訪問管理是控制和管理授權使用者對 Azure SQL 資料庫的存取權限和許可權的過程。

### <a name="implement-principle-of-least-privilege"></a>實現最低特權原則

> 在中提及： FedRamp 控制 AC-06， NIST： AC-6， OSA 實踐#3

最低特權原則規定，使用者不應擁有比完成任務所需的更多許可權。 有關詳細資訊，請參閱文章["剛到足夠的管理](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)"。

**如何實現**：

僅分配完成所需任務所需的[許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)：

- 在 SQL 資料平面中： 
    - 使用細微性許可權和使用者定義的資料庫角色（或 MI 中的伺服器角色）： 
        1. 創建所需的角色
            - [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [CREATE SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. 創建所需的使用者
            - [創建使用者](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. 將使用者作為成員添加到角色 
            - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [更改伺服器角色](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. 然後為角色指派許可權。 
            - [授予](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - 確保不要將使用者分配給不必要的角色。

- 在 Azure 資源管理器中：
  - 如果可用或自訂 RBAC 角色，請使用內置角色，並分配必要的許可權。
    - [Azure 的內置角色](../role-based-access-control/built-in-roles.md) 
    - [適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)

**最佳實踐**：

以下最佳實踐是可選的，但將更好地管理和支援您的安全性原則： 

- 如果可能，從盡可能少的許可權集開始，如果確實存在必要（和理由），則開始逐個添加許可權，而不是相反的方法：一步一步地取消許可權。 

- 避免向單個使用者分配許可權。 而是一致地使用角色（資料庫或伺服器角色）。 角色對報告和故障排除許可權有很大説明。 （Azure RBAC 僅支援通過角色指派許可權。 

- 創建和使用具有所需確切許可權的自訂角色。 實際使用的典型角色： 
  - 安全部署 
  - 系統管理員 
  - 開發人員 
  - 支援人員 
  - 稽核員 
  - 自動化流程 
  - 終端使用者 
  
- 僅當角色的許可權與使用者所需的許可權完全符合時，才使用內置角色。 您可以將使用者分配給多個角色。 

- 請記住，SQL Server 資料庫引擎中的許可權可以應用於以下作用域。 範圍越小，授予的許可權的影響越小： 
  - Azure SQL 資料庫伺服器（主資料庫中的特殊角色） 
  - 資料庫 
  - 結構描述
      - 最佳做法是使用架構授予資料庫中的許可權。 （另請參閱[：SQL Server 的架構設計：考慮到安全性的架構設計建議](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)）
  - 物件（表、視圖、過程等） 
  > [!NOTE]
  > 不建議在物件級別應用許可權，因為此級別會增加整個實現不必要的複雜性。 如果您決定使用物件級許可權，則應清楚地記錄這些許可權。 這同樣適用于列級許可權，由於同樣的原因，這些許可權更不可推薦。 還要注意，預設情況下，表級[DENY](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql)不會覆蓋列級 GRANT。 這將需要啟動[符合伺服器配置的通用條件](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)。

- 使用[漏洞評估 （VA）](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)執行定期檢查，以測試許可權過多。

### <a name="implement-separation-of-duties"></a>實行職責分離

> 中提到的： FedRamp： AC-04， NIST： AC-5， ISO： A.6.1.2， PCI 6.4.2， SOC： CM-3， SDL-3

職責分離，也稱為職責分離，描述了將敏感任務拆分為分配給不同使用者的多個任務的要求。 職責分離有助於防止資料洩露。

**如何實現**：

- 確定職責分離的必要級別。 範例： 
  - 在開發/測試和生產環境之間 
  - 安全敏感任務與資料庫管理員 （DBA） 管理級別任務與開發人員任務。 
    - 示例：審核員、創建角色級安全 （RLS） 安全性原則、使用 DDL 許可權實現 SQL 資料庫物件。

- 確定訪問系統的使用者（和自動化進程）的全面層次結構。

- 根據所需的使用者組創建角色，並將許可權分配給角色。 
  - 對於 Azure 門戶中的管理級任務或通過 PowerShell 自動化使用 RBAC 角色。 找到符合要求的內置角色，或使用可用許可權創建自訂 RBAC 角色 
  - 為託管實例中的伺服器範圍任務（創建新登錄名、資料庫）創建伺服器角色。 
  - 為資料庫級任務創建資料庫角色。

- 對於某些敏感任務，請考慮創建由證書簽名的特殊預存程序，以代表使用者執行任務。 
  - 示例：[教程：使用證書對預存程序進行簽名](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- 在 Azure 金鑰保存庫中使用客戶管理的金鑰實施透明資料加密 （TDE），以便在資料擁有者和安全擁有者之間實現職責分離。 
  - 請參閱文章"[從 Azure 門戶配置 Azure 存儲加密的客戶管理金鑰](../storage/common/storage-encryption-keys-portal.md)"。 

- 為了確保 DBA 看不到被視為高度敏感但仍可以執行 DBA 任務的資料，可以使用"始終加密"與角色分離。 
  - 請參閱文章[，"始終加密的金鑰管理概述](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)"，[使用角色分離進行金鑰配置](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)，以及[具有角色分離的列主金鑰輪換](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)。 

- 如果至少如果沒有可能導致系統幾乎無法使用的重大成本和努力，則至少不可行，則可以通過使用補償控制（如： 
  - 進程中的人工干預。 
  - 審核跟蹤 – 有關審核的詳細資訊，請參閱[審核關鍵安全事件](#audit-critical-security-events)。

**最佳實踐**：

- 確保不同的帳戶用於開發/測試和生產環境。 不同的帳戶有助於遵守測試&生產系統的分離。

- 避免向單個使用者分配許可權。 而是一致地使用角色（資料庫或伺服器角色）。 具有角色對報告和故障排除許可權有很大説明。

- 當權限與所需的許可權完全符合時，請使用內置角色 - 如果多個內置角色的擁有權限聯合導致 100% 匹配，也可以同時分配多個角色。 

- 當內置角色授予的許可權過多或許可權不足時，創建和使用自訂角色。 

- 角色指派也可以臨時完成，也稱為動態職責分離 （DSD），無論是在 T-SQL 中的 SQL 代理作業步驟中，還是使用 Azure PIM 執行 RBAC 角色。 

- 確保 DBA 無權訪問加密金鑰或金鑰存儲，並且有權訪問金鑰的安全管理員反過來無法訪問資料庫。 

- 始終確保具有與安全相關的操作的審核跟蹤。 

- 您可以檢索內置 RBAC 角色的定義，以查看使用的許可權，並根據這些角色的摘錄和累積內容通過 PowerShell 創建自訂角色。

- 由於db_owner資料庫角色的任何成員都可以更改安全設置（如透明資料加密 （TDE））或更改 SLO，因此應謹慎授予此成員資格。 但是，有許多工需要db_owner特權。 任務，如更改任何資料庫設置，如更改資料庫選項。 審核在任何解決方案中都起著關鍵作用。

- 無法限制db_owner的許可權，因此無法阻止管理帳戶查看使用者資料。 如果資料庫中有高度敏感的資料，則"始終加密"可用於安全地防止db_owners或任何其他 DBA 查看資料。

> [!NOTE]
> 實現職責分離 （SoD） 對於安全相關或故障排除任務具有挑戰性。 其他領域（如開發和最終使用者角色）更易於隔離。 大多數合規性相關控制項允許在其他解決方案不可行時使用備用控制功能，如審核。

對於想要深入瞭解 SoD 的讀者，我們建議提供以下資源： 

- 針對 Azure SQL Database：  
  - [控制及授與 SQL Database 和 SQL 資料倉儲的資料庫存取權](sql-database-manage-logins.md)
  - [應用程式開發人員的發動機職責分離](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [SQL Server 2014 中的職責分離](https://www.microsoft.com/download/details.aspx?id=39269)
  - [在 SQL Server 中簽署預存程序](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- 對於 Azure 資源管理：
  - [Azure 的內置角色](../role-based-access-control/built-in-roles.md) 
  - [適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)
  - [使用 Azure AD 特權標識管理進行提升訪問](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>執行定期代碼評審

> 在中提及： PCI： 6.3.2， SOC： SDL-3 

職責分離並不限於資料庫中的資料，還包括應用程式代碼。 惡意程式碼可能會繞過安全控制。 在將自訂代碼部署到生產中之前，必須查看正在部署的內容。

**如何實現**：

- 使用支援原始程式碼管理的 Azure 資料工作室等資料庫工具。 

- 實施隔離的代碼部署過程。

- 在提交到主分支之前，人員（代碼本身的作者之外）必須檢查代碼中的許可權風險的潛在提升以及惡意資料修改，以防止欺詐和惡意訪問。 這可以使用原始程式碼管理機制來完成。

**最佳實踐**：

- 標準化：它有助於實現任何代碼更新應遵循的標準過程。 

- 漏洞評估包含檢查許可權過大、使用舊加密演算法以及資料庫架構中的其他安全問題的規則。 

- 可以使用高級威脅保護在 QA 或測試環境中執行進一步檢查，該保護可以掃描易受 SQL 注入攻擊的代碼。

- 需要查找哪些示例： 
  - 在自動 SQL 代碼更新部署中創建使用者或更改安全設置。 
  - 預存程序，根據提供的參數，以不符合項的方式更新儲存格中的貨幣值。 

- 確保執行審核的人員是原始代碼作者以外的個人，並且精通代碼評審和安全編碼。

- 請務必瞭解代碼更改的所有來源。 代碼可以位於 T-SQL 腳本中。 它可以是臨時命令，以視圖、函數、觸發器和預存程序的形式執行或部署。 它可以是 SQL 代理作業定義（步驟）的一部分。 也可以在 SSIS 包、Azure 資料工廠和其他服務中執行它。

## <a name="data-protection"></a>資料保護

資料保護是一組功能，用於保護重要資訊免受加密或混淆的影響。

> [!NOTE]
> Microsoft 證明 Azure SQL 資料庫符合 FIPS 140-2 級別 1 標準。 這是在驗證了嚴格使用 FIPS 140-2 級 1 級可接受演算法和 FIPS 1 級 1 級驗證的這些演算法實例後完成的，包括與所需的金鑰長度、金鑰管理、金鑰生成和金鑰存儲的一致性。 此證明旨在讓我們的客戶回應在資料處理或系統或應用程式交付中使用 FIPS 140-2 級 1 級驗證實例的需求或要求。 我們定義了上述聲明中使用的"符合 FIPS 140-2 級別 1"和"FIPS 140-2 等級 1 符合性"等術語，以證明它們有意適用于美國和加拿大政府使用不同的術語"FIPS 140-2 級別 1 驗證"。 


### <a name="encrypt-data-in-transit"></a>加密傳輸中的資料

> 在： OSA 實踐#6， ISO 控制系列： 加密

在用戶端和伺服器之間移動資料時保護資料。 請參閱[網路安全](#network-security)。

### <a name="encrypt-data-at-rest"></a>加密待用資料

> 在： OSA 實踐#6， ISO 控制系列： 加密

靜態加密是資料在資料庫、日誌和備份檔案中保留時的加密保護。

**如何實現**：

- 預設情況下，對於 Azure SQL 資料庫中 2017 年之後創建的任何資料庫，將啟用具有服務託管金鑰的[透明資料庫加密 （TDE）。](transparent-data-encryption-azure-sql.md)
- 在託管實例中，如果資料庫是使用本機伺服器從還原操作創建的，則原始資料庫的 TDE 設置將得到遵守。 如果原始資料庫未啟用 TDE，我們建議手動為託管實例打開 TDE。

**最佳實踐**：

- 不要將需要靜態加密的資料存儲在主資料庫中。 主資料庫不能使用 TDE 加密。

- 如果需要提高透明度和對 TDE 保護進行精細控制，請使用 Azure 金鑰保存庫中的客戶管理金鑰。 Azure 金鑰保存庫允許隨時撤銷許可權，使資料庫無法訪問。 您可以與其他金鑰一起集中管理 TDE 保護程式，或使用 Azure 金鑰保存庫按照自己的計畫輪換 TDE 保護程式。

- 如果在 Azure 金鑰保存庫中使用客戶管理的金鑰，請按照以下文章：[使用 Azure 金鑰保存庫配置 TDE 的指南](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv)以及如何[使用 Azure 金鑰保存庫配置 Geo-DR。](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde)

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>保護使用中的敏感性資料免受高特權、未經授權的使用者的攻擊

正在使用的資料是在 SQL 查詢執行期間存儲在資料庫系統記憶體中的資料。 如果資料庫存儲敏感性資料，則可能需要組織確保阻止高特權使用者查看資料庫中的敏感性資料。 高特權使用者（如組織中的 Microsoft 運算子或 DBA）應該能夠管理資料庫，但阻止查看和可能從 SQL Server 進程記憶體中滲透敏感性資料或查詢資料庫。

確定哪些資料是敏感的，以及敏感性資料是否必須在記憶體中加密，管理員不能以純文字方式訪問的策略特定于您的組織和需要遵守的合規性法規。 請參閱相關要求：[識別和標記敏感性資料](#identify-and-tag-sensitive-data)。

**如何實現**：

- 使用["始終加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)"可確保敏感性資料不會在 Azure SQL 資料庫中以純文字形式公開，即使在記憶體/正在使用中也是如此。 始終加密可保護資料免受資料庫管理員 （DBA） 和雲管理員（或可能類比高特權但未經授權的使用者的不法分子）的攻擊，並使您能夠對誰可以訪問您的資料進行更多控制。

**最佳實踐**：

- 始終加密不能替代加密靜態資料 （TDE） 或傳輸中的資料 （SSL/TLS）。 應始終加密不應用於非敏感性資料，以儘量減少性能和功能影響。 建議將"始終加密"與 TDE 和傳輸層安全 （TLS） 結合使用，以便全面保護靜態、在途和在用中的資料。 

- 在生產資料庫中部署"始終加密"之前，評估加密已識別的敏感性資料列的影響。 通常，"始終加密"會減少加密列查詢的功能，並存在其他限制，如["始終加密 - 功能詳細資訊](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details)"。 因此，您可能需要重新構建應用程式以在用戶端或/重構資料庫架構（包括預存程序、函數、視圖和觸發器的定義）上重新實現該功能、查詢不支援的功能。 如果現有應用程式不符合"始終加密"的限制和限制，則它們可能無法使用加密列。 雖然支援"始終加密"的 Microsoft 工具、產品和服務的生態系統正在增長，但其中一些工具的生態系統不適用於加密列。 加密列也會影響查詢性能，具體取決於工作負荷的特徵。 

- 如果使用"始終加密"保護資料免受惡意 DBA 的攻擊，請使用角色分離管理始終加密的金鑰。 通過角色分離，安全管理員將創建物理金鑰。 DBA 在資料庫中創建列主金鑰和列加密金鑰中繼資料物件，描述物理金鑰。 在此過程中，安全管理員不需要訪問資料庫，並且 DBA 不需要以純文字形式訪問物理金鑰。 
  - 有關詳細資訊，請參閱文章"[使用角色分離管理金鑰](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation)"。 

- 將列主金鑰存儲在 Azure 金鑰保存庫中，便於管理。 避免使用使金鑰管理困難的 Windows 憑證存放區（以及一般分發金鑰存儲解決方案，而不是中央金鑰管理解決方案）。 

- 仔細考慮使用多個金鑰（列主金鑰或列加密金鑰）的權衡。 保持金鑰數量小，以降低金鑰管理成本。 每個資料庫的一列主金鑰和一列加密金鑰通常在穩態環境中就足夠了（不在金鑰輪換的中間）。 如果具有不同的使用者組（每個使用者組使用不同的金鑰並訪問不同的資料）則可能需要其他金鑰。  

- 根據您的合規性要求旋轉列主鍵。 如果還需要輪換列加密金鑰，請考慮使用連線加密來最小化應用程式停機時間。 
  - 請參閱文章"[性能和可用性注意事項](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations)"。 

- 如果需要支援對資料的計算（相等），請使用確定性加密。 否則，請使用隨機加密。 避免對低熵資料集或具有已知分佈的資料集使用確定性加密。 

- 如果您擔心未經您的同意合法訪問資料，請確保所有有權訪問純文字金鑰和資料的應用程式和工具都在 Microsoft Azure 雲之外運行。 如果沒有對金鑰的訪問，協力廠商將無法解密資料，除非他們繞過加密。

- 始終加密不容易支援授予對金鑰（和受保護資料）的臨時存取權限。 例如，如果需要與 DBA 共用金鑰，以便 DBA 對敏感和加密資料執行一些清理操作。 可靠性從 DBA 撤銷對資料的訪問的唯一方法是輪換列加密金鑰和保護資料的列主金鑰，這是一項昂貴的操作。 

- 要訪問加密列中的純文字值，使用者需要有權訪問保護列的 CMK，該列在保存 CMK 的金鑰存儲中配置。 使用者還需要具有 **"查看任何"主金鑰定義**"並**查看任何"控制金鑰定義"** 資料庫許可權。

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>通過加密控制應用程式使用者對敏感性資料的訪問

加密可用作確保只有有權訪問加密金鑰的特定應用程式使用者才能查看或更新資料的一種方式。

**如何實現**：

- 使用單元級加密 （CLE）。 有關詳細資訊，請參閱文章["加密資料列](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data)"。 
- 使用始終加密，但要注意其局限性。 限制如下。

**最佳作法**

使用 CLE 時：

- 通過 SQL 許可權和角色控制對金鑰的訪問。 

- 使用 AES（建議使用 AES 256）進行資料加密。 演算法（如 RC4、DES 和 TripleDES）被棄用，不應由於已知漏洞而使用。 

- 使用非對稱金鑰/證書（不是密碼）保護對稱金鑰，以避免使用 3DES。 

- 通過匯出/導入（bacpac 檔）使用單元級加密遷移資料庫時要小心。 
  - 有關如何在遷移資料時防止丟失金鑰，請參閱文章["在 Azure SQL 資料庫中使用單元級加密的建議](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)"，以及其他最佳實踐指南。

請記住，始終加密主要是為了保護 Azure SQL 資料庫的高特權使用者（雲運算子、DBA） 中的敏感性資料，請參閱[保護正在使用的敏感性資料免受高特權、未經授權的使用者](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)的攻擊。 使用"始終加密"保護資料免受應用程式使用者的影響時，請注意以下挑戰：

- 預設情況下，支援"始終加密"的所有 Microsoft 用戶端驅動程式都維護列加密金鑰的全域（每個應用程式一個）緩存。 一旦用戶端驅動程式通過聯繫持有列主金鑰的金鑰存儲獲取純文字列加密金鑰，便緩存純文字列加密金鑰。 這使得從多使用者應用程式的使用者隔離資料具有挑戰性。 如果應用程式在與金鑰存儲（如 Azure 金鑰保存庫）交互時類比最終使用者，則在使用者的查詢使用列加密金鑰填充緩存後，需要相同金鑰但由其他使用者觸發的後續查詢將使用緩存的金鑰。 驅動程式不會調用金鑰存儲，也不會檢查第二個使用者是否具有訪問列加密金鑰的許可權。 因此，即使使用者無法訪問金鑰，使用者也可以看到加密的資料。 要在多使用者應用程式中實現使用者隔離，可以禁用列加密金鑰緩存。 禁用緩存將導致額外的性能開銷，因為驅動程式需要為每個資料加密或解密操作與金鑰存儲聯繫。

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>保護資料免受應用程式使用者未經授權的查看，同時保留資料格式
防止未經授權的使用者查看資料的另一種方法是混淆或遮罩資料，同時保留資料類型和格式，以確保使用者應用程式可以繼續處理和顯示資料。

**如何實現**：

- 使用[動態資料掩蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)可混淆表列。

> [!NOTE]
> 始終加密不適用於動態資料遮罩。 無法加密和遮罩同一列，這意味著您需要優先保護正在使用的資料，而不是通過動態資料遮罩為應用使用者遮罩資料。

**最佳實踐**：

> [!NOTE]
> 動態資料掩蔽不能用於保護資料免受高特權使用者。 遮罩策略不適用於具有管理存取權限（如db_owner的使用者。

- 不允許應用使用者運行臨時查詢（因為他們可能能夠處理動態資料遮罩）。  
  - 請參閱文章，[使用推理或暴力技術繞過掩蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques)以瞭解詳細資訊。  

- 使用適當的存取控制策略（通過 SQL 許可權、角色、RLS）來限制使用者許可權以在遮罩列中進行更新。 在列上創建遮罩不會阻止對該列的更新。 查詢蒙版列時接收蒙版資料的使用者可以更新資料（如果他們具有寫入權限）。    

-  動態資料掩蔽不會保留蒙版值的統計屬性。 這可能會影響查詢結果（例如，包含篩選謂詞或蒙版資料上的聯接的查詢）。

## <a name="network-security"></a>網路安全性
網路安全是指存取控制和最佳做法，用於保護在傳輸到 Azure SQL 資料庫中的資料。

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>將用戶端配置為安全地連接到 Azure SQL 資料庫 
有關如何防止具有已知漏洞的用戶端電腦和應用程式（例如，使用較舊的 TLS 協定和密碼套件）連接到 Azure SQL 資料庫的最佳做法。

**如何實現**：

- 確保連接到 Azure SQL 資料庫的用戶端電腦使用[傳輸層安全性 （TLS）。](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)

**最佳實踐**：

- 配置所有應用和工具，以啟用加密連接到 SQL DB 
  - 加密 = 打開、信任伺服器憑證 = 關閉（或與非 Microsoft 驅動程式等效）。 

- 如果應用使用的驅動程式不支援 TLS 或支援舊版本的 TLS，請盡可能更換該驅動程式。 如果不可能，請仔細評估安全風險。 

- 通過在用戶端電腦上禁用每個[傳輸層安全 （TLS） 註冊表設置](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)連接到 Azure SQL 資料庫的用戶端電腦上，通過 SSL 2.0、SSL 3.0、TLS 1.0 和 TLS 1.1 中的漏洞來減少攻擊媒介。 

- 檢查用戶端上可用的密碼套件[：TLS/SSL （S通道 SSP） 中的密碼套件](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel)。 具體來說，禁用 3DES 每個[配置 TLS 密碼套件訂單](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)。 

- 對於 Azure SQL 資料庫，對代理和重定向連線類型強制執行加密。 如果使用託管實例，請使用**代理**連線類型（預設），因為這將強制從伺服器端進行加密。 **重定向**連線類型當前不支援加密強制，並且僅在專用 IP 連接上可用。 

- 有關詳細資訊，請參閱[Azure SQL 連接體系結構 - 連接策略](sql-database-connectivity-architecture.md#connection-policy)。


### <a name="minimize-attack-surface"></a>最小化攻擊面
儘量減少惡意使用者可以攻擊的功能數量。 為 Azure SQL 資料庫實施網路訪問控制項。

> 在： OSA 實踐#5

**如何實現**：

在 Azure SQL 資料庫伺服器（包含單例資料庫或彈性池）：
- 設置允許訪問 Azure 服務到 OFF。

- 使用 VNet 服務終結點和 VNet 防火牆規則。

- 使用專用連結（預覽）。

在託管實例中：
- 遵循[網路要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)中的準則。 

**最佳實踐**：

- 通過連接專用終結點（例如，使用專用資料路徑）限制對 Azure SQL 資料庫的訪問： 
  - 可以在 VNet 中隔離託管實例，以防止外部訪問。 位於同一區域中的相同或對等 VNet 中的應用程式和工具可以直接存取它。 位於不同區域的應用和工具可以使用 VNet 到 VNet 連接或 ExpressRoute 電路對等互連來建立連接。 客戶應使用網路安全性群組 （NSG） 僅將埠 1433 的訪問限制為需要訪問託管實例的資源 
  - 對於 SQL 資料庫伺服器（包含單個資料庫或彈性池），請使用[專用連結](sql-database-private-endpoint-overview.md)功能，該功能為 VNet 內的 SQL 資料庫伺服器提供專用專用 IP。 您還可以使用[VNet 服務終結點與 VNet 防火牆規則一起](sql-database-vnet-service-endpoint-rule-overview.md)限制對 SQL 資料庫伺服器的訪問。
  - 移動使用者應使用點對點 VPN 連接通過資料路徑進行連接。
  - 連接到其本地網路的使用者應使用網站到網站 VPN 連接或 ExpressRoute 通過資料路徑進行連接。

- 您可以通過連接到公共終結點（例如，使用公共資料路徑）訪問 Azure SQL 資料庫。 應考慮以下最佳做法： 
  - 對於 SQL 資料庫伺服器，使用[IP 防火牆規則](sql-database-firewall-configure.md)限制僅訪問授權的 IP 位址。
  - 對於託管實例，使用網路安全性群組 （NSG） 僅將埠 3342 的訪問限制為所需的資源。 有關詳細資訊，請參閱將[Azure SQL 資料庫託管實例安全地用於公共終結點](sql-database-managed-instance-public-endpoint-securely.md)。 

> [!NOTE]
> 預設情況下，未啟用託管實例公共終結點，並且必須顯式啟用該終結點。 如果公司策略不允許使用公共終結點，請使用[Azure 策略](../governance/policy/overview.md)首先防止啟用公共終結點。

- 設置 Azure 網路元件： 
  - 遵循[Azure 網路安全的最佳做法](../security/fundamentals/network-best-practices.md)。
  - 根據 Azure 虛擬網路中概述的最佳做法規劃虛擬網路 （VNet） 配置[，常見問題解答和](../virtual-network/virtual-networks-faq.md)計畫。 
  - 將 VNet 分段到多個子網中，並將資源配置給同一子網（例如前端與後端資源）。
  - 使用[網路安全性群組 （NSG）](../virtual-network/security-overview.md)控制 Azure VNet 邊界內子網之間的流量。
  - 為訂閱啟用[Azure 網路觀察程式](../network-watcher/network-watcher-monitoring-overview.md)以監視入站和出站網路流量。

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>為連接到 Azure SQL 資料庫的安全連線配置電源 BI

**最佳實踐**：

- 對於 Power BI 桌面，盡可能使用私有資料路徑。 

- 根據[傳輸層安全 （TLS）](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)註冊表設置，通過在用戶端電腦上設置登錄機碼，確保 Power BI 桌面使用 TLS1.2 進行連接。 

- 使用 Power BI 通過[行級安全 （RLS）](https://docs.microsoft.com/power-bi/service-admin-rls)限制特定使用者的資料訪問。 

- 對於 Power BI 服務，請使用[本地資料閘道](https://docs.microsoft.com/power-bi/service-gateway-onprem)，牢記[限制和注意事項](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)。

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>配置應用服務以安全連線到 Azure SQL 資料庫

**最佳實踐**：

- 對於簡單的 Web 應用，通過公共終結點進行連接需要設置 **"允許 Azure 服務**到 ON"。 

- [將應用與 Azure 虛擬網路集成，](../app-service/web-sites-integrate-with-vnet.md)以便與託管實例進行專用資料路徑連接。 或者，您還可以部署具有[應用服務環境 （ASE） 的](../app-service/environment/intro.md)Web 應用。 

- 對於具有連接到 SQL 資料庫伺服器中的資料庫的 ASE 或 VNet 集成 Web 應用的 Web 應用，可以使用[VNet 服務終結點和 VNet 防火牆規則](sql-database-vnet-service-endpoint-rule-overview.md)限制從特定 VNet 和子網的訪問。 然後設置**允許 Azure 服務**關閉。 您還可以通過專用資料路徑將 ASE 連接到託管實例。  

- 確保 Web 應用已根據文章（使用[Azure 應用服務保護 PaaS Web 和移動應用程式的最佳做法](../security/security-paas-applications-using-app-services.md)）進行配置。 

- 安裝[Web 應用程式防火牆 （WAF）](../application-gateway/waf-overview.md)以保護 Web 應用免受常見漏洞和漏洞的攻擊。

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>配置 Azure VM 託管以安全連線到 Azure SQL 資料庫

**最佳實踐**：

- 在 Azure VM 的 NSG 上使用"允許"和"拒絕"規則的組合來控制可以從 VM 訪問哪些區域。

- 確保根據[Azure 中的 IaaS 工作負荷的安全最佳做法](../security/azure-security-iaas.md)一文配置 VM。

- 確保所有 VM 都與特定的 VNet 和子網相關聯。 

- 根據[有關強制隧道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)的指南，評估是否需要預設路由 0.0.0.0/Internet。 
  - 如果是 - 例如前端子網 - 則保留預設路由。
  - 如果沒有（例如，中介層或後端子網），則啟用強制隧道，以便 Internet 上的流量不會到達本地（即跨界）。 

- 如果使用對等互連或連接到本地，則實現[可選的預設路由](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes)。 

- 如果需要將 VNet 中的所有流量發送到網路虛擬裝置以進行資料包檢查，請實施[使用者定義的路由](../virtual-network/virtual-networks-udr-overview.md#user-defined)。 

- 使用[VNet 服務終結點](sql-database-vnet-service-endpoint-rule-overview.md)通過 Azure 骨幹絡安全訪問 PaaS 服務（如 Azure 存儲）。 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>防止分散式阻斷服務 （DDoS） 攻擊
分散式阻斷服務 （DDoS） 攻擊是惡意使用者嘗試向 Azure SQL 資料庫發送大量網路流量，目的是使 Azure 基礎結構不堪重負，並導致其拒絕有效的登錄和工作負載。

> 在： OSA 實踐#9

**如何實現**：

DDoS 保護作為 Azure 平臺的一部分自動啟用。 它包括始終打開的流量監視和對公共終結點的網路級攻擊的即時緩解。 

- 使用[Azure DDoS 保護](../virtual-network/ddos-protection-overview.md)監視與 VNet 中部署的資源關聯的公共 IP 位址。

- [對 Azure SQL 資料庫使用高級威脅保護](sql-database-threat-detection-overview.md)來檢測對資料庫的拒絕服務 （DoS） 攻擊。

**最佳實踐**：

- 遵循["最小化攻擊面"](#minimize-attack-surface)中描述的實踐，有助於最大限度地減少 DDoS 攻擊威脅。 

- 高級威脅防護**暴力 SQL 憑據**警報有助於檢測暴力攻擊。 在某些情況下，警報甚至可以區分滲透測試工作負載。 

- 對於連接到 SQL 資料庫的 Azure VM 託管應用程式： 
  - 按照建議限制 Azure 安全中心中面向 Internet 的終結點的訪問。 
  - 使用虛擬機器縮放集在 Azure VM 上運行應用程式的多個實例。 
  - 禁用互聯網上的 RDP 和 SSH，以防止暴力攻擊。 

## <a name="monitoring-logging-and-auditing"></a>監視、日誌記錄和審核  
本節引用的功能可説明您檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。 它還介紹了設定資料庫審核以跟蹤和捕獲資料庫事件的最佳做法。

### <a name="protect-databases-against-attacks"></a>保護資料庫免受攻擊 
高級威脅防護使您能夠通過針對異常活動提供安全警報來檢測和回應潛在威脅的發生。

**如何實現**：

- 使用[SQL 的高級威脅防護](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)來檢測訪問或利用資料庫的異常和潛在有害嘗試，包括：
  - SQL 注入攻擊。
  - 憑據被盜/洩露。
  - 特權濫用。
  - 資料滲漏。

**最佳實踐**：

- 為 Azure SQL 資料庫配置特定 SQL 資料庫伺服器或託管實例[的高級資料安全 （ADS）。](sql-database-advanced-data-security.md#getting-started-with-ads)   還可以通過切換到[Azure 安全中心標準層](../security-center/security-center-pricing.md)，為訂閱中的所有 SQL 資料庫伺服器和託管實例配置 ADS。 

- 為了獲得完整的調查體驗，建議啟用 [SQL 資料庫審核](sql-database-auditing.md)。 通過審核，可以跟蹤資料庫事件並將其寫入 Azure 存儲帳戶或 Azure 日誌分析工作區中的稽核記錄。 

### <a name="audit-critical-security-events"></a>審核關鍵安全事件
跟蹤資料庫事件可説明您瞭解資料庫活動。 您可以深入瞭解可能指示業務問題或可疑安全違規的差異和異常。 它還支援和促進遵守合規性標準。 

**如何實現**：

- 啟用 [SQL 資料庫審核](sql-database-auditing.md)以跟蹤資料庫事件並將其寫入 Azure 存儲帳戶、日誌分析工作區（預覽）或事件中心（預覽）中的稽核記錄。 

- 稽核記錄可以寫入 Azure 存儲帳戶、日誌分析工作區供 Azure 監視器日誌使用，也可以寫入事件中心使用事件中心。 您可以設定這些選項的任何組合，並將稽核記錄寫入至每個組合。 

**最佳實踐**：

- 通過在資料庫伺服器上配置[SQL 資料庫審核](sql-database-auditing.md)以審核事件，將審核該伺服器上的所有現有和新創建的資料庫。
- 預設情況下，稽核原則包括針對資料庫的所有操作（查詢、預存程序以及成功和失敗的登錄），這可能導致大量稽核記錄。 建議客戶[使用 PowerShell 為不同類型的操作和操作組配置審核](sql-database-auditing.md#subheading-7)。 配置此操作將有助於控制審核操作的數量，並將事件丟失的風險降至最低。 自訂審核配置允許客戶僅捕獲所需的審核資料。
- 稽核記錄可以直接在[Azure 門戶](https://portal.azure.com/)中使用，也可以直接從配置的存儲位置使用。 


> [!NOTE]
> 啟用對日誌分析的審核將產生基於引入率的成本。 請注意使用[此選項](https://azure.microsoft.com/pricing/details/monitor/)的相關成本，或者考慮將稽核記錄存儲在 Azure 存儲帳戶中。 

**更多資源**：

- [SQL 資料庫審核](sql-database-auditing.md)
- [SQL Server 稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>安全稽核記錄
限制對存儲帳戶的訪問，以支援職責分離，並將 DBA 與審核員分開。 

**如何實現**：

- 將稽核記錄保存到 Azure 存儲時，請確保對存儲帳戶的訪問僅限於最低安全原則。 控制誰有權訪問存儲帳戶。
    - 有關詳細資訊，請參閱[授權訪問 Azure 存儲](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

**最佳實踐**：

- 控制對審核目標的訪問是將 DBA 與審核員分開的一個關鍵概念。 

- 在審核對敏感性資料的訪問時，請考慮使用資料加密來保護資料，以避免資訊洩露給審核員。 有關詳細資訊，請參閱["保護來自高特權、未經授權的使用者正在使用的敏感性資料](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)"一節。

## <a name="security-management"></a>安全性管理

本節介紹管理資料庫安全狀態的不同方面和最佳實踐。 它包括確保資料庫配置為符合安全標準、發現以及分類和跟蹤資料庫中潛在敏感性資料訪問的最佳做法。 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>確保資料庫配置為滿足安全最佳實踐 

通過發現和修復潛在的資料庫漏洞，主動提高資料庫安全性。

**如何實現**：

- 啟用[SQL 漏洞評估](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)（VA） 以掃描資料庫的安全問題，並在資料庫上自動運行。

**最佳實踐**：

- 最初，在資料庫上運行 VA，並通過修復反對安全最佳實踐的失敗檢查來反覆運算。 為可接受的配置設置基線，直到掃描_乾淨_，或者所有檢查都已通過。  

- 將定期定期掃描配置為每週運行一次，並將相關人員配置為接收摘要電子郵件。 

- 查看每次每週掃描後的 VA 摘要。 對於任何發現的漏洞，請評估從以前的掃描結果漂移，並確定是否應解決檢查。 查看配置更改是否有合法原因。   

- 解決檢查並更新相關基線。 創建用於解決操作的票證項，並跟蹤這些操作，直到它們得到解決。 

**更多資源**：

- [SQL 漏洞評量](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [SQL 弱點評量服務可協助您識別資料庫弱點](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>識別和標記敏感性資料 

發現可能包含敏感性資料的列。 被視為敏感性資料的內容在很大程度上取決於客戶、合規性法規等，需要由負責該資料的使用者進行評估。 對列進行分類以使用基於高級的基於靈敏度的審核和保護方案。 

**如何實現**：

- 使用[SQL 資料發現和分類](sql-database-data-discovery-and-classification.md)來發現、分類、標記和保護資料庫中的敏感性資料。 
  - 在 SQL 資料發現和分類儀表板中查看自動探索創建的分類建議。 接受相關分類，以便您的敏感性資料持續使用分類標籤進行標記。 
  - 手動添加自動機制未發現的任何其他敏感資料欄位的分類。 
- 有關詳細資訊，請參閱[SQL 資料發現&分類](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification)。

**最佳實踐**：

- 定期監視分類儀表板，以便準確評估資料庫的分類狀態。 可以匯出或列印有關資料庫分類狀態的報告，以便出於合規性和審核目的共用。

- 持續監視 SQL 漏洞評估中建議的敏感性資料的狀態。 跟蹤敏感性資料發現規則，並標識建議分類列中的任何漂移。  

- 以適合組織特定需求的方式使用分類。 在 Azure 安全中心的[SQL 資訊保護](../security-center/security-center-info-protection-policy.md)策略中自訂資訊保護原則（敏感度標籤、資訊類型、發現邏輯）。 

### <a name="track-access-to-sensitive-data"></a>跟蹤對敏感性資料的訪問 
監視誰訪問敏感性資料並捕獲稽核記錄中敏感性資料的查詢。

**如何實現**：

- 將 SQL 審核和資料分類組合使用。 
  - 在[SQL 資料庫審核](sql-database-auditing.md)日誌中，您可以專門跟蹤對敏感性資料的訪問。 您還可以查看資訊，如訪問的資料及其敏感度標籤。 有關詳細資訊，請參閱[審核對敏感性資料的訪問](sql-database-data-discovery-and-classification.md#subheading-3)。 

**最佳實踐**：

- 請參閱審核和資料分類部分的最佳做法： 
  - [審核關鍵安全事件](#audit-critical-security-events) 
  - [識別和標記敏感性資料](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>視覺化安全性和合規性狀態 

使用統一的基礎架構安全管理系統來增強資料中心（包括 SQL 資料庫）的安全狀態。 查看有關資料庫安全性和合規性狀態的建議清單。

**如何實現**：

- 監視[Azure 安全中心](https://azure.microsoft.com/documentation/services/security-center/)中與 SQL 相關的安全建議和活動威脅。

## <a name="common-security-threats-and-potential-mitigations"></a>常見的安全威脅和潛在緩解措施

本節可説明您找到安全措施來抵禦某些攻擊媒介。 預計大多數緩解措施都可以通過遵循上述一個或多個安全準則來實現。

### <a name="security-threat-data-exfiltration"></a>安全威脅：資料滲漏

資料洩露是未經授權從電腦或伺服器複製、傳輸或檢索資料。 請參閱維琪百科上[資料滲出](https://en.wikipedia.org/wiki/Data_exfiltration)的定義。

通過公共終結點連接到 Azure SQL 資料庫伺服器會帶來資料洩露風險，因為它要求客戶向公共 IP 打開防火牆。  

**方案 1**：Azure VM 上的應用程式連接到 Azure SQL 資料庫伺服器中的資料庫。 惡意執行元件可以訪問 VM 並危及 VM。 在這種情況下，資料滲漏意味著使用惡意 VM 的外部實體連接到資料庫，複製個人資料，並將其存儲在 Blob 存儲中或其他 SQL 資料庫中。

**方案 2**：紅色 DBA。 此方案通常由來自受監管行業的安全敏感客戶提出。 在這種情況下，高許可權使用者可能會將資料從 Azure SQL 資料庫複製到資料擁有者無法控制的另一個訂閱。

**潛在的緩解措施**：

如今，Azure SQL 資料庫提供了以下技術來緩解資料滲漏威脅： 

- 在 Azure VM 的 NSG 上使用"允許"和"拒絕"規則的組合來控制可以從 VM 訪問哪些區域。 
- 如果使用 Azure SQL 資料庫伺服器（包含單例資料庫或彈性池），則設置以下選項：
  - 允許 Azure 服務關閉。
  - 僅通過設置 VNet 防火牆規則，允許來自包含 Azure VM 的子網的流量。
  - 使用[專用連結](sql-database-private-endpoint-overview.md)
- 對於託管實例，預設情況下使用專用 IP 訪問解決了惡意 VM 的第一個資料洩露問題。 打開子網上的子網委派功能，自動設置託管實例子網上限制最大的策略。
- Rogue DBA 問題更多地暴露了託管實例，因為它具有更大的表面積，並且客戶可以看到網路要求。 最好的緩解措施是應用本安全指南中的所有實踐，首先防止惡意 DBA 方案（而不僅僅是資料滲漏）。 始終加密是一種通過加密敏感性資料並保持 DBA 無法訪問金鑰來保護敏感性資料的方法。

## <a name="security-aspects-of-business-continuity-and-availability"></a>業務連續性和可用性的安全方面

大多數安全標準都通過實現冗余和容錯移轉功能來處理操作連續性方面的資料可用性，以避免單點故障。 對於災難場景，通常的做法是保留資料和日誌檔的備份。以下部分提供了內置於 Azure 中的功能的高級概述。 它還提供了可配置為滿足特定需求的其他選項： 

- Azure 提供內置的高可用性：[高可用性和 Azure SQL 資料庫](sql-database-high-availability.md) 

- 業務關鍵型層包括容錯移轉組、多可用性區域、完整和差異記錄備份以及預設情況下啟用的時間點還原備份：  
  - [高可用性和 Azure SQL 資料庫 - 區域冗余配置](sql-database-high-availability.md#zone-redundant-configuration)
  - [自動備份](sql-database-automated-backups.md)
  - [使用自動資料庫備份恢復 Azure SQL 資料庫 - 時間點還原](sql-database-recovery-using-backups.md#point-in-time-restore)

- 可以按照此處所述配置其他業務連續性功能，如跨不同 Azure 地理位置的自動容錯移轉組[：Azure SQL 資料庫的業務連續性概述](sql-database-business-continuity.md)

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure SQL 資料庫安全功能概述](sql-database-security-overview.md)
