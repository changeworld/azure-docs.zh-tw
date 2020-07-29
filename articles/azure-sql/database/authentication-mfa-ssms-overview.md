---
title: 使用多重要素 Azure Active Directory 驗證
description: Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse 分析支援使用 Active Directory 通用驗證從 SQL Server Management Studio （SSMS）進行連線。
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
ms.date: 07/27/2020
tags: azure-synapse
ms.openlocfilehash: b2d179121b05d7bf3493937a9ff72e302fd31f3d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281135"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>使用多重要素 Azure Active Directory 驗證
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse 分析支援使用*Azure Active Directory-通用與 MFA*驗證，從[SQL Server Management Studio （SSMS）](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)進行連線。 本文討論各種驗證選項之間的差異，以及在 Azure SQL 的 Azure Active Directory （Azure AD）中使用通用驗證的相關限制。

**下載最新的 SSMS** - 在用戶端電腦上，從[下載 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下載最新版的 SSMS。

對於本文討論的所有功能，請至少使用 2017 年 7 月的 17.2 版。 最近的連線對話方塊看起來應該會類似下圖：

  ![1mfa-universal-connect](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "完成 [使用者名稱] 方塊。")  

## <a name="authentication-options"></a>驗證選項

Azure AD 有兩種非互動式驗證模型，可用於許多不同的應用程式（ADO.NET、JDCB、.ODC 等等）。 這兩種方法絕對不會產生快顯對話方塊：

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

也支援 Azure 多重要素驗證（MFA）的互動式方法為： 

- `Azure Active Directory - Universal with MFA`

Azure MFA 有助於保護資料和應用程式的存取，同時又滿足使用者對簡單登入程序的需求。 它利用各種簡單的驗證選項來提供強大的驗證 (包括電話、簡訊、含有 Pin 的智慧卡或行動應用程式通知)，讓使用者能夠選擇自己喜歡的方式。 搭配 Azure AD 使用互動式 MFA 時，會出現快顯對話方塊以進行驗證。

如需 Azure 多重要素驗證的說明，請參閱[多重要素驗證](../../active-directory/authentication/multi-factor-authentication.md)。
如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](authentication-mfa-ssms-configure.md)。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 網域名稱或租用戶 ID 參數

從[SSMS 第17版](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)開始，從其他 Azure Active directory 匯入到目前 Azure AD 做為來賓使用者的使用者，可以在連線時提供 Azure AD 功能變數名稱或租使用者識別碼。 來賓使用者包括從其他 Azure AD、Microsoft 帳戶 (例如 outlook.com、hotmail.com、live.com) 或其他帳戶 (例如 gmail.com) 邀請的使用者。 此資訊可讓 `Azure Active Directory - Universal with MFA` 驗證識別正確的驗證授權單位。 支援 Microsoft 帳戶 (MSA) (如 outlook.com、hotmail.com、live.com) 或非 MSA 帳戶時也需要此選項。 

所有想要使用通用驗證進行驗證的來賓使用者，都必須輸入其 Azure AD 功能變數名稱或租使用者識別碼。 此參數代表與 Azure SQL 邏輯伺服器相關聯的目前 Azure AD 功能變數名稱或租使用者識別碼。 例如，如果 SQL 邏輯伺服器與 Azure AD 網域相關聯 `contosotest.onmicrosoft.com` ，其中使用者是以匯 `joe@contosodev.onmicrosoft.com` 入的使用者身分裝載于 Azure AD 網域 `contosodev.onmicrosoft.com` 中，則驗證此使用者所需的功能變數名稱為 `contosotest.onmicrosoft.com` 。 當使用者是與 SQL 邏輯伺服器相關聯 Azure AD 的原生使用者，而且不是 MSA 帳戶時，就不需要功能變數名稱或租使用者識別碼。 若要輸入參數（從 SSMS 17.2 版開始）：


1. 在 SSMS 中開啟連接。 輸入您的伺服器名稱，然後選取 [ **Azure Active Directory-通用**] 和 [MFA 驗證]。 新增您想要用來登入的**使用者名稱**。
1. 選取 [**選項**] 方塊，然後移至 [**連接屬性**] 索引標籤。在 [**連接到資料庫**] 對話方塊中，完成資料庫的對話方塊。 核取 [AD 網域名稱或租用戶 ID]  方塊並提供驗證授權單位，如網域名稱 (**contosotest.onmicrosoft.com**) 或租用戶 ID 的 GUID。 

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

如果您執行的是 SSMS 18. x 或更新版本，來賓使用者不再需要 AD 功能變數名稱或租使用者識別碼，因為 18. x 或更新版本會自動辨識它。

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 企業對企業支援

> [!IMPORTANT]
> 支援來賓使用者連線至 Azure SQL Database、SQL 受控執行個體和 Azure Synapse，而不需要是群組的一部分，目前處於**公開預覽**狀態。 如需詳細資訊，請參閱[建立 Azure AD 來賓使用者並設定為 Azure AD 系統管理員](authentication-aad-guest-users.md)。

Azure AD 支援作為來賓使用者 Azure AD B2B 案例的使用者（請參閱[什麼是 AZURE B2B](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)共同作業）只能連接到 SQL Database 和 azure Synapse，做為在相關聯 Azure AD 中建立之群組的成員，並使用指定資料庫中的[CREATE USER （transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)語句來手動對應。 例如，如果 `steve@gmail.com` 受邀 Azure AD `contosotest` （使用 Azure AD 網域 `contosotest.onmicrosoft.com` ），就 `usergroup` 必須在包含成員的 Azure AD 中建立 Azure AD 群組，例如 `steve@gmail.com` 。 然後，您必須藉由 `MyDatabase` 執行 transact-sql 語句，以 AZURE AD SQL 管理員或 AZURE AD DBO）為特定資料庫建立此群組 `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 。 

建立資料庫使用者之後，使用者就 `steve@gmail.com` 可以 `MyDatabase` 使用 SSMS 驗證選項來登入 `Azure Active Directory – Universal with MFA` 。 根據預設， `usergroup` 只有 connect 許可權。 任何進一步的資料存取都必須由擁有足夠許可權的使用者[授](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql)與資料庫。 

> [!NOTE]
> 針對 SSMS 17. x， `steve@gmail.com` 您必須檢查 [ **AD 功能變數名稱或租使用者識別碼**] 方塊，並 `contosotest.onmicrosoft.com` 在 [**連接屬性**] 對話方塊中新增 ad 功能變數名稱。 [ **AD 功能變數名稱或租使用者識別碼**] 選項僅支援使用 MFA 驗證的 [ **Azure Active Directory-通用**]。 否則，核取方塊會呈現灰色。

## <a name="universal-authentication-limitations"></a>通用驗證限制

- SSMS 和 SqlPackage.exe 是目前唯一透過 Active Directory 通用驗證，針對 MFA 啟用的工具。
- SSMS 版本17.2 支援使用具有 MFA 的通用驗證的多使用者平行存取。 針對 SSMS 版本17.0 和17.1，此工具會使用通用驗證將 SSMS 實例的登入限制為單一 Azure Active Directory 帳戶。 若要以另一個 Azure AD 帳戶登入，您必須使用另一個 SSMS 實例。 此限制僅限於 Active Directory 通用驗證;您可以使用 `Azure Active Directory - Password` 驗證、驗證或登入不同的伺服器 `Azure Active Directory - Integrated` `SQL Server Authentication` 。
- SSMS 支援 Active Directory 通用驗證，可使用物件總管、查詢編輯器及查詢存放區視覺效果。
- SSMS 17.2 版針對匯出/擷取/部署資料資料庫提供 DacFx 精靈支援。 特定使用者使用通用驗證透過初始驗證對話方塊進行驗證後，DacFx 精靈的運作方式與所有其他驗證方法相同。
- SSMS 資料表設計工具不支援通用驗證。
- 除了您必須使用支援的 SSMS 版本之外，Active Directory 通用驗證並沒有其他軟體需求。  
- 如需通用驗證的最新 Active Directory 驗證程式庫（ADAL）版本，請參閱下列連結： [microsoft.identitymodel. 用戶端. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。  

## <a name="next-steps"></a>後續步驟

- 如需了解組態步驟，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](authentication-mfa-ssms-configure.md)。
- 授與對資料庫的其他存取權：[SQL Database 驗證和授權：授與存取權](logins-create-manage.md)  
- 請確定其他人可透過防火牆連線：[使用 Azure 入口網站設定伺服器層級防火牆規則](firewall-configure.md)  
- [使用 SQL Database 或 Azure Synapse 來設定及管理 Azure Active Directory 驗證](authentication-aad-configure.md)
- [建立 Azure AD 來賓使用者，並將其設定為 Azure AD 管理員](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [將 BACPAC 檔案匯入至新的資料庫](database-import.md)  
- [從 BACPAC 檔案匯出資料庫](database-export.md)  
- C# 介面 [IUniversalAuthProvider 介面](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- 使用**Azure Active Directory-通用搭配 MFA**驗證時，從[SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)開始就可以使用 ADAL 追蹤。 預設為關閉，您可以使用 [Azure 服務]****、[Azure 雲端]****、[ADAL 輸出視窗的追蹤層級]**** 下的 [工具]****、[選項]**** 功能表，然後啟用 [檢視]**** 功能表中的 [輸出]****，以開啟 ADAL 追蹤功能。 選取 [Azure Active Directory 選項]**** 時，可以在輸出視窗中取得追蹤結果。  
