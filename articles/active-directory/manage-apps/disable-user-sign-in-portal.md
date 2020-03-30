---
title: 禁用 Azure AD 中企業應用的使用者登錄
description: 如何在 Azure Active Directory 中停用企業應用程式，讓任何使用者都無法登入它
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
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274099"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中停用企業應用程式的使用者登入

禁用企業應用程式很容易，因此使用者無法在 Azure 活動目錄 （Azure AD） 中登錄到它。 您需要適當的許可權來管理企業應用。 而且，您必須是目錄的全域管理員。

## <a name="how-do-i-disable-user-sign-ins"></a>如何停用使用者登入？

1. 使用目錄的全域管理員的帳戶登錄到[Azure 門戶](https://portal.azure.com)。
1. 選擇**所有服務**，在文字方塊中輸入**Azure 活動目錄**，然後選擇"**輸入**"。
1. 在**Azure 活動目錄** -  ***名稱***窗格（即要管理的目錄的 Azure AD 窗格）上，選擇**企業應用程式**。
1. 在 **"企業版應用程式 - 所有應用程式**"窗格中，您將看到可以管理的應用清單。 選取應用程式。
1. 在 [appname]****** 窗格 (亦即標題中含有所選應用程式之名稱的窗格) 上，選取 [屬性]****。
1. 在 ***"應用名稱*** - **屬性"** 窗格中，選擇 **"為使用者啟用****"** 以登錄？
1. 選取 [儲存] **** 命令。

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>使用 Azure AD 電源外殼禁用未列出的應用

如果您知道未顯示在企業應用清單中的應用的 AppId（例如，由於您刪除了應用或由於應用被 Microsoft 預授權而尚未創建服務主體），則可以手動為應用創建服務主體，然後使用[AzureAD PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)將其禁用。

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>後續步驟

* [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
* [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
* [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)
