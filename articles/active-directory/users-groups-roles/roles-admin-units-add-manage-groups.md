---
title: 新增、移除及列出管理單位中的群組-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 的管理單位中管理群組及其角色權限
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 324d0ff0659270c6f2e90c0456ded83344f73936
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264988"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>在 Azure Active Directory 中的管理單位新增和管理群組

在 Azure Active Directory (Azure AD) 中，您可以將群組新增至管理單位 (AU)，以取得更細微的控制管理範圍。

如需準備使用 PowerShell 和 Microsoft Graph 進行管理單位管理的步驟，請參閱[快速入門](roles-admin-units-manage.md#get-started)。

## <a name="add-groups-to-an-au"></a>將群組新增至 AU

### <a name="azure-portal"></a>Azure 入口網站

您只能將群組個別指派給管理單位。 沒有將群組大量指派給管理單位的選項。 您可以使用入口網站中的兩種方式之一，將群組指派給管理單位：

1. 從 **Azure AD > 群組**頁面

    開啟 Azure AD 中的群組概觀頁面，然後選取需要指派給管理單位的群組。 在左側，選取 [管理單位] 以列出群組指派目的地的管理單位。 在頂端，您會發現 [指派給管理單位] 選項，按一下系統會在右側提供一個面板來選擇管理單位。

    ![將群組個別指派給管理單位](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. 從 **Azure AD > 群組單位 > 所有群組** 頁面

    在 Azure AD > 管理單位中開啟 [所有群組] 刀鋒視窗。 如果有已指派給管理單位的群組，則會顯示在右側。 選取頂端的 [新增]，右側面板會列出您 Azure AD 組織中可用的群組。 選取要指派給管理單位的一或多個群組。

    ![選取管理單位，然後選取新增成員](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

在此範例中，會使用 Cmdlet Add-AzureADAdministrativeUnitMember 將群組新增至管理單位。 管理單位的物件識別碼，以及要新增的群組物件識別碼會作為引數。 反白顯示的區段可能會視特定環境的需要而變更。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
}
```

範例：

```http
{
"@odata.id":"https://graph.microsoft.com/beta/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="list-groups-in-an-au"></a>列出 AU 中的群組

### <a name="azure-portal"></a>Azure 入口網站

在入口網站中，移至 **Azure AD > 管理單位**。 選取您要列出使用者的管理單位。 根據預設，左側面板上已選取 [所有使用者]。 選取 [所有群組]，然後您會在右側找到屬於所選管理單位成員的群組清單。

![列出管理單位中的群組](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

這可協助您取得管理單位的所有成員。 如果您要顯示屬於管理單位成員的所有群組，可以使用下列程式碼片段：

```http
foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```
### <a name="microsoft-graph"></a>Microsoft Graph

```http
HTTP request
GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="list-aus-for-a-group"></a>列出群組的 AU

### <a name="azure-portal"></a>Azure 入口網站

在 Azure AD 入口網站中，您可以透過開啟**群組**，開啟群組的詳細資料。 選取群組以開啟群組的設定檔。 選取 [管理單位] 以列出群組為所屬成員的所有管理單位。

![列出群組的管理單位](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-au"></a>從 AU 移除群組

### <a name="azure-portal"></a>Azure 入口網站

有兩種方式可從 Azure 入口網站中的管理單位移除群組。

開啟 **Azure AD** > **群組**，然後開啟您要從管理單位移除之群組的設定檔。 選取左側窗格中的 [管理單位]，以列出群組為所屬成員的所有管理單位。 選取您要移除群組的管理單位，然後選取 [從管理單位移除]。

![從管理單位移除群組](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

或者，您可以移至 **Azure AD** > **管理單位** 並選取群組為所屬成員的管理單位。 選取左側面板中的 [群組] 以列出成員群組。 選取要從管理單位移除的群組，然後選取 [移除群組]。

![列出管理單位中的群組](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>後續步驟

- [將角色指派至管理單位](roles-admin-units-assign-roles.md)
- [管理管理單位中的使用者](roles-admin-units-add-manage-users.md)
