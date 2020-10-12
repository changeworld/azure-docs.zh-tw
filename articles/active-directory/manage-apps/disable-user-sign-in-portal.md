---
title: 在 Azure AD 中停用企業應用程式的使用者登入
description: 如何在 Azure Active Directory 中停用企業應用程式，讓任何使用者都無法登入它
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6fc9a4716f1427257b3bbf18e5fa653567e141
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763375"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中停用企業應用程式的使用者登入

您可以輕鬆停用企業應用程式，如此一來，使用者就不能在 Azure Active Directory (Azure AD) 登入。 您需要適當的許可權，才能管理企業應用程式。 而且，您必須是目錄的全域管理員。

## <a name="how-do-i-disable-user-sign-ins"></a>如何停用使用者登入？

1. 使用具有目錄全域管理員身分的帳戶登入 [Azure 入口網站](https://portal.azure.com) 。
1. 選取 [ **所有服務**]，在文字方塊中輸入 **Azure Active Directory** ，然後選取 [ **輸入**]。
1. 在 [ **Azure Active Directory**  -   ***directoryname*** ] 窗格上 (也就是您所管理之目錄的 Azure AD 窗格) ，選取 [**企業應用程式**]。
1. 在 [ **企業應用程式-所有應用程式** ] 窗格中，您會看到可管理的應用程式清單。 選取應用程式。
1. 在 [appname]****** 窗格 (亦即標題中含有所選應用程式之名稱的窗格) 上，選取 [屬性]****。
1. 在 [ ***appname***  -  **屬性**] 窗格上，選取 [**否**]，讓**使用者登入？**。
1. 選取 [儲存] **** 命令。

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>使用 Azure AD PowerShell 來停用未列出的應用程式

如果您知道應用程式的 AppId 未出現在企業應用程式清單上 (例如，因為您已刪除應用程式，或因為 Microsoft) 預先授權的應用程式而尚未建立服務主體，您可以手動建立應用程式的服務主體，然後使用 [AzureAD PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)將它停用。

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

## <a name="next-steps"></a>接下來的步驟

* [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
* [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
* [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)
