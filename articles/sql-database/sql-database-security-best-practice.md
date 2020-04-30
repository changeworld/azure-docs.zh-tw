---
title: 解決常見安全性需求的腳本 |Microsoft Docs
titleSuffix: Azure SQL Database
description: 本文提供 Azure SQL Database 中常見的安全性需求和最佳作法。
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 07e8d8690cb07cfde3e0def902bf6d8e7ba5b788
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232354"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>使用 Azure SQL Database 解決常見安全性需求的腳本

> [!NOTE]
> 本檔提供如何解決常見安全性需求的最佳作法。 並非所有的需求都適用于所有環境，而且您應該向資料庫和安全性小組諮詢要執行的功能。

## <a name="solving-common-security-requirements"></a>解決常見的安全性需求

本檔提供有關如何使用 Azure SQL Database 來解決新的或現有應用程式的一般安全性需求的指引。 它是依高階安全性區域來組織。 如需解決特定威脅，請參閱[常見的安全性威脅和可能](#common-security-threats-and-potential-mitigations)的緩和措施一節。 雖然某些呈現的建議適用于將應用程式從內部部署遷移至 Azure 時，但遷移案例並不是本檔的重點。

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>本指南中涵蓋的 Azure SQL Database 部署優惠

- [SQL 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-single-index)： [Azure SQL Database 伺服器](sql-database-servers.md)中的[單一資料庫](sql-database-single-database.md)和[彈性](sql-database-elastic-pool.md)集區
- [受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>本指南未涵蓋的 SQL 部署優惠

- Azure SQL 資料倉儲
- Azure SQL Vm （IaaS）
- 內部部署的 SQL Server

### <a name="audience"></a>適用對象

本指南的目標物件是有關如何保護 Azure SQL Database 的客戶問題。 對這個最佳做法文章感興趣的角色包括（但不限於）：

- 安全性架構設計人員
- 安全性管理員
- 合規性主管
- 隱私權主管
- 安全性工程師

### <a name="using-this-guide"></a><a id="using"></a>使用本指南

本檔旨在做為現有[Azure SQL Database 安全性](sql-database-security-overview.md)檔的隨附。

除非另有說明，否則建議您遵循每一節中所列的所有最佳作法，以達成個別的目標或需求。 為了符合特定的安全性合規性標準或最佳作法，重要的法規合規性控制會列在 [需求] 或 [目標] 區段的任何適當位置。 以下是本文所提及的安全性標準和法規：

- [FedRAMP](https://www.fedramp.gov/documents/)： ac-04、ac-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html)： CM-3，SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html)：存取控制、密碼編譯
- [Microsoft 營運安全性保證（OSA）作法](https://www.microsoft.com/en-us/securityengineering/osa/practices)：練習 #1-6 和 #9
- [NIST 特殊發行集800-53 安全性控制](https://nvd.nist.gov/800-53)： ac-5、ac-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library)：6.3.2、6.4。2

### <a name="feedback"></a>意見反應

我們打算繼續更新此處所列的建議和最佳作法。 請使用本文底部的**意見**反應連結，為此檔提供輸入或任何更正。

## <a name="authentication"></a>驗證

驗證是證明使用者宣告身分的程序。 Azure SQL Database 支援兩種驗證類型：

- SQL 驗證
- Azure Active Directory 驗證

> [!NOTE]
> 所有工具和協力廠商應用程式可能不支援 Azure Active Directory 驗證。

### <a name="central-management-for-identities"></a>身分識別的集中管理

中央身分識別管理提供下列優點：

- 管理群組帳戶及控制使用者權限，而不需要跨 Azure SQL Database 伺服器和資料庫複製登入。
- 簡化且彈性的版權管理。
- 大規模管理應用程式。

**如何執行**：

- 使用 Azure Active Directory （Azure AD）驗證來進行集中式身分識別管理。

**最佳做法**：

- 建立 Azure AD 租使用者，並[建立使用者](../active-directory/fundamentals/add-users-azure-active-directory.md)來代表人類的使用者，並建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md)來代表應用程式、服務和自動化工具。 服務主體相當於 Windows 和 Linux 中的服務帳戶。 

- 透過群組指派將資源的存取權限指派給 Azure AD 主體：建立 Azure AD 群組、授與群組的存取權，以及將個別成員新增至群組。 在您的資料庫中，建立對應 Azure AD 群組的自主資料庫使用者。 若要在資料庫內指派許可權，請將與您的 Azure AD 群組相關聯的使用者，放在具有適當許可權的資料庫角色中。
  - 請參閱文章：[使用 Sql 設定及管理 Azure Active Directory 驗證](sql-database-aad-authentication-configure.md)和[使用 AZURE AD 以使用 sql 進行驗證](sql-database-aad-authentication.md)。
  > [!NOTE]
  > 在受控實例中，您也可以建立對應到 master 資料庫中 Azure AD 主體的登入。 請參閱[CREATE LOGIN （transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。

- 使用 Azure AD 群組可簡化版權管理和群組擁有者，而資源擁有者可以在群組中新增/移除成員。 

- 為每個 SQL DB 伺服器的 Azure AD 系統管理員建立個別的群組。

  - 請參閱[為您的 Azure SQL Database 伺服器布建 Azure Active Directory 系統管理員一](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)文。

- 使用 Azure AD audit 活動報告監視 Azure AD 群組成員資格變更。 

- 若為受控實例，則需要個別的步驟來建立 Azure AD 系統管理員。 
  - 請參閱[為您的受控實例布建 Azure Active Directory 系統管理員一](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)文。 

> [!NOTE]
> - Azure AD 驗證會記錄在 Azure SQL audit 記錄檔中，但不會在 Azure AD 登入記錄中。
> - 在 Azure 中授與的 RBAC 許可權不會套用至 Azure SQL DB 許可權。 這類許可權必須使用現有的 SQL 許可權，以手動方式在 SQL DB 中建立/對應。
> - 在用戶端上，Azure AD 驗證需要存取網際網路，或透過使用者定義的路由（UDR）存取 VNet。
> - Azure AD 存取權杖會在用戶端快取，且其存留期取決於權杖設定。 請參閱[Azure Active Directory 中可設定的權杖存留期](../active-directory/develop/active-directory-configurable-token-lifetimes.md)文章
> - 如需疑難排解 Azure AD 驗證問題的指引，請參閱下列 blog：<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> 提及于： OSA 實務 #2，ISO 存取控制（AC）

Azure 多重要素驗證（MFA）藉由要求多種形式的驗證來提供額外的安全性。

**如何執行**：

- 使用條件式存取在 Azure AD 中[啟用 MFA](../active-directory/authentication/concept-mfa-howitworks.md) ，並使用互動式驗證。 

- 替代方式是針對整個 Azure AD 或 AD 網域啟用 MFA。

**最佳做法**：

- 啟用 Azure AD 中的條件式存取（需要 Premium 訂用帳戶）。 
  - 請參閱[Azure AD 中的條件式存取](../active-directory/conditional-access/overview.md)一文。  

- 使用 Azure AD 條件式存取，為選取的群組建立 Azure AD 群組並啟用 MFA 原則。 
  - 請參閱[規劃條件式存取部署](../active-directory/conditional-access/plan-conditional-access.md)一文。 

- 您可以針對整個 Azure AD 或與 Azure AD 同盟的整個 Active Directory 啟用 MFA。 

- 針對 SQL DB 使用 Azure AD 互動式驗證模式，其中會以互動方式要求密碼，然後進行 MFA 驗證：      
  - 在 SSMS 中使用通用驗證。 請參閱[使用多因素 AAD 驗證搭配 Azure SQL Database 和 Azure SQL 資料倉儲（適用于 MFA 的 SSMS 支援）](sql-database-ssms-mfa-authentication.md)一文。
  - SQL Server Data Tools （SSDT）中支援使用互動式驗證。 請參閱[SQL Server Data Tools 中的 Azure Active Directory 支援（SSDT）](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current)一文。
  - 使用支援 MFA 的其他 SQL 工具。 
    - SSMS Wizard 對匯出/解壓縮/部署資料庫的支援  
    - [sqlpackage .exe](https://docs.microsoft.com/sql/tools/sqlpackage)：選項 '/ua ' 
    - [Sqlcmd 公用程式](https://docs.microsoft.com/sql/tools/sqlcmd-utility)：選項-G （互動式）
    - [Bcp 公用程式](https://docs.microsoft.com/sql/tools/bcp-utility)：選項-G （互動式） 

- 使用具有 MFA 支援的互動式驗證，來執行您的應用程式以連接到 Azure SQL Database。 
  - 請參閱[使用 Azure 多重要素驗證連接到 Azure SQL Database 一](active-directory-interactive-connect-azure-sql-db.md)文。 
  > [!NOTE]
  > 此驗證模式需要以使用者為基礎的身分識別。 在使用可略過個別 Azure AD 使用者驗證（例如使用 Azure 資源的受控識別）的信任身分識別模型的情況下，不適用 MFA。

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>將使用者密碼型驗證的使用降至最低

> 提及于： OSA 實務 #4，ISO 存取控制（AC）

以密碼為基礎的驗證方法是較弱的驗證形式。 認證可能會遭到入侵或錯誤地提供。

**如何執行**：

- 使用 Azure AD 的整合式驗證，以避免使用密碼。

**最佳做法**：

- 使用 Windows 認證的單一登入驗證。 將內部部署 AD 網域與 Azure AD 同盟，並使用整合式 Windows 驗證（適用于具有 Azure AD 的已加入網域的電腦）。
  - 請參閱[Azure AD 整合式驗證的 SSMS 支援](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication)一文。

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>將以密碼為基礎的驗證應用程式的使用降至最低 

> 提及于： OSA 實務 #4，ISO 存取控制（AC）

**如何執行**：

- 啟用 Azure 受控識別。 您也可以使用整合式或以憑證為基礎的驗證。 

**最佳做法**：

- 使用[適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。
  - [系統指派的受控識別](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [使用具有受控識別的 app service Azure SQL Database （不需變更程式碼）](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- 針對應用程式使用以憑證為基礎的驗證。 
  - 請參閱此程式[代碼範例](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)。 

- 針對整合式同盟網域和已加入網域的電腦使用 Azure AD 驗證（請參閱上面的一節）。
  - 請參閱[整合式驗證的範例應用程式](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)。

### <a name="protect-passwords-and-secrets"></a>保護密碼和秘密

針對不肇因密碼的情況，請確定其受到保護。

**如何執行**：

- 使用 Azure Key Vault 來儲存密碼和秘密。 適用時，請將 MFA 用於具有 Azure AD 使用者的 Azure SQL Database。

**最佳做法**：

- 如果無法避免密碼或秘密，請將使用者密碼和應用程式秘密儲存在 Azure Key Vault 中，並透過 Key Vault 存取原則來管理存取權。 

- 各種不同的應用程式開發架構也可以提供架構特定的機制來保護應用程式中的秘密。 例如： [ASP.NET 核心應用程式](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows)。

### <a name="use-sql-authentication-for-legacy-applications"></a>針對繼承應用程式使用 SQL 驗證 

SQL 驗證是指使用使用者名稱和密碼連接到 Azure SQL Database 時的驗證。 必須在每個 SQL Database 伺服器或受控實例中建立登入，並在每個資料庫中建立一個使用者。

**如何執行**：

- 使用 SQL 驗證。

**最佳做法**：

- 以伺服器管理員的身分，建立登入和使用者。 除非使用含有密碼的自主資料庫使用者，否則所有密碼都會儲存在 master 資料庫中。
  - 請參閱[控制和授與資料庫存取權的 SQL Database 和 SQL 資料倉儲](sql-database-manage-logins.md)一文。

## <a name="access-management"></a>存取管理

存取管理（也稱為授權）是控制和管理授權的使用者存取權和許可權以 Azure SQL Database 的程式。

### <a name="implement-principle-of-least-privilege"></a>執行最低許可權原則

> 在中所述： FedRamp 控制 AC-06、NIST： AC-6、OSA 實務 #3

最低許可權的原則規定使用者不應擁有比完成其工作所需更多的許可權。 如需詳細資訊，請參閱[這](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)篇文章：足夠的系統管理。

**如何執行**：

只指派必要的[許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)以完成必要的工作：

- 在 SQL 資料庫中： 
    - 使用細微許可權和使用者定義資料庫角色（或 MI 中的伺服器角色）： 
        1. 建立所需的角色
            - [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [CREATE SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. 建立必要的使用者
            - [建立使用者](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. 將使用者新增為角色的成員 
            - [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. 然後將許可權指派給角色。 
            - [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - 請務必不要將使用者指派給不必要的角色。

- 在 Azure Resource Manager：
  - 使用內建角色（如果有的話）或自訂 RBAC 角色，並指派必要的許可權。
    - [Azure 的內建角色](../role-based-access-control/built-in-roles.md) 
    - [適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)

**最佳做法**：

下列最佳作法是選擇性的，但會導致安全性策略的管理能力和支援性較佳： 

- 可能的話，請從最低可能的許可權集開始，並在有真正的必要（和理由）時，逐一新增許可權，而不是相反的方法：逐步採取許可權。 

- 避免將許可權指派給個別使用者。 請改用一致的角色（資料庫或伺服器角色）。 角色有助於對報告和疑難排解許可權有很大的説明。 （Azure RBAC 僅支援透過角色指派的許可權）。 

- 以所需的確切許可權建立和使用自訂角色。 實務中使用的一般角色： 
  - 安全性部署 
  - 系統管理員 
  - 開發人員 
  - 支援人員 
  - 稽核員 
  - 自動化進程 
  - 終端使用者 
  
- 只有當角色的許可權符合使用者所需的許可權時，才使用內建角色。 您可以將使用者指派給多個角色。 

- 請記住，SQL Server 資料庫引擎中的許可權可套用至下列範圍。 範圍越小，授與許可權的影響就越小： 
  - Azure SQL Database server （master 資料庫中的特殊角色） 
  - 資料庫 
  - 結構描述
      - 最佳做法是使用架構來授與資料庫內的許可權。 （另請參閱：[適用于 SQL Server 的架構設計：建議在考慮安全性的架構設計](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)）
  - 物件（資料表、view、procedure 等） 
  > [!NOTE]
  > 不建議在物件層級上套用許可權，因為此層級會在整體的執行中增加不必要的複雜度。 如果您決定使用物件層級的許可權，則應該清楚地記載。 同樣的，也適用于資料行層級許可權，因為相同的原因，這是較少的建議。 也請注意，根據預設，資料表層級的[DENY](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql)不會覆寫資料行層級的授與。 這需要啟用[通用條件合規性伺服器](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)設定。

- 使用[弱點評定（VA）](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)執行定期檢查，以測試是否有太多許可權。

### <a name="implement-separation-of-duties"></a>實行職責分離

> 在中所述： FedRamp： AC-04、NIST： AC-5、ISO： A. 6.1.2、PCI 6.4.2、SOC： CM-3、SDL-3

責任區隔（也稱為「責任劃分」）說明將敏感性工作分割成多個指派給不同使用者的工作的需求。 區分責任有助於防止資料缺口。

**如何執行**：

- 識別所需的責任分隔層級。 範例： 
  - 開發/測試與生產環境之間 
  - 安全性取向的敏感性工作與資料庫管理員（DBA）管理層級工作與開發人員工作。 
    - 範例：審核員，建立角色層級安全性（RLS）的安全性原則，使用 DDL 許可權來執行 SQL Database 物件。

- 識別存取系統的完整使用者（和自動化進程）階層。

- 根據所需的使用者群組建立角色，並將許可權指派給角色。 
  - 針對 Azure 入口網站中的管理層級工作，或透過 PowerShell-自動化會使用 RBAC 角色。 尋找符合需求的內建角色，或使用可用的許可權建立自訂的 RBAC 角色 
  - 在受控實例中建立伺服器範圍工作（建立新的登入、資料庫）的伺服器角色。 
  - 建立資料庫層級工作的資料庫角色。

- 針對某些敏感性工作，請考慮建立由憑證簽署的特殊預存程式，以代表使用者執行工作。 數位簽署預存程式的其中一個重要優點是，如果程式已變更，就會立即移除授與先前程式版本的許可權。
  - 範例：[教學課程：使用憑證簽署預存程式](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- 在 Azure Key Vault 中使用客戶管理的金鑰來執行透明資料加密（TDE），以便在資料擁有者和安全性擁有者之間區分責任。 
  - 請參閱將[客戶管理的金鑰設定為從 Azure 入口網站 Azure 儲存體加密](../storage/common/storage-encryption-keys-portal.md)一文。 

- 若要確保 DBA 無法看到視為高度敏感的資料，而且仍然可以執行 DBA 工作，您可以使用 Always Encrypted 與角色隔離。 
  - 請參閱下列文章： [Always Encrypted 的金鑰管理](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)、[具有角色隔離的金鑰](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)布建，以及[使用角色分離的資料行主要金鑰輪替](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)。 

- 在使用 Always Encrypted 不可行的情況下，或至少沒有主要成本和工作甚至可能導致系統無法使用時，就可以透過使用補償控制項來進行危害，例如： 
  - 程式中的人為介入。 
  - Audit 線索–如需有關審核的詳細資訊，請參閱[審核重大安全性事件](#audit-critical-security-events)。

**最佳做法**：

- 請確定開發/測試和生產環境使用不同的帳戶。 不同的帳戶有助於符合測試 & 生產系統的分離。

- 避免將許可權指派給個別使用者。 請改用一致的角色（資料庫或伺服器角色）。 擁有角色有助於大幅提供報告和疑難排解許可權。

- 當權限完全符合所需的許可權時，請使用內建角色–如果多個內建角色的擁有權限聯集導致100% 相符，您也可以同時指派多個角色。 

- 當內建角色授與太多許可權或許可權不足時，建立和使用使用者定義的角色。 

- 角色指派也可以暫時完成，也就是在 T-sql 的 SQL 代理程式作業步驟中，或使用適用于 RBAC 角色的 Azure PIM，以動態方式區分責任（DSD）。 

- 請確定 Dba 沒有加密金鑰或金鑰存放區的存取權，而且有權存取金鑰的安全性系統管理員又無法再存取該資料庫。 使用可延伸[金鑰管理（EKM）](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-ekm)可以更輕鬆地完成這項分割。 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)可以用來執行 EKM。 

- 請一律確定有安全性相關動作的審核記錄。 

- 您可以取得內建 RBAC 角色的定義，以查看所使用的許可權，並根據這些透過 PowerShell 的摘錄和 cumulations 建立自訂角色。

- 由於 db_owner 資料庫角色的任何成員都可以變更透明資料加密（TDE）之類的安全性設定，或變更 SLO，因此應該謹慎授與此成員資格。 不過，有許多工需要 db_owner 許可權。 變更任何資料庫設定（例如變更 DB 選項）之類的工作。 「審核」在任何解決方案中扮演著重要的角色。

- 您無法限制 db_owner 的許可權，因此可防止系統管理帳戶查看使用者資料。 如果資料庫中有高度敏感的資料，Always Encrypted 可以用來安全地防止 db_owners 或任何其他 DBA 看到它。

> [!NOTE]
> 達成責任區分（SoD）是安全性相關或疑難排解工作的挑戰。 其他領域（例如開發和終端使用者角色）更容易隔離。 大部分的合規性相關控制項都允許在其他解決方案不可行時，使用替代的控制函數，例如進行審核。

對於想要深入瞭解 SoD 的讀者，建議您採用下列資源： 

- 針對 Azure SQL Database：  
  - [控制及授與 SQL Database 和 SQL 資料倉儲的資料庫存取權](sql-database-manage-logins.md)
  - [應用程式開發人員職責的引擎分離](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [SQL Server 2014 中的職責分離](https://www.microsoft.com/download/details.aspx?id=39269)
  - [在 SQL Server 中簽署預存程序](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- 針對 Azure 資源管理：
  - [Azure 的內建角色](../role-based-access-control/built-in-roles.md) 
  - [適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)
  - [使用 Azure AD Privileged Identity Management 提升存取權](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>執行一般程式碼審查

> 于： PCI：6.3.2、SOC： SDL-3 中提及 

責任區隔不限於資料庫中的資料，但包含應用程式代碼。 惡意程式碼可能會規避安全性控制措施。 將自訂程式碼部署到生產環境之前，必須先檢查部署的內容。

**如何執行**：

- 使用支援原始檔控制的資料庫工具，例如 Azure Data Studio。 

- 執行隔離的程式碼部署程式。

- 在認可至主要分支之前，個人（程式碼本身以外的人）必須檢查程式碼是否有潛在的許可權提升風險，以及惡意的資料修改以防止詐騙和 rogue 存取。 這可以使用原始檔控制機制來完成。

**最佳做法**：

- 標準化：它可協助您執行標準程式，以遵循任何程式碼更新。 

- 弱點評估包含的規則會檢查是否有過多的許可權、使用舊的加密演算法，以及資料庫架構中的其他安全性問題。 

- 您可以在 QA 或測試環境中，使用先進的威脅防護來進行進一步的檢查，掃描是否容易遭受 SQL 插入式攻擊的程式碼。

- 應注意的範例： 
  - 從自動化的 SQL 程式碼更新部署內建立使用者或變更安全性設定。 
  - 預存程式，視提供的參數而定，會以不符合標準的方式更新資料格中的貨幣值。 

- 請確定執行審核的人員是原始程式碼作者以外的個人，並熟悉程式碼審查和安全編碼。

- 請務必瞭解程式碼的所有來源-變更。 程式碼可以在 T-sql 腳本中。 它可以是要執行的臨機操作命令，或是以視圖、函數、觸發程式和預存程式的形式來部署。 它可以是 SQL 代理程式作業定義的一部分（步驟）。 也可以從 SSIS 封裝、Azure Data Factory 和其他服務中執行。

## <a name="data-protection"></a>資料保護

資料保護是一組保護重要資訊免于受到加密或混淆的功能。

> [!NOTE]
> Microsoft 證明 Azure SQL Database 為符合 FIPS 140-2 層級1的規範。 這項作業是在確認嚴格使用 FIPS 140-2 層級1可接受的演算法和 FIPS 140-2 層級1驗證的演算法實例之後完成，包括需要的金鑰長度、金鑰管理、金鑰產生和金鑰儲存的一致性。 此證明旨在讓我們的客戶回應在處理資料或傳遞系統或應用程式時，使用 FIPS 140-2 Level 1 驗證實例的需求或需求。 在上述的語句中，我們定義了「FIPS 140-2 層級1符合規範」和「FIPS 140-2 層級1合規性」的條款，以示範其對美國和加拿大政府使用不同詞彙「FIPS 140-2 層級1驗證」的預期適用性。 


### <a name="encrypt-data-in-transit"></a>加密傳輸中的資料

> 于中所述： OSA 實務 #6，ISO 控制系列：密碼編譯

在您的用戶端與伺服器之間移動資料時，保護您的資料。 請參閱[網路安全性](#network-security)。

### <a name="encrypt-data-at-rest"></a>加密待用資料

> 于中所述： OSA 實務 #6，ISO 控制系列：密碼編譯

待用加密是保存在資料庫、記錄檔和備份檔案中的資料密碼編譯保護。

**如何執行**：

- 預設會針對 Azure SQL Database 中2017之後所建立的任何資料庫，啟用具有服務管理金鑰的[透明資料庫加密（TDE）](transparent-data-encryption-azure-sql.md) 。
- 在受控實例中，如果使用內部部署伺服器從還原作業建立資料庫，則會接受原始資料庫的 TDE 設定。 如果原始資料庫未啟用 TDE，建議您針對受控實例手動開啟 TDE。

**最佳做法**：

- 請勿在 master 資料庫中儲存需要待用加密的資料。 Master 資料庫無法以 TDE 加密。

- 如果您需要更高的透明度，並更精細地控制 TDE 保護，請在 Azure Key Vault 中使用客戶管理的金鑰。 Azure Key Vault 允許隨時撤銷許可權，以使資料庫無法存取。 您可以使用 Azure Key Vault，集中管理 TDE 保護裝置以及其他金鑰，或依自己的排程旋轉 TDE 保護裝置。

- 如果您是在 Azure Key Vault 中使用客戶管理的金鑰，請遵循下列文章：使用[Azure Key Vault 設定 TDE 的指導方針](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv)，以及[如何使用 AZURE KEY VAULT 設定異地 DR](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde)。

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>保護使用中的機密資料免于高許可權、未經授權的使用者

使用中的資料是在 SQL 查詢執行期間，儲存在資料庫系統記憶體中的資料。 如果您的資料庫儲存機密資料，您的組織可能需要確保高許可權的使用者無法在資料庫中查看敏感性資料。 高許可權使用者（例如組織中的 Microsoft 操作員或 Dba）應該能夠管理資料庫，但防止從 SQL Server 程式的記憶體中或藉由查詢資料庫來查看及可能 exfiltrating 敏感性資料。

決定哪些資料是機密的原則，以及敏感性資料是否必須在記憶體中加密，而且不能由純文字中的系統管理員存取，是專屬於您的組織和您必須遵守的合規性法規。 請參閱相關的需求：[識別並標記敏感性資料](#identify-and-tag-sensitive-data)。

**如何執行**：

- 使用[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)確保機密資料不會在 Azure SQL Database 中以純文字公開，即使在記憶體/使用中也一樣。 Always Encrypted 保護來自資料庫管理員（Dba）和雲端系統管理員（或可以模擬高許可權但未經授權的使用者）的資料，並讓您更充分掌控誰可以存取您的資料。

**最佳做法**：

- Always Encrypted 不是用來加密待用資料（TDE）或傳輸（SSL/TLS）的替代方式。 Always Encrypted 不應用於非敏感性資料，以將效能和功能的影響降至最低。 建議使用 Always Encrypted 搭配 TDE 和傳輸層安全性（TLS），以全面保護待用、傳輸中和使用中的資料。 

- 在生產資料庫中部署 Always Encrypted 之前，請先評估加密已識別之敏感性資料行的影響。 一般來說，Always Encrypted 會減少加密資料行上查詢的功能，而且有其他限制，列于[Always Encrypted 功能詳細資料](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details)中。 因此，您可能需要重新架構應用程式，以便在用戶端或/上重新執行功能，而不支援查詢，包括預存程式、函數、視圖和觸發程式的定義。 如果現有的應用程式不符合 Always Encrypted 的限制和限制，則可能無法使用加密的資料行。 雖然 Microsoft 工具的生態系統、支援 Always Encrypted 的產品和服務日益成長，但其中有一些無法與加密的資料行搭配使用。 加密資料行可能也會影響查詢效能，端視工作負載的特性而定。 

- 如果您使用 Always Encrypted 來保護來自惡意 Dba 的資料，請使用角色隔離來管理 Always Encrypted 金鑰。 使用角色隔離時，安全性系統管理員會建立實體金鑰。 DBA 會在資料庫中建立資料行主要金鑰和資料行加密金鑰中繼資料物件，以描述實體金鑰。 在此過程中，安全性系統管理員不需要存取資料庫，而且 DBA 不需要存取純文字格式的實體金鑰。 
  - 如需詳細資訊，請參閱[使用角色隔離管理金鑰](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation)一文。 

- 將您的資料行主要金鑰儲存在 Azure Key Vault 中，以方便管理。 避免使用 Windows 憑證存放區（通常是分散式金鑰存放區解決方案，而不是集中金鑰管理解決方案），讓金鑰管理更為困難。 

- 請仔細考慮使用多個金鑰（資料行主要金鑰或資料行加密金鑰）的取捨。 將金鑰數目保持為小型，以降低金鑰管理成本。 每個資料庫的一個資料行主要金鑰和一個資料行加密金鑰，通常在穩定狀態的環境中就已足夠（而不是在金鑰輪替的過程中）。 如果您有不同的使用者群組，您可能需要額外的金鑰，每個都使用不同的金鑰並存取不同的資料。  

- 依據您的合規性需求，輪替資料行主要金鑰。 如果您也需要輪替資料行加密金鑰，請考慮使用線上加密來將應用程式停機時間降到最低。 
  - 請參閱[效能和可用性考慮](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations)一文。 

- 如果需要支援資料的計算（相等），請使用決定性加密。 否則，請使用隨機加密。 請避免針對低熵資料集或具有公開已知散發的資料集使用決定性加密。 

- 如果您擔心協力廠商在合法而不同意的情況下存取您的資料，請確定可存取純文字中之金鑰和資料的所有應用程式和工具都是在 Microsoft Azure 雲端外部執行。 若沒有金鑰的存取權，協力廠商將無法解密資料，除非其略過加密。

- Always Encrypted 不容易支援授與金鑰的暫時存取權（和受保護的資料）。 例如，如果您需要與 DBA 共用金鑰，讓 DBA 能夠對機密和加密資料進行一些清理作業。 若要從 DBA 撤銷資料的存取權，唯一的方法就是輪替資料行加密金鑰和資料行主要金鑰，以保護資料，這是一項昂貴的作業。 

- 若要存取加密資料行中的純文字值，使用者必須具有資料行主要金鑰（CMK）的存取權，以保護資料行（在保存 CMK 的金鑰存放區中設定）。 使用者也必須擁有**VIEW ANY COLUMN MASTER KEY definition**和**VIEW ANY COLUMN ENCRYPTION KEY definition**資料庫許可權。

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>透過加密控制應用程式使用者對敏感性資料的存取

加密可做為確保只有可存取密碼編譯金鑰的特定應用程式使用者可以查看或更新資料。

**如何執行**：

- 使用資料格層級加密（CLE）。 如需詳細資訊，請參閱[資料行加密一](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data)文。 
- 請使用 Always Encrypted，但請留意其限制。 限制如下所示。

**最佳做法**

使用 CLE 時：

- 透過 SQL 許可權和角色控制金鑰的存取權。 

- 使用 AES （建議採用 AES 256）進行資料加密。 諸如 RC4、DES 和 TripleDES 等演算法已淘汰，因此不應使用，因為已知的弱點。 

- 使用非對稱金鑰/憑證（不是密碼）保護對稱金鑰，以避免使用3DES。 

- 使用資料格層級加密透過匯出/匯入（bacpac 檔案）來遷移資料庫時，請務必小心。 
  - 如需如何避免在遷移資料時遺失金鑰的建議事項，請參閱在[Azure SQL Database 中使用資料格層級加密的建議](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)一文，以及其他最佳作法指引。

請記住，Always Encrypted 主要是用來保護從 Azure SQL Database 的高許可權使用者使用的敏感性資料（雲端操作員、Dba）-請參閱[保護使用中的敏感性資料，使其免于具有高許可權的未經授權使用者](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)。 使用 Always Encrypted 保護來自應用程式使用者的資料時，請注意下列挑戰：

- 根據預設，所有支援 Always Encrypted 的 Microsoft 用戶端驅動程式都會維護資料行加密金鑰的全域（每個應用程式一個）快取。 一旦用戶端驅動程式藉由聯繫持有資料行主要金鑰的金鑰存放區來取得純文字資料行加密金鑰，就會快取純文字資料行加密金鑰。 這會讓多使用者應用程式的使用者隔離資料。 如果您的應用程式在與金鑰存放區（例如 Azure Key Vault）互動時模擬使用者，則在使用者的查詢使用資料行加密金鑰填入快取之後，需要相同索引鍵但由另一位使用者觸發的後續查詢，將會使用快取的索引鍵。 驅動程式不會呼叫金鑰存放區，也不會檢查第二個使用者是否具有存取資料行加密金鑰的許可權。 因此，即使使用者沒有金鑰的存取權，使用者也可以看到加密的資料。 若要在多使用者應用程式中達成使用者隔離，您可以停用資料行加密金鑰快取。 停用快取會導致額外的效能負擔，因為驅動程式將需要連線到金鑰存放區，以進行每項資料加密或解密操作。

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>保護資料免于應用程式使用者未經授權的流覽，同時保留資料格式
防止未經授權的使用者查看資料的另一種方法是，將資料模糊或遮罩，同時保留資料類型和格式，以確保使用者應用程式可以繼續處理和顯示資料。

**如何執行**：

- 使用[動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)來模糊處理資料表資料行。

> [!NOTE]
> Always Encrypted 無法與動態資料遮罩搭配使用。 您無法將相同的資料行加密並加上遮罩，這表示您必須設定保護使用中資料的優先順序，以及透過動態資料遮罩將應用程式使用者的資料加上遮罩。

**最佳做法**：

> [!NOTE]
> 動態資料遮罩無法用來保護高許可權使用者的資料。 遮罩原則不適用於具有系統管理存取權的使用者，例如 db_owner。

- 不允許應用程式使用者執行臨機操作查詢（因為他們可能能夠解決動態資料遮罩）。  
  - 如需詳細資訊，請參閱[略過使用推斷的遮罩或暴力密碼破解技術一](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques)文。  

- 使用適當的存取控制原則（透過 SQL 許可權、角色、RLS），以限制使用者在遮罩資料行中進行更新的許可權。 在資料行上建立遮罩，並不會防止更新該資料行。 當使用者在查詢遮罩資料行時接收到已遮罩的資料時，可以更新資料（如果有寫入權限的話）。    

-  動態資料遮罩不會保留遮罩值的統計屬性。 這可能會影響查詢結果（例如，包含篩選述詞的查詢或遮罩資料上的聯結）。

## <a name="network-security"></a>網路安全性
網路安全性是指存取控制和最佳作法，以保護傳輸中的資料以 Azure SQL Database。

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>設定我的用戶端安全地連接到 Azure SQL Database 
有關如何防止具有已知弱點的用戶端電腦和應用程式（例如，使用舊版 TLS 通訊協定和加密套件）連接到 Azure SQL Database 的最佳做法。

**如何執行**：

- 確定連接到 Azure SQL Database 的用戶端電腦使用[傳輸層安全性（TLS）](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)。

**最佳做法**：

- 設定所有應用程式和工具，以在啟用加密的情況下連接到 SQL DB 
  - Encrypt = On、TrustServerCertificate = Off （或等同于非 Microsoft 驅動程式）。 

- 如果您的應用程式使用不支援 TLS 的驅動程式，或支援較舊版本的 TLS，請盡可能更換驅動程式。 如果無法這麼做，請仔細評估安全性風險。 

- 藉由在連線到每個[傳輸層安全性（TLS）登錄設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10)Azure SQL Database 的用戶端電腦上停用 ssl 2.0、ssl 3.0、tls 1.0 和 tls 1.1 中的弱點，來減少攻擊媒介。 

- 檢查用戶端上可用的加密套件： [TLS/SSL （安全通道 SSP）中的加密套件](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel)。 具體而言，請停用每個設定[TLS 加密套件順序](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)的3des。 

- 針對 Azure SQL Database，會針對 Proxy 和重新導向連線類型強制執行加密。 如果您使用受控實例，請使用**Proxy**連線類型（預設值），因為這會從伺服器端強制加密。 重新**導向**連線類型目前不支援加密強制，而且僅適用于私人 IP 連接。 

- 如需詳細資訊，請參閱[AZURE SQL 連線架構-連線原則](sql-database-connectivity-architecture.md#connection-policy)。


### <a name="minimize-attack-surface"></a>將受攻擊面降至最低
將惡意使用者可能會遭受攻擊的功能數目降至最低。 執行 Azure SQL Database 的網路存取控制。

> 提及于： OSA 實務 #5

**如何執行**：

在 Azure SQL Database 伺服器（包含單一資料庫或彈性集區）中：
- 將 [允許存取 Azure 服務] 設定為 [關閉]。

- 使用 VNet 服務端點和 VNet 防火牆規則。

- 使用私人連結（預覽）。

在受控實例中：
- 遵循[網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)中的指導方針。 

**最佳做法**：

- 藉由在私用端點上連接來限制 Azure SQL Database 的存取（例如，使用私用資料路徑）： 
  - 受控實例可以在 VNet 內隔離，以避免外部存取。 位於相同區域或對等互連 VNet 中的應用程式和工具都可以直接存取。 不同區域中的應用程式和工具可以使用 VNet 對 VNet 連線或 ExpressRoute 線路對等互連來建立連接。 客戶應該使用網路安全性群組（NSG），將僅限埠1433的存取限制為需要存取受控實例的資源 
  - 針對 SQL Database 伺服器（包含單一資料庫或彈性集區），請使用[私人連結](sql-database-private-endpoint-overview.md)功能，為 VNet 內的 SQL Database 伺服器提供專用的私人 IP。 您也可以使用[Vnet 服務端點搭配 Vnet 防火牆規則](sql-database-vnet-service-endpoint-rule-overview.md)，來限制 SQL Database 伺服器的存取權。
  - 行動使用者應使用點對站 VPN 連線，透過資料路徑進行連線。
  - 連線到其內部部署網路的使用者應該使用站對站 VPN 連線或 ExpressRoute 來透過資料路徑進行連接。

- 您可以藉由連接到公用端點（例如，使用公用資料路徑）來存取 Azure SQL Database。 應考慮下列最佳作法： 
  - 若為 SQL Database 伺服器，請使用[IP 防火牆規則](sql-database-firewall-configure.md)來限制只有授權的 IP 位址才能存取。
  - 若為受控實例，請使用網路安全性群組（NSG），將透過埠3342的存取限制為所需的資源。 如需詳細資訊，請參閱透過[公用端點安全地使用 Azure SQL Database 受控實例](sql-database-managed-instance-public-endpoint-securely.md)。 

> [!NOTE]
> 受控實例公用端點預設不會啟用，而且必須明確啟用。 如果公司原則不允許使用公用端點，請使用[Azure 原則](../governance/policy/overview.md)來避免在一開始就啟用公用端點。

- 設定 Azure 網路元件： 
  - 遵循[適用于網路安全性的 Azure 最佳作法](../security/fundamentals/network-best-practices.md)。
  - 規劃[Azure 虛擬網路常見問題（FAQ）](../virtual-network/virtual-networks-faq.md)和方案中所述的每個最佳作法的虛擬網路（VNet）設定。 
  - 將 VNet 分割成多個子網，並將類似角色的資源指派給相同的子網（例如，前端與後端資源）。
  - 使用[網路安全性群組（nsg）](../virtual-network/security-overview.md)來控制 Azure VNet 界限內子網之間的流量。
  - 為您的訂用帳戶啟用[Azure 網路監看員](../network-watcher/network-watcher-monitoring-overview.md)，以監視輸入和輸出網路流量。

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>設定 Power BI 以 Azure SQL Database 的安全連線

**最佳做法**：

- 如 Power BI Desktop，請盡可能使用私用資料路徑。 

- 根據[傳輸層安全性（TLS）](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)登錄設定，在用戶端電腦上設定登錄機碼，以確定 POWER BI DESKTOP 使用 TLS 1.2 連接。 

- 透過 Power BI 的資料[列層級安全性（RLS）](https://docs.microsoft.com/power-bi/service-admin-rls)來限制特定使用者的資料存取。 

- 針對 Power BI 服務，請使用[內部部署資料閘道](https://docs.microsoft.com/power-bi/service-gateway-onprem)，[並記住限制和考慮](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)。

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>設定 App Service 以 Azure SQL Database 的安全連線

**最佳做法**：

- 若是簡單的 Web 應用程式，必須將 [**允許 Azure 服務**] 設定為 [開啟]，才能透過公用端點連接。 

- 將[您的應用程式與 Azure 虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md)，以取得與受控實例的私人資料路徑連線。 （選擇性）您也可以使用[App Service 環境（ASE）](../app-service/environment/intro.md)部署 Web 應用程式。 

- 針對使用 ASE 或 VNet 整合式 Web 應用程式連線到 SQL Database server 中的資料庫的 Web 應用程式，您可以使用[Vnet 服務端點和 Vnet 防火牆規則](sql-database-vnet-service-endpoint-rule-overview.md)來限制來自特定 VNet 和子網的存取。 然後將 [**允許 Azure 服務**] 設為 [關閉]。 您也可以透過私人資料路徑，將 ASE 連線到受控實例。  

- 請確定您的 Web 應用程式已根據文章中的設定，[使用 Azure App Service 保護 PaaS Web 和行動應用程式的最佳作法](../security/security-paas-applications-using-app-services.md)。 

- 安裝[Web 應用程式防火牆（WAF）](../application-gateway/waf-overview.md) ，以保護您的 web 應用程式免于遭遇常見的攻擊和弱點。

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>為 Azure SQL Database 的安全連線設定 Azure VM 裝載

**最佳做法**：

- 在 Azure Vm 的 Nsg 上使用允許和拒絕規則的組合，以控制可從 VM 存取的區域。

- 請確定您的 VM 已根據[Azure 中 IaaS 工作負載的安全性最佳作法](../security/azure-security-iaas.md)一文進行設定。

- 確定所有 Vm 都與特定的 VNet 和子網相關聯。 

- 根據[關於強制通道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)的指導方針，評估您是否需要預設路由 0.0.0.0/網際網路。 
  - 如果是（例如，前端子網），則保留預設路由。
  - 如果為 [否] （例如，仲介層或後端子網），則啟用 [強制通道]，讓流量無法透過網際網路連線到內部部署（a. k. 跨單位）。 

- 如果您使用對等互連或連線至內部部署，請執行[選用的預設路由](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes)。 

- 如果您需要將 VNet 中的所有流量傳送至網路虛擬裝置以進行封包檢查，請執行[使用者定義的路由](../virtual-network/virtual-networks-udr-overview.md#user-defined)。 

- 使用[VNet 服務端點](sql-database-vnet-service-endpoint-rule-overview.md)來安全存取 PaaS 服務，例如透過 Azure 骨幹網路的 Azure 儲存體。 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>防止分散式阻斷服務（DDoS）攻擊
分散式阻斷服務（DDoS）攻擊是由惡意使用者嘗試傳送大量網路流量給 Azure SQL Database，其目標是讓 Azure 基礎結構變得龐大，並導致它拒絕有效的登入和工作負載。

> 提及于： OSA 實務 #9

**如何執行**：

「DDoS 保護」會在 Azure 平臺中自動啟用。 其中包括 alwayson 流量監視，以及即時緩和公用端點上的網路層級攻擊。 

- 使用[Azure DDoS 保護](../virtual-network/ddos-protection-overview.md)來監視與 vnet 中所部署資源相關聯的公用 IP 位址。

- 使用[Advanced 威脅防護來進行 Azure SQL Database](sql-database-threat-detection-overview.md) ，以偵測對資料庫的拒絕服務（DoS）攻擊。

**最佳做法**：

- 請遵循[最小化攻擊面](#minimize-attack-surface)中所述的作法，協助將 DDoS 攻擊威脅降至最低。 

- Advanced 威脅防護**暴力密碼破解 SQL 認證**警示有助於偵測暴力密碼破解攻擊。 在某些情況下，警示甚至可以區分滲透測試工作負載。 

- 針對連接到 SQL Database 的 Azure VM 裝載應用程式： 
  - 遵循建議，透過 Azure 資訊安全中心中的網際網路面向端點來限制存取。 
  - 使用虛擬機器擴展集在 Azure Vm 上執行應用程式的多個實例。 
  - 停用來自網際網路的 RDP 和 SSH，以防止暴力密碼破解攻擊。 

## <a name="monitoring-logging-and-auditing"></a>監視、記錄和審核  
本節所指的功能可協助您偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。 它也會說明設定資料庫審核來追蹤和捕捉資料庫事件的最佳作法。

### <a name="protect-databases-against-attacks"></a>保護資料庫免于遭受攻擊 
「先進的威脅防護」可讓您藉由提供異常活動的安全性警示，偵測並回應潛在威脅。

**如何執行**：

- 使用[適用于 SQL 的先進威脅防護](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)，來偵測存取或惡意探索資料庫的異常且可能有害的嘗試，包括：
  - SQL 插入式攻擊。
  - 竊取/洩漏認證。
  - 許可權濫用。
  - 資料外泄。

**最佳做法**：

- 針對特定 SQL Database 伺服器或受控實例，設定 Azure SQL Database 的[Advanced Data Security （ADS）](sql-database-advanced-data-security.md#getting-started-with-ads) 。 您也可以藉由切換至[Azure 資訊安全中心標準層](../security-center/security-center-pricing.md)，為訂用帳戶中的所有 SQL Database 伺服器和受控實例設定廣告。 

- 如需完整的調查體驗，建議您啟用 [SQL Database 的審核](sql-database-auditing.md)。 使用審核功能，您可以追蹤資料庫事件，並將它們寫入 Azure 儲存體帳戶或 Azure Log Analytics 工作區中的 audit 記錄。 

### <a name="audit-critical-security-events"></a>審核重大安全性事件
追蹤資料庫事件可協助您瞭解資料庫活動。 您可以深入瞭解可能指出業務疑慮或可疑安全性違規的不一致和異常。 它也會啟用並促進遵循合規性標準。 

**如何執行**：

- 啟用 [SQL Database 審核](sql-database-auditing.md)來追蹤資料庫事件，並將它們寫入您 Azure 儲存體帳戶、log Analytics 工作區（預覽）或事件中樞（預覽）中的 audit 記錄。 

- 您可以使用事件中樞，將 Audit 記錄寫入 Azure 儲存體帳戶、Log Analytics 工作區，以供 Azure 監視器記錄取用，或用於事件中樞以供耗用量。 您可以設定這些選項的任何組合，並將稽核記錄寫入至每個組合。 

**最佳做法**：

- 藉由在資料庫伺服器上設定[SQL Database 審核](sql-database-auditing.md)來審核事件，將會審核該伺服器上所有現有和新建立的資料庫。
- 根據預設，稽核原則會針對資料庫包含所有動作（查詢、預存程式、成功和失敗的登入），而這可能會導致大量的 audit 記錄。 建議客戶[使用 PowerShell 來設定不同類型動作和動作群組的審核](sql-database-auditing.md#manage-auditing)。 設定此項將有助於控制已審核的動作數目，並將事件遺失的風險降到最低。 [自訂審核設定] 可讓客戶只捕捉所需的 audit 資料。
- 您可以直接在[Azure 入口網站](https://portal.azure.com/)中，或從已設定的儲存位置取用 Audit 記錄。 


> [!NOTE]
> 啟用 Log Analytics 的審核會根據內嵌速率產生成本。 請留意使用此[選項](https://azure.microsoft.com/pricing/details/monitor/)的相關成本，或考慮將 audit 記錄儲存在 Azure 儲存體帳戶中。 

**其他資源**：

- [SQL 資料庫稽核](sql-database-auditing.md)
- [SQL Server 稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>安全的審核記錄
限制對儲存體帳戶的存取，以支援責任分離，並將 DBA 與審計員分開。 

**如何執行**：

- 將 Audit 記錄檔儲存到 Azure 儲存體時，請確定儲存體帳戶的存取權僅限於最低安全性原則。 控制可存取儲存體帳戶的人員。
    - 如需詳細資訊，請參閱[授權存取 Azure 儲存體](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

**最佳做法**：

- 控制審核目標的存取是將 DBA 與審計員分開的重要概念。 

- 在審核敏感性資料的存取權時，請考慮使用資料加密來保護資料安全，以避免資訊洩漏給審核員。 如需詳細資訊，請參閱[保護使用中的敏感性資料，從具有高許可權的未經授權的使用者](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)。

## <a name="security-management"></a>安全性管理

本節說明管理資料庫安全性狀態的不同層面和最佳作法。 其中包含最佳作法，可確保您的資料庫設定為符合安全性標準，以探索及分類和追蹤資料庫中可能敏感性資料的存取權。 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>確定已設定資料庫以符合安全性最佳做法 

藉由探索並修復潛在的資料庫弱點，主動改善您的資料庫安全性。

**如何執行**：

- 啟用[SQL 弱點評定](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)（VA）來掃描您的資料庫是否有安全性問題，並在您的資料庫上定期自動執行。

**最佳做法**：

- 一開始，請在您的資料庫上執行 VA，並藉由修復相比安全性最佳做法的失敗檢查來逐一查看。 設定可接受設定的基準，直到掃描_結束，或_所有檢查都已通過為止。  

- 將週期性週期性掃描設定為每週執行一次，並設定相關人員以接收摘要電子郵件。 

- 檢查每個每週掃描後的 VA 摘要。 針對找到的任何弱點，評估先前掃描結果的漂移，並判斷是否應解決此檢查。 檢查是否有合理的設定變更原因。   

- 解決檢查並更新相關的基準。 建立用於解析動作的票證專案，並在解決後加以追蹤。 

**其他資源**：

- [SQL 弱點評定](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [SQL 弱點評量服務可協助您識別資料庫弱點](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>識別及標記敏感性資料 

探索可能包含敏感性資料的資料行。 所謂的敏感性資料主要取決於客戶、合規性法規等，而且需要由使用者負責評估該資料。 分類資料行，以使用先進的敏感性式審核和保護案例。 

**如何執行**：

- 使用[SQL 資料探索和分類](sql-database-data-discovery-and-classification.md)來探索、分類、標記和保護資料庫中的敏感性資料。 
  - 在 [SQL 資料探索與分類] 儀表板中，查看自動探索所建立的分類建議。 接受相關分類，讓您的機密資料持續以分類標籤標記。 
  - 針對自動機制未探索到的任何其他機密資料欄位，手動加入分類。 
- 如需詳細資訊，請參閱[SQL 資料探索 & 分類](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification)。

**最佳做法**：

- 定期監視 [分類] 儀表板，以精確評估資料庫的分類狀態。 您可以匯出或列印資料庫分類狀態的報表，以進行合規性和審核目的。

- 持續監視 SQL 弱點評定中的建議敏感性資料狀態。 追蹤敏感性資料探索規則，並找出建議的資料行中的任何漂移以進行分類。  

- 以針對貴組織特定需求量身打造的方式來使用分類。 在 Azure 資訊安全中心的[SQL 資訊保護](../security-center/security-center-info-protection-policy.md)原則中，自訂您的資訊保護原則（敏感度標籤、資訊類型、探索邏輯）。 

### <a name="track-access-to-sensitive-data"></a>追蹤機密資料的存取權 
監視存取機密資料的人員，並在 audit 記錄中捕捉機密資料的查詢。

**如何執行**：

- 使用 SQL Audit 和資料分類組合。 
  - 在您的[SQL Database Audit](sql-database-auditing.md)記錄檔中，您可以特別追蹤敏感性資料的存取權。 您也可以查看資訊，例如存取的資料，以及其敏感度標籤。 如需詳細資訊，請參閱[資料探索 & 分類](sql-database-data-discovery-and-classification.md)和[審核機密資料的存取權](sql-database-data-discovery-and-classification.md#audit-sensitive-data)。 

**最佳做法**：

- 請參閱「審核和資料分類」章節的最佳做法： 
  - [審核重大安全性事件](#audit-critical-security-events) 
  - [識別及標記敏感性資料](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>將安全性和合規性狀態視覺化 

使用統一的基礎結構安全性管理系統，可強化資料中心的安全性狀態（包括 SQL 資料庫）。 查看有關資料庫安全性和合規性狀態的建議清單。

**如何執行**：

- 在[Azure 資訊安全中心](https://azure.microsoft.com/documentation/services/security-center/)中監視 SQL 相關的安全性建議和作用中的威脅。

## <a name="common-security-threats-and-potential-mitigations"></a>常見的安全性威脅和可能的緩和措施

本節可協助您找出安全性措施，以防範特定的攻擊媒介。 您必須遵循上述一或多個安全性指導方針，來達成大部分的緩和措施。

### <a name="security-threat-data-exfiltration"></a>安全性威脅：資料外泄

資料外泄是指從電腦或伺服器進行未經授權的複製、傳送或抓取資料。 查看維琪百科上[外泄的資料](https://en.wikipedia.org/wiki/Data_exfiltration)定義。

透過公用端點連接到 Azure SQL Database server，會呈現資料外泄風險，因為它需要客戶將其防火牆開啟至公用 Ip。  

**案例 1**： Azure VM 上的應用程式會連接到 Azure SQL Database 伺服器中的資料庫。 Rogue 執行者會取得 VM 的存取權，並加以危害。 在此案例中，資料外泄表示使用 rogue VM 的外部實體會連線到資料庫、複製個人資料，並將其儲存在不同訂用帳戶中的 blob 儲存體或不同的 SQL Database。

**案例 2**：有 rouge DBA。 這種情況通常是由受管制產業的安全性敏感性客戶所引發。 在此案例中，高許可權使用者可能會將資料從 Azure SQL Database 複製到不是由資料擁有者控制的另一個訂用帳戶。

**可能**的緩和措施：

目前，Azure SQL Database 提供下列技術來減輕資料外泄威脅： 

- 在 Azure Vm 的 Nsg 上使用允許和拒絕規則的組合，以控制可從 VM 存取的區域。 
- 如果使用 Azure SQL Database 伺服器（包含單一資料庫或彈性集區），請設定下列選項：
  - 允許 Azure 服務關閉。
  - 藉由設定 VNet 防火牆規則，僅允許來自包含您 Azure VM 之子網的流量。
  - 使用[私人連結](sql-database-private-endpoint-overview.md)
- 針對受控實例，預設使用私人 IP 存取可解決 rogue VM 的第一個資料外泄問題。 開啟子網上的子網委派功能，以在受控實例子網上自動設定限制最嚴格的原則。
- 惡意的 DBA 顧慮會更容易受到受控實例的攻擊，因為它具有較大的介面區，而且客戶可以看到網路需求。 為此，最佳的緩和措施就是套用本安全性指南中的所有實務，以避免發生 Rogue DBA 案例（而不只是針對資料外泄）。 Always Encrypted 是用來保護機密資料的方法之一，就是將其加密並讓 DBA 無法存取金鑰。

## <a name="security-aspects-of-business-continuity-and-availability"></a>商務持續性和可用性的安全性層面

大部分的安全性標準都會以營運持續性的角度來處理資料可用性，並藉由執行冗余和損毀修復功能來避免單一失敗點。 在嚴重損壞狀況中，經常備份資料和記錄檔是常見的作法。下一節提供 Azure 內建功能的高階總覽。 它也會提供其他選項，讓您可以設定以符合特定需求： 

- Azure 提供內建的高可用性：[高可用性和 Azure SQL Database](sql-database-high-availability.md) 

- 「業務關鍵層」包含容錯移轉群組、多可用性區域、完整和差異記錄備份，以及預設啟用的時間點還原備份：  
  - [高可用性和 Azure SQL Database 區域的冗余設定](sql-database-high-availability.md#zone-redundant-configuration)
  - [自動備份](sql-database-automated-backups.md)
  - [使用自動資料庫備份來復原 Azure SQL Database-時間點還原](sql-database-recovery-using-backups.md#point-in-time-restore)

- 其他商務持續性功能（例如跨不同 Azure 地區的自動容錯移轉群組）可以設定如下所述：[使用 Azure SQL Database 的商務持續性總覽](sql-database-business-continuity.md)

## <a name="next-steps"></a>後續步驟

- 查看[Azure SQL Database 安全性功能的總覽](sql-database-security-overview.md)
