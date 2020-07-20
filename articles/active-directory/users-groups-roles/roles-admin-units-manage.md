---
title: 新增和移除管理單位（預覽）-Azure Active Directory |Microsoft Docs
description: 使用管理單位來限制 Azure Active Directory 中的角色許可權範圍。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 977a90419c142e576fcf484562875d12c8dad451
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851763"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>管理 Azure Active Directory 中的管理單位

若要在 Azure Active Directory （Azure AD）中進行更細微的系統管理控制，您可以將使用者指派給範圍限制為一或多個管理單位（au）的 Azure AD 角色。

## <a name="get-started"></a>開始使用

1. 若要透過 [[圖形瀏覽器]](https://aka.ms/ge)執行下列指示的查詢，請執行下列動作：

    a. 在 Azure 入口網站中，移至 Azure AD。 在應用程式清單中，選取 [ **Graph Explorer]**，然後選取 **[將管理員同意授與 graph explorer]**。

    ![顯示 [授與系統管理員同意] 連結的螢幕擷取畫面](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. 在 [圖形瀏覽器] 中，選取**測試**版。

    ![顯示已選取測試版的螢幕擷取畫面](./media/roles-admin-units-manage/select-beta-version.png)

1. 使用 Azure AD PowerShell 的預覽版本。

## <a name="add-an-administrative-unit"></a>新增管理單位

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 在 [Azure 入口網站中，移至 [Azure AD]，然後在左窗格中選取 [**管理單位**]。

    ![Azure AD 中 [管理單位（預覽）] 連結的螢幕擷取畫面](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. 選取 [**新增**]，然後輸入管理單位的名稱。 （選擇性）新增管理單位的描述。

    ![[新增] 按鈕和文字方塊的螢幕擷取畫面，用來輸入管理單位的名稱](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. 選取 [**新增**] 以完成管理單位。

### <a name="use-powershell"></a>使用 PowerShell

請先安裝 Azure AD PowerShell （預覽），然後再嘗試執行下列命令：

```powershell
Connect-AzureAD
New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

您可以視需要修改括在引號中的值。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>移除管理單位

在 Azure AD 中，您可以移除不再需要作為系統管理角色範圍單位的管理單位。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 在 [Azure 入口網站中，移至**Azure AD**  >  **管理單位**]。 
1. 選取要刪除的管理單位，然後選取 [**刪除**]。 
1. 若要確認您想要刪除管理單位，請選取 **[是]**。 系統管理單位已刪除。

![[管理單位刪除] 按鈕和確認視窗的螢幕擷取畫面](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>使用 PowerShell

```powershell
$delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId
```

您可以依據特定環境的需求，修改括在引號中的值。

### <a name="use-the-graph-api"></a>使用圖形 API

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>後續步驟

* [管理管理單位中的使用者](roles-admin-units-add-manage-users.md)
* [管理單位中的群組](roles-admin-units-add-manage-groups.md)
