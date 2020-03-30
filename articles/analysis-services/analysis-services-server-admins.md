---
title: 管理 Azure Analysis Services 中的伺服器管理員 | Microsoft Docs
description: 本文介紹如何使用 Azure 門戶、PowerShell 或 REST API 管理 Azure 分析服務伺服器的伺服器管理員。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572616"
---
# <a name="manage-server-administrators"></a>管理伺服器管理員

對於伺服器所在的租用戶，伺服器管理員必須是 Azure Active Directory (Azure AD) 中有效的使用者或安全性群組。 在 Azure 入口網站、SSMS 的 [伺服器屬性]、PowerShell 或 REST API 中，您可以使用伺服器的 **Analysis Services 管理員**來管理伺服器管理員。 

**安全性群組**必須[啟用郵件](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups)，`MailEnabled`屬性設置為`True`。 使用 通過電子郵件地址指定組時`obj:groupid@tenantid`。

## <a name="to-add-server-administrators-by-using-azure-portal"></a>使用 Azure 入口網站來新增伺服器管理員

1. 在入口網站中，針對您的伺服器按一下 [Analysis Services 管理員]****。
2. 在**\<伺服器名稱> - 分析服務管理員**中，按一下"**添加**"。
3. 在 [新增伺服器管理員]**** 中，選取 Azure AD 中的使用者帳戶，或透過電子郵件地址邀請外部使用者。

    ![Azure 入口網站的伺服器管理員](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>使用 SSMS 來新增伺服器管理員

1. 以滑鼠右鍵按一下伺服器 > [屬性]****。
2. 在 [Analysis Server 屬性]**** 中，按一下 [安全性]****。
3. 按一下 [新增]****，然後在 Azure AD 中輸入使用者或群組的電子郵件地址。
   
    ![在 SSMS 中新增伺服器管理員](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用[New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) Cmdlet 在創建新伺服器時指定管理員參數。 <br>
使用[Set-AzAnalysis 服務伺服器](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)Cmdlet 修改現有伺服器的管理員參數。

## <a name="rest-api"></a>REST API

使用 [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) 可在建立新的伺服器時，指定 asAdministrator 屬性。 <br>
使用 [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) 可在修改現有伺服器時，指定 asAdministrator 屬性。 <br>



## <a name="next-steps"></a>後續步驟 

[驗證和使用者權限](analysis-services-manage-users.md)  
[管理資料庫角色和使用者](analysis-services-database-users.md)  
[基於角色的存取控制](../role-based-access-control/overview.md)  

