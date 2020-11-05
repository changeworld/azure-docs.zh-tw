---
title: 新增、移除及列出管理單位中的群組-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 的管理單位中管理群組及其角色許可權。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 092604429cf5a527d7ee62b412e879ad9991eace
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394742"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 的管理單位中新增和管理群組

在 Azure Active Directory (Azure AD) ，您可以將群組新增至管理單位，以取得更細微的控制管理範圍。

若要準備使用 PowerShell 和 Microsoft Graph 來管理管理單位，請參閱 [開始](admin-units-manage.md#get-started)使用。

## <a name="add-groups-to-an-administrative-unit"></a>將群組新增至管理單位

您可以使用 Azure 入口網站、PowerShell 或 Microsoft Graph，將群組新增至管理單位。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

您只能將個別群組指派給管理單位。 沒有任何選項可將群組指派為大量操作。 在 Azure 入口網站中，您可以使用下列兩種方式之一，將群組指派給管理單位：

* 從 [ **群組** ] 窗格：

  1. 在 Azure 入口網站中，移至 **Azure AD** 。
  1. 選取 [ **群組** ]，然後選取您要指派給管理單位的群組。 
  1. 在左窗格中，選取 [ **管理單位** ]，以顯示指派給群組的管理單位清單。 

     ![[管理單位] 窗格上 [指派給管理單位] 連結的螢幕擷取畫面。](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. 選取 [ **指派給管理單位** ]。
  1. 選取右窗格中的管理單位。

* 從 [ **管理單位**  >  **所有群組** ] 窗格：

  1. 在 Azure 入口網站中，移至 **Azure AD** 。
  
  1. 在左窗格中，選取 [ **管理單位** ]，然後選取 [ **所有群組** ]。 
     任何已指派給管理單位的群組都會顯示在右窗格中。 

  1. 在 [ **群組** ] 窗格中，選取 [ **新增** ]。
    右窗格會列出您 Azure AD 組織中的所有可用群組。 

     ![將群組新增至管理單位的 [新增] 按鈕的螢幕擷取畫面。](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. 選取一或多個要指派給管理單位的群組，然後選取 [ **選取** ] 按鈕。

### <a name="use-powershell"></a>使用 PowerShell

在下列範例中，使用 `Add-AzureADMSAdministrativeUnitMember` Cmdlet 將群組新增至管理單位。 系統管理單位的物件識別碼和要加入之群組的物件識別碼都會被當作引數。 變更您特定環境所需的醒目提示區段。


```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

執行下列命令：

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{id}"
}
```

範例：

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>在管理單位中查看群組清單

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 在 Azure 入口網站中，移至 **Azure AD** 。

1. 在左窗格中，選取 [ **管理單位** ]，然後選取您想要查看其群組的管理單位。 依預設，會在左窗格中選取 [ **所有使用者** ]。 

1. 在左窗格中，選取 [ **群組** ]。 右窗格會顯示屬於所選管理單位成員的群組清單。

   ![[群組] 窗格的螢幕擷取畫面，其中顯示管理單位中的群組清單。](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>使用 PowerShell

若要顯示管理單位所有成員的清單，請執行下列命令： 

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

若要顯示屬於管理單位成員的所有群組，請使用下列程式碼片段：

```http
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

執行以下命令：

```http
HTTP request
GET /directory/administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>查看群組的系統管理單位清單

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 在 Azure 入口網站中，移至 **Azure AD** 。

1. 在左窗格中，選取 [ **群組** ] 以顯示群組清單。

1. 選取群組以開啟群組的設定檔。 

1. 在左窗格中，選取 [ **管理單位** ]，以列出群組為其成員的所有管理單位。

   ![[管理單位] 窗格的螢幕擷取畫面，其中顯示指派給群組的清單管理單位。](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>使用 PowerShell

執行以下命令：

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

執行以下命令：

```http
https://graph.microsoft.com/v1.0/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>從管理單位移除群組

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

您可以使用下列兩種方式之一，從 Azure 入口網站中的管理單位移除群組：

- 將它從群組中移除總覽：

  1. 在 Azure 入口網站中，移至 **Azure AD** 。
  1. 在左窗格中，選取 [ **群組** ]，然後針對您要從管理單位移除的群組開啟設定檔。
  1. 在左窗格中，選取 [ **管理單位** ]，以列出指派給群組的所有管理單位。 
  1. 選取您要移除群組的管理單位，然後選取 [從管理單位移除]。

     ![[管理單位] 窗格的螢幕擷取畫面，其中顯示指派給所選管理單位的群組清單。](./media/admin-units-add-manage-groups/group-au-remove.png)

- 從管理單位移除：

  1. 在 Azure 入口網站中，移至 **Azure AD** 。
  1. 在左窗格中，選取 [ **管理單位** ]，然後選取指派給群組的管理單位。
  1. 在左窗格中，選取 [ **群組** ] 以列出指派給管理單位的所有群組。
  1. 選取您要移除的群組，然後選取 [ **移除群組** ]。

    ![[群組] 窗格的螢幕擷取畫面，其中顯示管理單位中的群組清單。](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>使用 PowerShell

執行以下命令：

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

執行以下命令：

```http
https://graph.microsoft.com/v1.0/directory/AdministrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>後續步驟

- [將角色指派至管理單位](admin-units-assign-roles.md)
- [管理管理單位中的使用者](admin-units-add-manage-users.md)
