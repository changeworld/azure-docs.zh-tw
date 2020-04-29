---
title: 條件式存取
description: 瞭解如何設定 Azure SQL Database 和 Azure Synapse 的條件式存取。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124899"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>具有 Azure SQL Database 和 Azure Synapse 分析的條件式存取（MFA）

Azure [SQL Database](sql-database-technical-overview.md)、[受控執行個體](sql-database-managed-instance.md)和[azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)支援 Microsoft 條件式存取。 

> [!NOTE]
> 本主題適用于 Azure SQL server，以及在 Azure SQL server 上建立 SQL Database 和 Azure Synapse。 為了簡單起見，在同時參考 SQL Database 和 Azure Synapse 時，會使用 SQL Database。

下列步驟示範如何將 SQL Database 設定為強制執行條件式存取原則。  

## <a name="prerequisites"></a>先決條件  
- 您必須在 Azure Synapse 中設定您的 SQL Database 或 SQL 集區，以支援 Azure Active Directory 驗證。 如需特定步驟，請參閱[使用 SQL Database 或 Azure Synapse 來設定和管理 Azure Active Directory 驗證](sql-database-aad-authentication-configure.md)。  
- 啟用多重要素驗證時，您必須在支援的工具進行連線，例如最新的 SSMS。 如需詳細資訊，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。  

## <a name="configure-ca-for-azure-sql-dbdw"></a>針對 Azure SQL DB/DW 設定 CA  
1. 登入入口網站，選取 [ **Azure Active Directory**]，然後選取 [**條件式存取**]。 如需詳細資訊，請參閱 [Azure Active Directory 條件式存取的技術參考](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)。  
   ![條件式存取分頁](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. 在 [條件式存取原則]**** 刀鋒視窗中，按一下 [新增原則]****、提供名稱，然後按一下 [設定規則]****。  
3. 在 [**指派**] 底下，選取 [**使用者和群組**]，勾選 [**選取使用者和群組**]，然後選取要進行條件式存取的使用者或群組。 按一下 [選取]****，然後按一下 [完成]**** 可接受您的選擇。  
   ![選取 [使用者和群組]](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. 選取 [雲端應用程式]****，按一下 [選取應用程式]****。 您會看到所有適用于條件式存取的應用程式。 選取底部的 [Azure SQL Database]**** 按一下 [選取]**** 然後按一下 [完成]****。  
   ![選取 SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   如果您找不到下列第三個螢幕擷取畫面中列出的**Azure SQL Database** ，請完成下列步驟：   
   - 使用 SSMS 搭配 AAD 管理帳戶來登入您的 Azure SQL DB/DW 執行個體。  
   - 執行 `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`。  
   - 登入 AAD，並確認 Azure SQL Database 和 Azure Synapse 列于 AAD 中的應用程式。  

5. 依序選取 [存取控制]****、[授與]****，然後核取您想要套用的原則。 例如，我們選取 [需要多重要素驗證]****。  
   ![選取授與存取權](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>[摘要]  
允許使用 Azure AD Premium 連線到 Azure SQL DB/DW 的選取應用程式 (Azure SQL Database)，現在會強制執行選取的條件式存取原則，**必要的多重要素驗證。**  
如有關于多因素驗證的 Azure SQL Database 和 Azure Synapse 的問題， MFAforSQLDB@microsoft.com請聯絡。  

## <a name="next-steps"></a>後續步驟  

如需教學課程，請參閱[保護 Azure SQL Database](sql-database-security-tutorial.md)。
