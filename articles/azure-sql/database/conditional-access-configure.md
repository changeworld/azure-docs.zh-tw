---
title: 條件式存取
description: 瞭解如何設定 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 的條件式存取。
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: ad80f68c1ab3b3583c5a22de49b77211571f345e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443992"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>使用 Azure SQL Database 和 Azure Synapse Analytics 的條件式存取

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md)、 [Azure SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 支援 Microsoft 條件式存取。

下列步驟說明如何設定 Azure SQL Database、SQL 受控執行個體或 Azure Synapse，以強制執行 (CA) 原則的條件式存取。  

## <a name="prerequisites"></a>必要條件

- 您必須在 Azure Synapse 中設定 Azure SQL Database、Azure SQL 受控執行個體或 Azure SQL 集區，以支援 Azure Active Directory (Azure AD) 驗證。 如需特定步驟，請參閱 [使用 SQL Database 或 Azure Synapse 來設定及管理 Azure Active Directory 驗證](authentication-aad-configure.md)。  
- 當 Multi-Factor Authentication 啟用時，您必須使用支援的工具（例如最新的 SQL Server Management Studio (SSMS) ）來連接。 如需詳細資訊，請參閱[設定適用於 SQL Server Management Studio 的 Azure SQL Database 多重要素驗證](authentication-mfa-ssms-configure.md)。  

## <a name="configure-conditional-access"></a>設定條件式存取

> [!NOTE]
> 下列範例會使用 Azure SQL Database，但您應該選取要設定條件式存取的適當產品。

1. 登入 Azure 入口網站，選取 [ **Azure Active Directory**]，然後選取 [ **條件式存取**]。 如需詳細資訊，請參閱 [Azure Active Directory 條件式存取的技術參考](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)。  
   ![條件式存取分頁](./media/conditional-access-configure/conditional-access-blade.png)

2. 在 [條件式存取原則]**** 刀鋒視窗中，按一下 [新增原則]****、提供名稱，然後按一下 [設定規則]****。  
3. 在 [ **指派**] 底下，選取 [ **使用者和群組**]，核取 [ **選取使用者和群組**]，然後選取使用者或群組以進行條件式存取。 按一下 [選取]****，然後按一下 [完成]**** 可接受您的選擇。  
   ![選取 [使用者和群組]](./media/conditional-access-configure/select-users-and-groups.png)  

4. 選取 [雲端應用程式]****，按一下 [選取應用程式]****。 您會看到所有適用于條件式存取的應用程式。 選取底部的 [Azure SQL Database]**** 按一下 [選取]**** 然後按一下 [完成]****。  
   ![選取 SQL Database](./media/conditional-access-configure/select-sql-database.png)  
   如果您找不到下列第三個螢幕擷取畫面中列出的 **Azure SQL Database** ，請完成下列步驟：
   - 使用 SSMS 搭配 Azure AD 管理帳戶，連接到 Azure SQL Database 中的資料庫。  
   - 執行 `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`。  
   - 登入 Azure AD，並確認 Azure SQL Database、SQL 受控執行個體或 Azure Synapse 會列在 Azure AD 實例的應用程式中。  

5. 依序選取 [存取控制]****、[授與]****，然後核取您想要套用的原則。 例如，我們選取 [需要多重要素驗證]****。  
   ![選取授與存取權](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>摘要

選取的應用程式 (Azure SQL Database) 使用 Azure AD Premium，現在會強制執行選取的條件式存取原則， **需要多重要素驗證。**

如有關于多重要素驗證的 Azure SQL Database 和 Azure Synapse 的問題，請聯絡 <MFAforSQLDB@microsoft.com> 。  

## <a name="next-steps"></a>後續步驟  

如需教學課程，請參閱 [SQL Database 中的保護資料庫](secure-database-tutorial.md)。
