---
title: 從 Azure AD 中的應用程式移除使用者或群組指派
description: 如何在 Azure Active Directory 中從企業應用程式移除使用者或群組的存取權指派
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: edf918b57212cf2adfbffb358a0257d9dbea85e9
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275871"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中從企業應用程式移除使用者或群組指派

在 Azure Active Directory （Azure AD）中，您可以輕鬆地將使用者或群組從指派給其中一個企業應用程式的存取權移除。 您需要有適當的許可權，才能管理企業應用程式。 而且，您必須是目錄的全域管理員。

> [!NOTE]
> 如果是 Microsoft 應用程式 (例如 Office 365 應用程式)，請使用 PowerShell 移除企業應用程式的使用者。

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>如何在 Azure 入口網站中移除對企業應用程式的使用者或群組指派？

1. 使用具有目錄全域管理員身分的帳戶來登入[Azure 入口網站](https://portal.azure.com)。
1. 選取 [**所有服務**]，在文字方塊中輸入**Azure Active Directory** ，然後選取**enter 鍵**。
1. 在 [ **Azure Active Directory- *directoryname* ** ] 頁面（也就是您所管理之目錄的 [Azure AD] 頁面）上，選取 [**企業應用程式**]。
1. 在 [**企業應用程式-所有應用程式**] 頁面上，您會看到一份您可以管理的應用程式清單。 選取應用程式。
1. 在 [ ***appname***總覽] 頁面（也就是標題中有所選應用程式名稱的頁面）上，選取 [**使用者 & 群組**]。
1. 在 [appname - 使用者和群組指派]****** **** 頁面上，選取一或多個使用者或群組，然後選取 [移除]**** 命令。 出現提示時，請確認您的決定。

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>如何使用 PowerShell 移除對企業應用程式的使用者或群組指派？

1. 開啟已提高權限的 Windows PowerShell 命令提示字元。

   > [!NOTE]
   > 您必須安裝 AzureAD 模組 (使用命令 `Install-Module -Name AzureAD`)。 如果系統提示您安裝 NuGet 模組或新的 Azure Active Directory V2 PowerShell 模組，請輸入 Y 然後按 ENTER。

1. 執行 `Connect-AzureAD` 並以全域管理員的使用者帳戶登入。
1. 使用下列腳本，從應用程式移除使用者和角色：

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="next-steps"></a>後續步驟

- [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
- [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
- [停用企業應用程式的使用者登入](disable-user-sign-in-portal.md)
- [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)
