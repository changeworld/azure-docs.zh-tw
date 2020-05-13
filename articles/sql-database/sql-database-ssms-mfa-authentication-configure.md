---
title: 設定 Multi-Factor Authentication
description: 瞭解如何搭配 SSMS 使用多重要素驗證來進行 SQL Database 和 Azure Synapse 分析
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: has-adal-ref
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: e551ac54414cb3114a7d0905b1b2e59b0d16580a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198555"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>設定適用於 SQL Server Management Studio 和 Azure AD 的多重要素驗證

本主題說明如何使用 Azure Active Directory 多重要素驗證 (MFA) 搭配 SQL Server Management Studio。 將 SSMS 或 SqlPackage 連接到 Azure [SQL Database](sql-database-technical-overview.md)和[azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)時，可以使用 Azure AD MFA。 如需 Azure SQL Database 多重要素驗證的總覽，請參閱[使用 SQL Database 的通用驗證和 Azure Synapse （MFA 的 SSMS 支援）](sql-database-ssms-mfa-authentication.md)。

> [!NOTE]
> 本主題適用于 Azure SQL server，以及在 Azure SQL server 上建立 SQL Database 和 Azure Synapse 資料庫。 為了簡單起見，在同時參考 SQL Database 和 Azure Synapse 時，會使用 SQL Database。

## <a name="configuration-steps"></a>設定步驟

1. **設定 Azure Active Directory** - 如需詳細資訊，請參閱[管理 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx)、[整合內部部署身分識別與 Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md)、[將您自己的網域名稱新增至 Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure 現在支援與 Windows Server Active Directory 同盟](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、[使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。
2. **設定 MFA** -如需逐步指示，請參閱[什麼是 Azure 多重要素驗證？](../active-directory/authentication/multi-factor-authentication.md)、[具有 Azure SQL Database 的條件式存取（MFA）和 Azure Synapse](sql-database-conditional-access.md)。 （完整條件式存取需要 Premium Azure Active Directory （Azure AD）。 有限的 MFA 適用於標準 Azure AD。)
3. **設定 SQL Database 或適用于 Azure AD 驗證的 Azure Synapse** -如需逐步指示，請參閱[使用 Azure Active Directory 驗證連接到 SQL Database 或 azure Synapse](sql-database-aad-authentication.md)。
4. **下載 SSMS** - 在用戶端電腦上，從[下載 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下載最新的 SSMS。 對於本主題中的所有功能，至少使用 2017 年 7 月的 17.2 版。  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>使用通用驗證搭配 SSMS 進行連線

下列步驟示範如何使用最新的 SSMS 連接到 SQL Database 或 SAzure Synapse。

1. 若要使用通用驗證進行連線，請在 [連線到伺服器]**** 對話方塊中，選取 [Active Directory - 通用驗證搭配 MFA 支援]****。 (如果您看到 **Active Directory 通用驗證**，則表示您不是使用最新的 SSMS 版本。)  
   ![1mfa-universal-connect][1]  
2. 使用 Azure Active Directory 認證完成 [使用者名稱]**** 方塊 (採用 `user_name@domain.com` 格式)。  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. 如果您以來賓使用者身分連線，您就不再需要為來賓使用者完成 [AD 功能變數名稱] 或 [租使用者識別碼] 欄位，因為 SSMS 18. x 或更新版本會自動辨識它。 如需詳細資訊，請參閱[使用 SQL Database 的通用驗證和 Azure Synapse （MFA 的 SSMS 支援）](sql-database-ssms-mfa-authentication.md)。
   ![mfa-無租使用者-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   不過，如果您使用 SSMS 17. x 或更舊版本連接為來賓使用者，您必須按一下 [**選項**]，然後在 [連線**屬性**] 對話方塊中，完成 [ **AD 功能變數名稱或租使用者識別碼**] 方塊。
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. SQL Database 和 Azure Synapse 的一般情況下，您必須**按一下 [選項]，並**在 [**選項**] 對話方塊上指定資料庫。 (如果已連線的使用者是來賓使用者 (亦即joe@outlook.com)，您必須核取此方塊，並將目前的 AD 網域名稱或租用戶 ID 新增為 [選項] 的一部分。 請參閱[使用 SQL Database 和 Azure Synapse 的通用驗證（MFA 的 SSMS 支援）](sql-database-ssms-mfa-authentication.md)。 然後按一下 [ **連接**]。  
5. 當 [登入您的帳戶] **** 對話方塊顯示時，請提供您 Azure Active Directory 身分識別的帳戶和密碼。 如果使用者不屬於與 Azure AD 同盟的網域，則不需要密碼。  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > 如果是使用不需要 MFA 的帳戶進行通用驗證，則您可以在此時連線。 對於需要 MFA 的使用者，請繼續執行下列步驟：
   >  
   
6. 可能會顯示兩個 MFA 設定對話方塊。 這個一次性作業是根據 MFA 系統管理員設定而定，因此可能是選擇性的。 對於已啟用 MFA 的網域，有時會預先定義這個步驟 (例如，網域會要求使用者使用智慧卡和 pin)。  
   ![3mfa-setup][3]  
7. 第二個可能的一次性對話方塊可讓您選取驗證方法的詳細資料。 可能的選項是由您的系統管理員所設定。  
   ![4mfa-verify-1][4]  
8. Azure Active Directory 會傳送確認資訊給您。 當您收到驗證碼時，請將其輸入 [輸入驗證碼]**** 方塊，然後按一下 [登入]****。  
   ![5mfa-verify-2][5]  

驗證完成時，SSMS 即會正常連線 (假設認證和防火牆存取有效)。

## <a name="next-steps"></a>後續步驟

- 如需 Azure SQL Database 多重要素驗證的總覽，請參閱使用 SQL Database 的通用驗證[和 Azure Synapse （MFA 的 SSMS 支援）](sql-database-ssms-mfa-authentication.md)。  
- 授與對資料庫的其他存取權：[SQL Database 驗證和授權：授與存取權](sql-database-manage-logins.md)  
- 確定其他人可透過防火牆連線：[使用 Azure 入口網站設定 Azure SQL Database 伺服器層級防火牆規則](sql-database-configure-firewall-settings.md)。  
- 使用 [Active Directory- 通用 MFA]**** 驗證時，自 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 開始可以使用 ADAL 追蹤功能。 預設為關閉，您可以使用 [Azure 服務]****、[Azure 雲端]****、[ADAL 輸出視窗的追蹤層級]**** 下的 [工具]****、[選項]**** 功能表，然後啟用 [檢視]**** 功能表中的 [輸出]****，以開啟 ADAL 追蹤功能。 選取 [Azure Active Directory 選項]**** 時，可以在輸出視窗中取得追蹤結果。   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

