---
title: 在 Azure AD 的使用者體驗中隱藏企業應用程式
description: 如何在 Azure Active Directory 存取面板或 Microsoft 365 啟動器中，隱藏企業應用程式的使用者體驗。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d2d6645bea6e99e9f62e36364adf8816329c26
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601134"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>在 Azure Active Directory 中隱藏終端使用者的企業應用程式

如何從終端使用者的 MyApps 面板或 Microsoft 365 啟動器隱藏應用程式的指示。 隱藏應用程式時，使用者仍有應用程式的權限。 

## <a name="prerequisites"></a>Prerequisites

需要應用程式系統管理員許可權，才能從 MyApps 面板和 Microsoft 365 啟動器中隱藏應用程式。

需要全域管理員許可權，才能隱藏所有 Microsoft 365 應用程式。


## <a name="hide-an-application-from-the-end-user"></a>對使用者隱藏應用程式
使用下列步驟，從 [MyApps] 面板中隱藏應用程式，並 Microsoft 365 應用程式啟動器。

1.  以目錄的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2.  選取 **Azure Active Directory**。
3.  選取 [企業應用程式]。 [企業應用程式 - 所有應用程式]**** 刀鋒視窗隨即開啟。
4.  在 [應用程式類型]**** 下，選取 [企業應用程式]\(如果尚未選取)****。
5.  搜尋您想要隱藏的庫應用程式，然後按一下該應用程式。  應用程式的概觀隨即開啟。
6.  按一下 **[屬性]** 。 
7.  針對 [是否要向使用者顯示]**** 問題，按一下 [否]****。
8.  按一下 [檔案] 。

> [!NOTE]
> 這些指示僅適用于企業應用程式。

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>使用 Azure AD PowerShell 來隱藏應用程式

若要從 MyApps 面板隱藏應用程式，您可以手動將 HideApp 標記加入至應用程式的服務主體。 執行下列 [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) 命令，將應用程式 **Visible** 屬性設定為 [ **否**]。 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>從 MyApps 面板隱藏 Microsoft 365 的應用程式

使用下列步驟，從 [MyApps] 面板中隱藏所有 Microsoft 365 應用程式。 這些應用程式仍會顯示在 Office 365 入口網站中。

1.  以目錄的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2.  選取 **Azure Active Directory**。
3.  選取 [使用者]。
4.  選取 [ **使用者設定**]。
5.  在 [企業應用程式]**** 之下，按一下 [管理終端使用者如何啟動及檢視其應用程式]****。
6.  針對 [使用者只能在 Office 365 入口網站看到 Office 365 應用程式]****，按一下 [是]****。
7.  按一下 [檔案] 。

## <a name="next-steps"></a>後續步驟
* [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
* [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
* [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)

