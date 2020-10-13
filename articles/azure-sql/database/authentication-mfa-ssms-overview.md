---
title: 使用多重要素 Azure Active Directory 驗證
description: Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 支援使用 (通用驗證，從 SQL Server Management Studio) SSMS Active Directory 的連接。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 2a3e1e7279e915c0c5992190ef0c8d1d83880dbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91596116"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>使用多重要素 Azure Active Directory 驗證
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 支援使用*具有 MFA 驗證的 (通用*，從[SQL Server Management Studio) SSMS Azure Active Directory](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)連接。 本文討論各種驗證選項之間的差異，以及在 Azure Active Directory (Azure AD) 的 Azure SQL 中使用通用驗證相關聯的限制。

**下載最新的 SSMS** - 在用戶端電腦上，從[下載 SQL Server Management Studio (SSMS)](https://aka.ms/ssms) 下載最新版的 SSMS。 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


對於本文討論的所有功能，請至少使用 2017 年 7 月的 17.2 版。 最近的連線對話方塊看起來應該會類似下圖：

  ![SQL Server Management Studio 中 [連接到伺服器] 對話方塊的螢幕擷取畫面，其中顯示伺服器類型、伺服器名稱和驗證的設定。](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>驗證選項

Azure AD 有兩種非互動式驗證模型，可用於許多不同的應用程式 (ADO.NET、JDCB、ODC 等) 。 這兩種方法絕對不會產生快顯對話方塊：

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

也支援 Azure Multi-Factor Authentication (MFA) 的互動式方法如下： 

- `Azure Active Directory - Universal with MFA`

Azure MFA 有助於保護資料和應用程式的存取，同時又滿足使用者對簡單登入程序的需求。 它利用各種簡單的驗證選項來提供強大的驗證 (包括電話、簡訊、含有 Pin 的智慧卡或行動應用程式通知)，讓使用者能夠選擇自己喜歡的方式。 搭配 Azure AD 使用互動式 MFA 時，會出現快顯對話方塊以進行驗證。

如需 Azure Multi-Factor Authentication 的說明，請參閱 [Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md)。
如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](authentication-mfa-ssms-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 網域名稱或租用戶 ID 參數

從 [SSMS 17 版](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)開始，從其他 Azure Active directory 匯入到目前 Azure AD 的使用者，可以在連線時提供 Azure AD 的功能變數名稱或租使用者識別碼。 來賓使用者包括從其他 Azure AD、Microsoft 帳戶 (例如 outlook.com、hotmail.com、live.com) 或其他帳戶 (例如 gmail.com) 邀請的使用者。 此資訊可讓 `Azure Active Directory - Universal with MFA` 驗證識別正確的驗證授權單位。 支援 Microsoft 帳戶 (MSA) (如 outlook.com、hotmail.com、live.com) 或非 MSA 帳戶時也需要此選項。 

所有想要使用通用驗證進行驗證的來賓使用者，都必須輸入其 Azure AD 功能變數名稱或租使用者識別碼。 此參數代表與 Azure SQL 邏輯伺服器相關聯的目前 Azure AD 功能變數名稱或租使用者識別碼。 例如，如果 SQL 邏輯伺服器與 Azure AD 網域相關聯 `contosotest.onmicrosoft.com` ，其中使用者 `joe@contosodev.onmicrosoft.com` 會以 Azure AD 網域的匯入使用者的身分來裝載，則 `contosodev.onmicrosoft.com` 驗證此使用者所需的功能變數名稱為 `contosotest.onmicrosoft.com` 。 當使用者是與 SQL 邏輯伺服器相關聯之 Azure AD 的原生使用者，且不是 MSA 帳戶時，不需要任何功能變數名稱或租使用者識別碼。 若要輸入參數 (從 SSMS 17.2 版開始) ：


1. 在 SSMS 中開啟連接。 輸入您的伺服器名稱，然後選取 [ **具有 MFA 驗證的 Azure Active Directory-通用** ]。 新增您想要用來登入的 **使用者名稱** 。
1. 選取 [ **選項** ] 方塊，然後移至 [ **連接屬性** ] 索引標籤。在 [ **連接到資料庫** ] 對話方塊中，完成資料庫的對話方塊。 核取 [AD 網域名稱或租用戶 ID]  方塊並提供驗證授權單位，如網域名稱 (**contosotest.onmicrosoft.com**) 或租用戶 ID 的 GUID。 

   ![[連線屬性] 索引標籤的螢幕擷取畫面，其中反白顯示 [連接到資料庫] 和 [AD 功能變數名稱] 或 [租使用者識別碼]](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

如果您執行 SSMS 6.x 或更新版本，來賓使用者將不再需要 AD 功能變數名稱或租使用者識別碼，因為 18. x 或更新版本會自動辨識該名稱。

   ![S s M S 中 [連接到伺服器] 對話方塊中 [連接屬性] 索引標籤的螢幕擷取畫面。已在 [連接到資料庫] 欄位中選取 [MyDatabase]。](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 企業對企業支援

> [!IMPORTANT]
> 支援來賓使用者連接到 Azure SQL Database、SQL 受控執行個體和 Azure Synapse，而不需要是群組的一部分，目前為 **公開預覽**狀態。 如需詳細資訊，請參閱 [建立 Azure AD 來賓使用者，並設定為 Azure AD 系統管理員](authentication-aad-guest-users.md)。

以來賓使用者的 Azure AD B2B 案例 Azure AD 使用者 (查看 [什麼是 AZURE b2b](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 共同作業) 只能在相關聯的 SQL Database 中建立的群組成員中連線到 Azure AD 和 Azure Synapse，並使用指定資料庫中的 [CREATE USER (transact-sql) ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 語句手動對應。 例如，如果 `steve@gmail.com` 受邀 Azure AD `contosotest` (Azure AD 網域 `contosotest.onmicrosoft.com`) ，則 `usergroup` 必須在包含該成員的 Azure AD 中建立 Azure AD 群組（例如） `steve@gmail.com` 。 然後，您必須為特定的資料庫建立此群組 (例如，藉由 `MyDatabase` AZURE AD SQL 系統管理員或 AZURE AD DBO，藉由執行 transact-sql `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 語句) 。 

建立資料庫使用者之後，使用者就 `steve@gmail.com` 可以 `MyDatabase` 使用 SSMS 驗證選項來登入 `Azure Active Directory – Universal with MFA` 。 依預設， `usergroup` 只有 connect 許可權。 任何進一步的資料存取都必須由具有足夠許可權的使用者在資料庫中 [授](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 與。 

> [!NOTE]
> 針對 SSMS 17. x， `steve@gmail.com` 您必須在 [連線屬性] 對話方塊中，檢查 [ **ad 功能變數名稱] 或 [租使用者識別碼**] 方塊，然後新增 ad 功能變數名稱，才能使用做為來賓使用者 `contosotest.onmicrosoft.com` 。 **Connection Property** 只有**具有 MFA 驗證的 Azure Active Directory 通用**，才支援**AD 功能變數名稱或租使用者識別碼**選項。 否則，核取方塊會呈現灰色。

## <a name="universal-authentication-limitations"></a>通用驗證限制

- SSMS 和 SqlPackage.exe 是目前唯一透過 Active Directory 通用驗證，針對 MFA 啟用的工具。
- SSMS 17.2 版支援使用具有 MFA 的通用驗證來進行多使用者平行存取。 針對 SSMS 17.0 和17.1 版，此工具會將使用通用驗證的 SSMS 實例的登入限制為單一 Azure Active Directory 帳戶。 若要以另一個 Azure AD 帳戶登入，您必須使用另一個 SSMS 實例。 這項限制僅限於 Active Directory 通用驗證;您可以使用 `Azure Active Directory - Password` 驗證、驗證或來登入不同的伺服器 `Azure Active Directory - Integrated` `SQL Server Authentication` 。
- SSMS 支援 Active Directory 通用驗證，可使用物件總管、查詢編輯器及查詢存放區視覺效果。
- SSMS 17.2 版針對匯出/擷取/部署資料資料庫提供 DacFx 精靈支援。 特定使用者使用通用驗證透過初始驗證對話方塊進行驗證後，DacFx 精靈的運作方式與所有其他驗證方法相同。
- SSMS 資料表設計工具不支援通用驗證。
- 除了您必須使用支援的 SSMS 版本之外，Active Directory 通用驗證並沒有其他軟體需求。  
- 請參閱下列連結，以取得適用于通用驗證 (ADAL) 版本的最新 Active Directory 驗證程式庫： [microsoft.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。  

## <a name="next-steps"></a>後續步驟

- 如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](authentication-mfa-ssms-configure.md)。
- 授與對資料庫的其他存取權：[SQL Database 驗證和授權：授與存取權](logins-create-manage.md)  
- 請確定其他人可以透過防火牆連線： [使用 Azure 入口網站設定伺服器層級防火牆規則](firewall-configure.md)  
- [使用 SQL Database 或 Azure Synapse 來設定及管理 Azure Active Directory authentication](authentication-aad-configure.md)
- [建立 Azure AD 來賓使用者，並將其設定為 Azure AD 管理員](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [將 BACPAC 檔案匯入至新的資料庫](database-import.md)  
- [從 BACPAC 檔案匯出資料庫](database-export.md)  
- C# 介面 [IUniversalAuthProvider 介面](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- 搭配使用 **Azure Active Directory-通用與 MFA** 驗證時，從 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)開始會提供 ADAL 追蹤。 預設為關閉，您可以使用 [Azure 服務]****、[Azure 雲端]****、[ADAL 輸出視窗的追蹤層級]**** 下的 [工具]****、[選項]**** 功能表，然後啟用 [檢視]**** 功能表中的 [輸出]****，以開啟 ADAL 追蹤功能。 選取 [Azure Active Directory 選項]**** 時，可以在輸出視窗中取得追蹤結果。  
