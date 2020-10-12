---
title: 管理 Azure Analysis Services 中的伺服器管理員 | Microsoft Docs
description: 本文說明如何使用 Azure 入口網站、PowerShell 或 REST Api 來管理 Azure Analysis Services server 的伺服器管理員。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bc17d27837d5b96f06b5172fb019db873418db94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922926"
---
# <a name="manage-server-administrators"></a>管理伺服器管理員

伺服器系統管理員必須是伺服器所在租使用者的 Azure Active Directory (Azure AD) 中有效的使用者、服務主體或安全性群組。 在 Azure 入口網站、SSMS 的 [伺服器屬性]、PowerShell 或 REST API 中，您可以使用伺服器的 **Analysis Services 管理員**來管理伺服器管理員。 

新增 **安全性群組**時，請使用 `obj:groupid@tenantid` 。 新增至伺服器管理員角色的安全性群組不支援服務主體。

## <a name="to-add-server-administrators-by-using-azure-portal"></a>使用 Azure 入口網站來新增伺服器管理員

1. 在入口網站中，針對您的伺服器按一下 [Analysis Services 管理員]****。
2. ** \<servername> Analysis Services 管理員**] 中，按一下 [**新增**]。
3. 在 [新增伺服器管理員]**** 中，選取 Azure AD 中的使用者帳戶，或透過電子郵件地址邀請外部使用者。

    ![Azure 入口網站的伺服器管理員](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>使用 SSMS 來新增伺服器管理員

1. 以滑鼠右鍵按一下伺服器 > [屬性]****。
2. 在 [Analysis Server 屬性]**** 中，按一下 [安全性]****。
3. 按一下 [新增]****，然後在 Azure AD 中輸入使用者或群組的電子郵件地址。
   
    ![在 SSMS 中新增伺服器管理員](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

建立新的伺服器時，請使用 [AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) Cmdlet 來指定 Administrator 參數。 <br>
使用 [AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) 指令程式來修改現有伺服器的系統管理員參數。

## <a name="rest-api"></a>REST API

使用 [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) 可在建立新的伺服器時，指定 asAdministrator 屬性。 <br>
使用 [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) 可在修改現有伺服器時，指定 asAdministrator 屬性。 <br>



## <a name="next-steps"></a>後續步驟 

[驗證和使用者權限](analysis-services-manage-users.md)  
[管理資料庫角色和使用者](analysis-services-database-users.md)  
[Azure 角色型存取控制 (Azure RBAC)](../role-based-access-control/overview.md)  
