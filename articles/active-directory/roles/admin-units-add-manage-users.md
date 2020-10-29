---
title: 新增、移除及列出管理單位中的使用者-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 的管理單位中管理使用者及其角色許可權
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
ms.openlocfilehash: c877878fe25d4c6c8904840c3c3350fbe2acf7b5
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026661"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 的管理單位中新增和管理使用者

在 Azure Active Directory (Azure AD) ，您可以將使用者新增至管理單位，以取得更細微的控制管理範圍。

若要準備使用 PowerShell 和 Microsoft Graph 來管理管理單位，請參閱 [開始](admin-units-manage.md#get-started)使用。

## <a name="add-users-to-an-administrative-unit"></a>將使用者新增至管理單位

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

您可以將使用者個別指派給系統管理單位或大量操作。

- 從使用者設定檔指派個別使用者：

   1. 使用具有特殊許可權的角色系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com) 。

   1. 選取 [ **使用者** ]，然後開啟使用者的設定檔，選取要指派給管理單位的使用者。
   
   1. 選取 [ **管理單位** ]。 
   
   1. 若要將使用者指派給一個或多個管理單位，請選取 [ **指派給管理單位** ]，然後在右窗格中選取您要指派給使用者的管理單位。

       ![將使用者指派給管理單位的 [管理單位] 窗格螢幕擷取畫面。](./media/admin-units-add-manage-users/assign-users-individually.png)

- 從管理單位指派個別使用者：

   1. 使用具有特殊許可權的角色系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com) 。
   1. 選取 [ **管理單位** ]，然後選取要指派使用者的管理單位。
   1. 選取 [ **所有使用者** ]，選取 [ **新增成員** ]，然後在 [ **新增成員** ] 窗格中，選取您要指派給管理單位的一或多個使用者。

        ![將使用者指派給管理單位的管理單位 [使用者] 窗格螢幕擷取畫面。](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- 將使用者指派為大量作業：

   1. 使用具有特殊許可權的角色系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com) 。

   1. 選取 [ **管理單位** ]。

   1. 選取您要新增使用者的管理單位。

   1. 選取 **使用者**  >  **大量活動**  >  **大量新增成員** 。 然後，您可以將逗點分隔值 (CSV) 範本中下載並編輯檔案。 格式很簡單，需要在每一行加入單一使用者主要名稱。 檔案準備就緒後，請將它儲存到適當的位置，然後將它上傳做為此步驟的一部分。

      ![[使用者] 窗格的螢幕擷取畫面，可將使用者指派給管理單位做為大量操作。](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>使用 PowerShell

在 PowerShell 中，使用 `Add-AzureADAdministrativeUnitMember` 下列範例中的 Cmdlet 將使用者新增至管理單位。 您要在其中新增使用者之管理單位的物件識別碼，以及您想要新增之使用者的物件識別碼，都會被當作引數。 變更您特定環境所需的醒目提示區段。

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```


### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

將預留位置取代為測試資訊，然後執行下列命令：

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

範例：

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>查看使用者的系統管理單位清單

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

在 Azure 入口網站中，您可以執行下列動作來開啟使用者的設定檔：

1. 移至 **Azure AD** ，然後選取 [ **使用者** ]。

1. 選取您要查看其設定檔的使用者。

1. 選取 [ **管理單位** ]，以顯示已指派給使用者的系統管理單位清單。

   ![已指派使用者的系統管理單位螢幕擷取畫面。](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>使用 PowerShell

執行下列命令：

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
> [!NOTE]
> 根據預設， `Get-AzureADAdministrativeUnitMember` 只會傳回一個管理單位的100成員。 若要取得更多成員，您可以加入 `"-All $true"` 。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

將預留位置取代為測試資訊，然後執行下列命令：

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>從管理單位移除單一使用者

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

您可以使用下列兩種方式之一，從管理單位移除使用者： 

* 在 [Azure 入口網站中，移至 [ **Azure AD** ]，然後選取 [ **使用者** ]。 
  1. 選取使用者以開啟使用者的設定檔。 
  1. 選取您要從中移除使用者的系統管理單位，然後選取 [ **從管理單位移除** ]。

     ![顯示如何從使用者的 [設定檔] 窗格中，將使用者從管理單位移除的螢幕擷取畫面。](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* 在 Azure 入口網站中，移至 [ **Azure AD** ]，然後選取 [ **管理單位** ]。
  1. 選取您要從中移除使用者的管理單位。 
  1. 選取使用者，然後選取 [ **移除成員** ]。
  
     ![顯示如何在系統管理單位層級移除使用者的螢幕擷取畫面。](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>使用 PowerShell

執行下列命令：

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

將預留位置取代為測試資訊，然後執行下列命令：

`https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref`

## <a name="remove-multiple-users-as-a-bulk-operation"></a>移除多個使用者做為大量操作

若要從管理單位移除多個使用者，請執行下列動作：

1. 在 Azure 入口網站中，移至 **Azure AD** 。

1. 選取 [ **管理單位** ]，然後選取您想要從中移除使用者的管理單位。 

1. 選取 [ **大量移除成員** ]，然後下載您要用來列出您想要移除之使用者的 CSV 範本。

   ![螢幕擷取畫面，顯示 [使用者] 窗格上的 [大量移除成員] 連結。](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. 使用相關的使用者專案來編輯下載的 CSV 範本。 請勿移除範本的前兩個數據列。 在每個資料列中加入一個使用者主體名稱 (UPN) 。

   ![已編輯之 CSV 檔案的螢幕擷取畫面，可從大量的管理單位中移除使用者。](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. 儲存您的變更、上傳檔案，然後選取 [ **提交** ]。

## <a name="next-steps"></a>後續步驟

- [將角色指派至管理單位](admin-units-assign-roles.md)
- [將群組新增至管理單位](admin-units-add-manage-groups.md)
