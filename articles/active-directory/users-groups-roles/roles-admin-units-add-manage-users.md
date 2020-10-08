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
ms.openlocfilehash: 0d29f4ef5806eb8ed9385696dea78f4ae0992b93
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818242"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 的管理單位中新增和管理使用者

在 Azure Active Directory (Azure AD) ，您可以將使用者新增至管理單位 (AU) ，以取得更細微的控制管理範圍。

如需準備使用 PowerShell 和 Microsoft Graph 進行管理單位管理的步驟，請參閱[快速入門](roles-admin-units-manage.md#get-started)。

## <a name="add-users-to-an-au"></a>將使用者新增至 AU

### <a name="azure-portal"></a>Azure 入口網站

您可以將使用者個別或大量操作指派給系統管理單位。

- 使用者設定檔中的個別指派

   1. 使用具有特殊許可權的角色系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com) 。
   1. 選取 [ **使用者** ]，然後選取要指派給管理單位的使用者，以開啟使用者的設定檔。
   1. 選取 [ **管理單位**]。 您可以選取 [ **指派給管理單位** ]，然後選取要指派給使用者的管理單位，將使用者指派給一個或多個管理單位。

       ![選取 [新增]，然後輸入管理單位的名稱](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

- 從管理單位進行個別指派

   1. 使用具有特殊許可權的角色系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com) 。
   1. 選取 [ **管理單位** ]，然後選取要指派使用者的管理單位。
   1. 選取 [ **所有使用者** ]，然後選取 [ **新增成員** ]，從 [ **新增成員** ] 窗格選取要指派給管理單位的一或多個使用者。

        ![選取管理單位，然後選取新增成員](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

- 大量指派

   1. 使用具有特殊許可權的角色系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com) 。
   1. 選取 [ **管理單位**]。
   1. 選取要新增使用者的管理單位。
   1. 開啟 [**所有使用者**  >  **從 .csv 檔案新增成員**]。 然後，您可以將逗點分隔值 (CSV) 範本中下載並編輯檔案。 格式很簡單，需要在每一行新增單一使用者主體名稱。 檔案準備就緒後，請將它儲存在適當的位置，然後將它上傳做為此步驟的一部分。

    ![將使用者大量指派給管理單位](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

在上述範例中，會使用 AzureADAdministrativeUnitMember Cmdlet 將使用者新增至管理單位。 要加入使用者之管理單位的物件識別碼，以及要加入之使用者的物件識別碼，將會被視為引數。 反白顯示的區段可能會視特定環境的需要而變更。

### <a name="microsoft-graph"></a>Microsoft Graph

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

## <a name="list-administrative-units-for-a-user"></a>列出使用者的系統管理單位

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站您可以透過下列方式開啟使用者的設定檔：

1. 開啟**Azure AD**  >  的**使用者**。

1. 選取使用者以開啟使用者的設定檔。

1. 選取 [ **管理單位** ] 以查看已指派給使用者的系統管理單位清單。

   ![列出使用者的系統管理單位](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
注意：根據預設，AzureADAdministrativeUnitMember 只會傳回100成員，您可以新增 "-All $true" 以抓取更多成員。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>從 AU 移除單一使用者

### <a name="azure-portal"></a>Azure 入口網站

有兩種方式可以從管理單位移除使用者。 在 Azure 入口網站您可以前往 [ **Azure AD**使用者] 來開啟使用者的設定檔  >  ** **。 選取使用者以開啟使用者的設定檔。 選取您要移除使用者的系統管理單位，然後選取 [ **從管理單位移除**]。

![從使用者設定檔將使用者從管理單位移除](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

您也可以**Azure AD**  >  選取您想要移除使用者的系統管理單位，以 Azure AD**管理單位**來移除使用者。 選取使用者，然後選取 [ **移除成員**]。
  
![移除管理單位層級的使用者](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>大量移除一個以上的使用者

您可以移至 Azure AD > 管理單位，然後選取您想要從中移除使用者的管理單位。 按一下 [大量移除成員]。 下載 CSV 範本以提供要移除的使用者清單。

使用相關的使用者專案來編輯下載的 CSV 範本。 請勿移除範本的前兩個數據列。 在每個資料列中加入一個使用者 UPN。

![編輯 CSV 檔案以從系統管理單位移除大量使用者](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

在檔案中儲存專案之後，請上傳檔案，然後選取 [ **提交**]。

![提交大量上傳檔案](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>後續步驟

- [將角色指派至管理單位](roles-admin-units-assign-roles.md)
- [將群組新增至管理單位](roles-admin-units-add-manage-groups.md)
