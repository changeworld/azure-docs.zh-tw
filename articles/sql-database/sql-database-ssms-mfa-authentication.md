---
title: 使用多重 AAD 身份驗證
description: Azure SQL 資料庫和 Azure 突觸支援使用活動目錄通用身份驗證從 SQL 伺服器管理工作室 （SSMS） 建立連接。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255869"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>將多重 AAD 身份驗證與 Azure SQL 資料庫和 Azure 突觸分析（對 MFA 的 SSMS 支援）
Azure SQL 資料庫和 Azure 突觸支援使用*活動目錄通用身份驗證*從 SQL 伺服器管理工作室 （SSMS） 建立連接。 本文討論了各種身份驗證選項之間的差異，以及與使用通用身份驗證相關的限制。 

**下載最新的 SSMS** - 在用戶端電腦上，從[下載 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下載最新版的 SSMS。 


對於本文中討論的所有功能，請至少使用 2017 年 7 月版本 17.2。  最新的連接對話方塊應類似于下圖：
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "完成使用者名框。")  

## <a name="the-five-authentication-options"></a>五個驗證選項  

活動目錄通用身份驗證支援兩種非互動式身份驗證方法：
    - `Active Directory - Password`認證
    - `Active Directory - Integrated`認證

還有兩種非互動式身份驗證模型，可用於許多不同的應用（ADO.NET、JDCB、ODC 等）。 這兩種方法永遠不會導致彈出對話方塊： 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

互動式方法是，也支援 Azure 多重要素驗證 （MFA） 是： 
- `Active Directory - Universal with MFA` 


Azure MFA 有助於保護資料和應用程式的存取，同時又滿足使用者對簡單登入程序的需求。 它利用各種簡單的驗證選項來提供強大的驗證 (包括電話、簡訊、含有 Pin 的智慧卡或行動應用程式通知)，讓使用者能夠選擇自己喜歡的方式。 搭配 Azure AD 使用互動式 MFA 時，會出現快顯對話方塊以進行驗證。

如需 Multi-Factor Authentication 的說明，請參閱 [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)。
如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 網域名稱或租用戶 ID 參數   

從 [SSMS 第 17 版](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)開始，從其他 Azure Active Directory 匯入目前 Active Directory 的使用者 (如來賓使用者)，可以在連線時提供 Azure AD 網域名稱或租用戶 ID。 來賓使用者包括從其他 Azure AD、Microsoft 帳戶 (例如 outlook.com、hotmail.com、live.com) 或其他帳戶 (例如 gmail.com) 邀請的使用者。 此資訊可讓 [Active Directory 通用驗證搭配 MFA 驗證]**** 識別正確的驗證授權單位。 支援 Microsoft 帳戶 (MSA) (如 outlook.com、hotmail.com、live.com) 或非 MSA 帳戶時也需要此選項。 所有要使用通用驗證來進行驗證的這些使用者，皆必須輸入他們的 Azure AD 網域名稱或租用戶 ID。 此參數代表目前與 Azure 伺服器連結的 Azure AD 網域名稱/租用戶 ID。 例如，如果 Azure 伺服器與 Azure AD 網域 `contosotest.onmicrosoft.com` 相關聯，其中託管的使用者 `joe@contosodev.onmicrosoft.com` 是從 Azure AD 網域 `contosodev.onmicrosoft.com` 匯入，則用於驗證此使用者的必要網域名稱為 `contosotest.onmicrosoft.com`。 如果使用者是 Azure AD (與 Azure 伺服器連結) 的原生使用者，不是 MSA 帳戶，則不需要網域名稱或租用戶 ID。 要輸入參數（以 SSMS 版本 17.2 開頭），請在"**連接到資料庫"** 對話方塊中完成對話方塊，選擇 **"活動目錄 - 具有 MFA**身份驗證通用"，按一下 **"選項**"，完成**使用者名**框，然後按一下"**連接屬性**"選項卡。選中**AD 功能變數名稱或租戶 ID**框，並提供身份驗證許可權，如租戶 ID 的功能變數名稱 **（contosotest.onmicrosoft.com）** 或 GUID。  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

如果您正在運行 SSMS 18.x 或更高版本，則來賓使用者不再需要 AD 功能變數名稱或租戶 ID，因為 18.x 或更高版本會自動識別它。

   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 企業對企業支援   
Azure AD 使用者作為來賓使用者支援 Azure AD B2B 方案（請參閱[什麼是 Azure B2B 協作](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)），只能作為在當前 Azure AD 中創建的組的成員連接到 SQL 資料庫和 Azure 突觸，並使用給定資料庫中的`CREATE USER`Transact-SQL 語句手動映射。 例如，如果 `steve@gmail.com` 受邀加入 Azure AD `contosotest` (與 Azure Ad 網域`contosotest.onmicrosoft.com`)，則必須在 Azure AD 中建立包含 `steve@gmail.com` 成員的 Azure AD 群組 (例如 `usergroup`)。 然後，必須通過執行 Transact-SQL`CREATE USER [usergroup] FROM EXTERNAL PROVIDER`語句，為特定資料庫（即 MyDatabase）創建此組。 建立資料庫使用者後，使用者 `steve@gmail.com` 就可以使用 SSMS 驗證選項 `Active Directory – Universal with MFA support` 來登入 `MyDatabase`。 根據預設，使用者群組只有 connect 權限，而任何進一步的資料存取權則需以一般方式進行授與。 請注意，身為來賓使用者的使用者 `steve@gmail.com` 必須核取此方塊，並且在 SSMS [連線屬性]**** 對話方塊中新增 AD 網域名稱 `contosotest.onmicrosoft.com`。 [AD 網域名稱或租用戶 ID]**** 選項僅對 [通用驗證搭配 MFA 連線] 選項提供支援，否則會呈現灰色。

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>SQL 資料庫和 Azure 突觸的通用身份驗證限制
- SSMS 和 SqlPackage.exe 是目前唯一透過 Active Directory 通用驗證，針對 MFA 啟用的工具。
- SSMS 17.2 版支援使用通用驗證搭配 MFA 的多使用者同時存取。 17.0 和 17.1 版，使用通用驗證將 SSMS 的登入限制為單一 Azure Active Directory 帳戶。 若要以另一個 Azure AD 帳戶登入，您必須使用另一個 SSMS 執行個體 (這項限制僅限於 Active Directory 通用驗證；您可以使用 Active Directory 密碼驗證、Active Directory 整合式驗證或 SQL Server 驗證來登入不同的伺服器)。
- SSMS 支援 Active Directory 通用驗證，可使用物件總管、查詢編輯器及查詢存放區視覺效果。
- SSMS 17.2 版針對匯出/擷取/部署資料資料庫提供 DacFx 精靈支援。 特定使用者使用通用驗證透過初始驗證對話方塊進行驗證後，DacFx 精靈的運作方式與所有其他驗證方法相同。
- SSMS 資料表設計工具不支援通用驗證。
- 除了您必須使用支援的 SSMS 版本之外，Active Directory 通用驗證並沒有其他軟體需求。  
- 通用驗證的 Active Directory 驗證程式庫 (ADAL) 版本已更新為最新的 ADAL.dll 3.13.9 可用發行版本。 請參閱 [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。  


## <a name="next-steps"></a>後續步驟

- 如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。
- 授與對資料庫的其他存取權：[SQL Database 驗證和授權：授與存取權](sql-database-manage-logins.md)  
- 確定其他人可透過防火牆連線：[使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)。  
- [使用 SQL 資料庫或 Azure 突觸配置和管理 Azure 活動目錄身份驗證](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [將 BACPAC 檔案匯入到新的 Azure SQL Database](../sql-database/sql-database-import.md)  
- [將 Azure SQL 資料庫匯出至 BACPAC 檔案](../sql-database/sql-database-export.md)  
- C# 介面 [IUniversalAuthProvider 介面](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- 使用 [Active Directory- 通用 MFA]**** 驗證時，自 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 開始可以使用 ADAL 追蹤功能。 預設為關閉，您可以使用 [Azure 服務]****、[Azure 雲端]****、[ADAL 輸出視窗的追蹤層級]**** 下的 [工具]****、[選項]**** 功能表，然後啟用 [檢視]**** 功能表中的 [輸出]****，以開啟 ADAL 追蹤功能。 選取 [Azure Active Directory 選項]**** 時，可以在輸出視窗中取得追蹤結果。  
