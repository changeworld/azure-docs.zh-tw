---
title: 從 Azure AD 中的使用者體驗中隱藏應用程式
description: 如何從 Azure Active Directory 存取面板或 Office 365 啟動器的使用者體驗中隱藏應用程式。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5718adf4fd76e2fbd0ff793dd2fa33ee08f7c0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295057"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>對 Azure Active Directory 中的使用者隱藏應用程式

如何在使用者的 MyApps 面板或 Office 365 啟動器中隱藏應用程式的指示。 隱藏應用程式時，使用者仍有應用程式的權限。 

## <a name="prerequisites"></a>Prerequisites

需具備應用程式管理員權限，才能對 MyApps 面板和 Office 365 啟動器隱藏應用程式。

需具備全域管理員權限，才能隱藏所有的 Office 365 應用程式。


## <a name="hide-an-application-from-the-end-user"></a>對使用者隱藏應用程式
使用下列步驟，在 MyApps 存取面板和 Office 365 應用程式啟動器中隱藏應用程式。

1.  以目錄的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2.  選擇**Azure 活動目錄**。
3.  選取 [企業應用程式]****。 [企業應用程式 - 所有應用程式]**** 刀鋒視窗隨即開啟。
4.  在 [應用程式類型]**** 下，選取 [企業應用程式]\(如果尚未選取)****。
5.  搜尋您想要隱藏的庫應用程式，然後按一下該應用程式。  應用程式的概觀隨即開啟。
6.  按一下 **[屬性]**。 
7.  針對 [是否要向使用者顯示]**** 問題，按一下 [否]****。
8.  按一下 [儲存]****。

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>使用 Azure AD 電源外殼隱藏應用程式

要從 MyApps 面板中隱藏應用程式，可以手動將 HideApp 標記添加到應用程式的服務主體中。 運行以下[AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals)命令，將應用程式的"**使用者可見"** 屬性設置為 **"否**"。 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>在使用者存取面板中隱藏 Office 365 應用程式

您可以使用下列步驟在 MyApps 面板中隱藏所有 Office 365 應用程式。 這些應用程式仍會顯示在 Office 365 入口網站中。

1.  以目錄的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2.  選擇**Azure 活動目錄**。
3.  選擇**使用者設置**。
4.  在 [企業應用程式]**** 之下，按一下 [管理終端使用者如何啟動及檢視其應用程式]****。
5.  針對 [使用者只能在 Office 365 入口網站看到 Office 365 應用程式]****，按一下 [是]****。
6.  按一下 [儲存]****。

## <a name="next-steps"></a>後續步驟
* [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
* [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
* [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)

