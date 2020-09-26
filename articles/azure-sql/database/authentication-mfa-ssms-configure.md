---
title: 設定 Multi-Factor Authentication
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: 瞭解如何使用 SSMS 的多重要素驗證來進行 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 8f72b9e9dfc2aa35960f9f81219a4c8973e2fe5b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91277915"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>設定適用於 SQL Server Management Studio 和 Azure AD 的多重要素驗證
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

本文說明如何使用 Azure Active Directory (Azure AD) 使用 () SSMS SQL Server Management Studio MFA (的多重要素驗證。 Azure AD MFA 可在連接 SSMS 或 SqlPackage.exe 至 [Azure SQL Database](sql-database-paas-overview.md)、 [Azure SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) 和 [Azure Synapse Analytics (先前的 SQL 資料倉儲) ](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)時使用。 如需多重要素驗證的總覽，請參閱 [SQL Database 的通用驗證、SQL 受控執行個體和 Azure Synapse (MFA 的 SSMS 支援) ](../database/authentication-mfa-ssms-overview.md)。

> [!IMPORTANT]
> Azure SQL Database 中的資料庫、Azure SQL 受控執行個體和 Azure Synapse (之前的 SQL 資料倉儲) 在本文的其餘部分統稱為資料庫，且伺服器是指裝載 Azure SQL Database 和 Azure Synapse 資料庫的 [伺服器](logical-servers.md) 。

## <a name="configuration-steps"></a>組態步驟

1. **設定 Azure Active Directory** - 如需詳細資訊，請參閱[管理 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx)、[整合內部部署身分識別與 Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md)、[將您自己的網域名稱新增至 Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure 現在支援與 Windows Server Active Directory 同盟](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、[使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。
2. **設定 MFA** - 如需逐步指示，請參閱[什麼是 Azure Multi-Factor Authentication？](../../active-directory/authentication/multi-factor-authentication.md)、[使用 Azure SQL Database 和資料倉儲的條件式存取 (MFA)](conditional-access-configure.md)。  (完整條件式存取需要 Premium Azure Active Directory。 有限的 MFA 適用於標準 Azure AD。)
3. **設定 Azure AD 驗證** -如需逐步指示，請參閱 [使用 Azure Active Directory 驗證連接到 SQL Database、SQL 受控執行個體或 Azure Synapse](authentication-aad-overview.md)。
4. **下載 SSMS** - 在用戶端電腦上，從[下載 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下載最新的 SSMS。

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>使用通用驗證搭配 SSMS 進行連線

下列步驟說明如何使用最新的 SSMS 進行連接。

1. 若要使用通用驗證進行連線，請在 [連線 **到伺服器** ] 對話方塊的 [SQL SERVER MANAGEMENT STUDIO (SSMS) 中選取 [ **具有 MFA 支援的 Active Directory-通用**]。 (如果您看到 **Active Directory 通用驗證**，則表示您不是使用最新的 SSMS 版本。)

   ![S s M S 中 [連接到伺服器] 對話方塊中 [連接屬性] 索引標籤的螢幕擷取畫面。已在 [連接到資料庫] 下拉式清單中選取 [MyDatabase]。](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. 使用 Azure Active Directory 認證完成 [使用者名稱]**** 方塊 (採用 `user_name@domain.com` 格式)。

   ![伺服器類型、伺服器名稱、驗證和使用者名稱的 [連接到伺服器] 對話方塊設定的螢幕擷取畫面。](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. 如果您是以來賓使用者的身份連線，就不再需要為來賓使用者完成 AD 功能變數名稱或租使用者識別碼欄位，因為 SSMS 18. x 或更新版本會自動辨識它。 如需詳細資訊，請參閱 [SQL Database 的通用驗證、SQL 受控執行個體和 Azure Synapse (MFA 的 SSMS 支援) ](../database/authentication-mfa-ssms-overview.md)。

   ![S s M S 中 [連接到伺服器] 對話方塊中 [連接屬性] 索引標籤的螢幕擷取畫面。已在 [連接到資料庫] 下拉式清單中選取 [MyDatabase]。](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   但是，如果您是使用 SSMS 17. x 或更舊的來賓使用者來連線，則必須按一下 [ **選項**]，然後在 [連線 **屬性** ] 對話方塊中，完成 [ **AD 功能變數名稱] 或 [租使用者識別碼** ] 方塊。

   ![[連接到伺服器] 對話方塊中 S s M S 的 [連線屬性] 索引標籤螢幕擷取畫面。已填入 [AD 功能變數名稱] 或 [租使用者識別碼] 屬性選項。](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. 選取 [ **選項** ]，並在 [ **選項** ] 對話方塊上指定資料庫。  (如果已連線的使用者是 (亦即) 的來賓使用者 joe@outlook.com ，您必須核取此方塊，並新增目前的 AD 功能變數名稱或租使用者識別碼作為選項的一部分。 請參閱 [SQL Database 的通用驗證，並 Azure Synapse Analytics (MFA 的 SSMS 支援) ](../database/authentication-mfa-ssms-overview.md)。 然後按一下 [ **連接**]。  
5. 當 [登入您的帳戶] **** 對話方塊顯示時，請提供您 Azure Active Directory 身分識別的帳戶和密碼。 如果使用者不屬於與 Azure AD 同盟的網域，則不需要密碼。

   ![Azure SQL Database 和資料倉儲的 [登入帳戶] 對話方塊的螢幕擷取畫面。 系統會填入帳戶和密碼。](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > 如果是使用不需要 MFA 的帳戶進行通用驗證，則您可以在此時連線。 對於需要 MFA 的使用者，請繼續執行下列步驟：
   >  

6. 可能會顯示兩個 MFA 設定對話方塊。 這個一次性作業是根據 MFA 系統管理員設定而定，因此可能是選擇性的。 對於已啟用 MFA 的網域，有時會預先定義這個步驟 (例如，網域會要求使用者使用智慧卡和 pin)。

   ![[登入您的帳戶] 對話方塊的螢幕擷取畫面，其中包含設定額外安全性驗證的提示 Azure SQL Database 和資料倉儲。](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. 第二個可能的一次性對話方塊可讓您選取驗證方法的詳細資料。 可能的選項是由您的系統管理員所設定。

   ![[其他安全性驗證] 對話方塊的螢幕擷取畫面，其中包含選取和設定驗證方法的選項。](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory 會傳送確認資訊給您。 當您收到驗證碼時，請將其輸入 [輸入驗證碼]**** 方塊，然後按一下 [登入]****。

   ![[登入您的帳戶] 對話方塊的螢幕擷取畫面，其中包含可提示您輸入驗證碼的 Azure SQL Database 和資料倉儲。](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

驗證完成時，SSMS 即會正常連線 (假設認證和防火牆存取有效)。

## <a name="next-steps"></a>後續步驟

- 如需多重要素驗證的總覽，請參閱 [SQL Database 的通用驗證、SQL 受控執行個體和 Azure Synapse (MFA 的 SSMS 支援) ](../database/authentication-mfa-ssms-overview.md)。  
- 授與對資料庫的其他存取權：[SQL Database 驗證和授權：授與存取權](logins-create-manage.md)  
- 請確定其他人可以透過防火牆連線： [使用 Azure 入口網站設定伺服器層級防火牆規則](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure)  
- 使用 [Active Directory- 通用 MFA]**** 驗證時，自 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 開始可以使用 ADAL 追蹤功能。 預設為關閉，您可以使用 [Azure 服務]****、[Azure 雲端]****、[ADAL 輸出視窗的追蹤層級]**** 下的 [工具]****、[選項]**** 功能表，然後啟用 [檢視]**** 功能表中的 [輸出]****，以開啟 ADAL 追蹤功能。 選取 [Azure Active Directory 選項]**** 時，可以在輸出視窗中取得追蹤結果。
