---
title: 條件式存取
description: 瞭解如何為 Azure SQL 資料庫和 Azure 突觸配置條件訪問。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124899"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>具有 Azure SQL 資料庫和 Azure 突觸分析的條件訪問 （MFA）

Azure [SQL 資料庫](sql-database-technical-overview.md)、[託管實例](sql-database-managed-instance.md)和[Azure 突觸](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)支援 Microsoft 條件訪問。 

> [!NOTE]
> 本主題適用于 Azure SQL 伺服器，以及在 Azure SQL 伺服器上創建的 SQL 資料庫和 Azure 突觸。 為簡單起見，SQL 資料庫在引用 SQL 資料庫和 Azure 突觸時使用。

下列步驟示範如何將 SQL Database 設定為強制執行條件式存取原則。  

## <a name="prerequisites"></a>Prerequisites  
- 必須在 Azure Synaps 中配置 SQL 資料庫或 SQL 池以支援 Azure 活動目錄身份驗證。 有關特定步驟，請參閱[使用 SQL 資料庫或 Azure 突觸配置和管理 Azure 活動目錄身份驗證](sql-database-aad-authentication-configure.md)。  
- 啟用多重要素驗證時，您必須在支援的工具進行連線，例如最新的 SSMS。 如需詳細資訊，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。  

## <a name="configure-ca-for-azure-sql-dbdw"></a>針對 Azure SQL DB/DW 設定 CA  
1. 登錄到門戶，選擇 Azure**活動目錄**，然後選擇**條件訪問**。 如需詳細資訊，請參閱 [Azure Active Directory 條件式存取的技術參考](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)。  
   ![條件訪問邊欄選項卡](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. 在 [條件式存取原則]**** 刀鋒視窗中，按一下 [新增原則]****、提供名稱，然後按一下 [設定規則]****。  
3. 在 **"分配"** 下，選擇 **"使用者"和"組**"，選中 **"選擇使用者和組**"，然後為條件訪問選擇使用者或組。 按一下 [選取]****，然後按一下 [完成]**** 可接受您的選擇。  
   ![選取 [使用者和群組]](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. 選取 [雲端應用程式]****，按一下 [選取應用程式]****。 您將看到所有可用於條件訪問的應用。 選取底部的 [Azure SQL Database]**** 按一下 [選取]**** 然後按一下 [完成]****。  
   ![選取 SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   如果找不到以下第三個螢幕截圖中列出的**Azure SQL 資料庫**，則完成以下步驟：   
   - 使用 SSMS 搭配 AAD 管理帳戶來登入您的 Azure SQL DB/DW 執行個體。  
   - 執行 `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`。  
   - 登錄到 AAD 並驗證 Azure SQL 資料庫和 Azure 突觸是否列在 AAD 中的應用程式中。  

5. 依序選取 [存取控制]****、[授與]****，然後核取您想要套用的原則。 例如，我們選取 [需要多重要素驗證]****。  
   ![選取授與存取權](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>總結  
允許使用 Azure AD Premium 連線到 Azure SQL DB/DW 的選取應用程式 (Azure SQL Database)，現在會強制執行選取的條件式存取原則，**必要的多重要素驗證。**  
有關 Azure SQL 資料庫和 Azure 突觸有關多重要素驗證的問題，請MFAforSQLDB@microsoft.com與 .  

## <a name="next-steps"></a>後續步驟  

如需教學課程，請參閱[保護 Azure SQL Database](sql-database-security-tutorial.md)。
