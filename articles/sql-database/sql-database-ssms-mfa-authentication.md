---
title: 使用多因素 AAD 驗證
description: Azure SQL Database 和 Azure Synapse 支援使用 Active Directory 通用驗證來自 SQL Server Management Studio （SSMS）的連接。
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194031"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>搭配使用多重要素 AAD 驗證與 Azure SQL Database 和 Azure Synapse 分析（適用于 MFA 的 SSMS 支援）
Azure SQL Database 和 Azure Synapse 支援使用*Active Directory 通用驗證*來自 SQL SERVER MANAGEMENT STUDIO （SSMS）的連接。 本文討論各種驗證選項之間的差異，以及與使用通用驗證相關聯的限制。 

**下載最新的 SSMS** - 在用戶端電腦上，從[下載 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下載最新版的 SSMS。 


如需本文中討論的所有功能，請使用至少2017年7月的版本17.2。  [最近的連接] 對話方塊看起來應該類似下圖：
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "完成 [使用者名稱] 方塊。")  

## <a name="the-five-authentication-options"></a>五個驗證選項  

Active Directory 通用驗證支援兩種非互動式驗證方法：
    - `Active Directory - Password` 驗證
    - `Active Directory - Integrated` 驗證

也有兩種非互動式驗證模型，可用於許多不同的應用程式（ADO.NET、JDCB、ODC 等等）。 這兩種方法永遠不會導致快顯對話方塊： 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

互動式方法也支援 Azure 多重要素驗證（MFA），也就是： 
- `Active Directory - Universal with MFA` 


Azure MFA 有助於保護資料和應用程式的存取，同時又滿足使用者對簡單登入程序的需求。 它利用各種簡單的驗證選項來提供強大的驗證 (包括電話、簡訊、含有 Pin 的智慧卡或行動應用程式通知)，讓使用者能夠選擇自己喜歡的方式。 搭配 Azure AD 使用互動式 MFA 時，會出現快顯對話方塊以進行驗證。

如需 Multi-Factor Authentication 的說明，請參閱 [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)。
如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 網域名稱或租用戶 ID 參數   

從 [SSMS 第 17 版](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)開始，從其他 Azure Active Directory 匯入目前 Active Directory 的使用者 (如來賓使用者)，可以在連線時提供 Azure AD 網域名稱或租用戶 ID。 來賓使用者包括從其他 Azure AD、Microsoft 帳戶 (例如 outlook.com、hotmail.com、live.com) 或其他帳戶 (例如 gmail.com) 邀請的使用者。 此資訊可讓 [Active Directory 通用驗證搭配 MFA 驗證]識別正確的驗證授權單位。 支援 Microsoft 帳戶 (MSA) (如 outlook.com、hotmail.com、live.com) 或非 MSA 帳戶時也需要此選項。 所有要使用通用驗證來進行驗證的這些使用者，皆必須輸入他們的 Azure AD 網域名稱或租用戶 ID。 此參數代表目前與 Azure 伺服器連結的 Azure AD 網域名稱/租用戶 ID。 例如，如果 Azure 伺服器與 Azure AD 網域 `contosotest.onmicrosoft.com` 相關聯，其中託管的使用者 `joe@contosodev.onmicrosoft.com` 是從 Azure AD 網域 `contosodev.onmicrosoft.com` 匯入，則用於驗證此使用者的必要網域名稱為 `contosotest.onmicrosoft.com`。 如果使用者是 Azure AD (與 Azure 伺服器連結) 的原生使用者，不是 MSA 帳戶，則不需要網域名稱或租用戶 ID。 若要輸入參數（從 SSMS 17.2 版開始），在 [**連接到資料庫**] 對話方塊中，完成對話方塊，並選取 [ **Active Directory-通用與 MFA**驗證]，**按一下 [** **選項**]，完成 [**使用者名稱**] 方塊，然後按一下 [線上內容] 索引標籤。請檢查 [ **AD 功能變數名稱] 或 [租使用者識別碼**] 方塊，並提供驗證授權單位，例如功能變數名稱（**contosotest.onmicrosoft.com**）或租使用者識別碼的 GUID。  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

如果您執行的是 SSMS 18. x 或更新版本，則來賓使用者不再需要 AD 功能變數名稱或租使用者識別碼，因為 18. x 或更新版本會自動辨識它。

   ![mfa-租使用者-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 企業對企業支援   
Azure AD 支援作為來賓使用者 Azure AD B2B 案例的使用者（請參閱[什麼是 AZURE B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)共同作業）只能連接到 SQL Database 和 azure Synapse，做為在目前 Azure AD 中建立之群組的成員，並使用指定資料庫中的 transact-sql `CREATE USER` 語句手動對應。 例如，如果 `steve@gmail.com` 受邀加入 Azure AD `contosotest` (與 Azure Ad 網域`contosotest.onmicrosoft.com`)，則必須在 Azure AD 中建立包含 `usergroup` 成員的 Azure AD 群組 (例如 `steve@gmail.com`)。 接著，您必須藉由執行 Transact-sql `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 語句 Azure AD SQL 管理員或 Azure AD DBO，為特定資料庫（也就是 MyDatabase）建立這個群組。 建立資料庫使用者後，使用者 `steve@gmail.com` 就可以使用 SSMS 驗證選項 `MyDatabase` 來登入 `Active Directory – Universal with MFA support`。 根據預設，使用者群組只有 connect 權限，而任何進一步的資料存取權則需以一般方式進行授與。 請注意，身為來賓使用者的使用者 `steve@gmail.com` 必須核取此方塊，並且在 SSMS [連線屬性]`contosotest.onmicrosoft.com`**對話方塊中新增 AD 網域名稱**。 [AD 網域名稱或租用戶 ID] 選項僅對 [通用驗證搭配 MFA 連線] 選項提供支援，否則會呈現灰色。

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>SQL Database 和 Azure Synapse 的通用驗證限制
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
- [使用 SQL Database 或 Azure Synapse 來設定及管理 Azure Active Directory 驗證](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [將 BACPAC 檔案匯入到新的 Azure SQL Database](../sql-database/sql-database-import.md)  
- [將 Azure SQL 資料庫匯出至 BACPAC 檔案](../sql-database/sql-database-export.md)  
- C# 介面 [IUniversalAuthProvider 介面](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- 使用 [Active Directory- 通用 MFA] 驗證時，自 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 開始可以使用 ADAL 追蹤功能。 預設為關閉，您可以使用 [Azure 服務]、[Azure 雲端]、[ADAL 輸出視窗的追蹤層級] 下的 [工具]、[選項] 功能表，然後啟用 [檢視] 功能表中的 [輸出]，以開啟 ADAL 追蹤功能。 選取 [Azure Active Directory 選項] 時，可以在輸出視窗中取得追蹤結果。  
