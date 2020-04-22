---
title: 在管理單元(預覽)中添加、刪除和列出使用者 - Azure 活動目錄 |微軟文件
description: 在 Azure 活動目錄中的管理單元中管理使用者及其角色權限
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684963"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure 活動目錄中的管理單元中加入及管理使用者

在 Azure 活動目錄 (Azure AD) 中,您可以將使用者添加到管理單位 (AU) 以進行更精細的管理控制範圍。

有關準備使用 PowerShell 和 Microsoft 圖形進行管理單元管理的步驟,請參閱[入門](roles-admin-units-manage.md#get-started)。

## <a name="add-users-to-an-au"></a>將使用者加入到 AU

### <a name="azure-portal"></a>Azure 入口網站

您可以通過兩種方式將使用者分配給管理單位。

1. 個人配置

    1. 您可以轉到門戶中的 Azure AD 並選擇「使用者」並選擇要分配給管理單元的使用者。 然後,您可以在左側面板中選擇"管理單位" 單擊「分配給管理單位」並選擇要分配使用者的管理單位,可以將使用者分配給一個或多個管理單位。

       ![選擇「新增」,然後輸入管理單位的名稱](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. 您可以轉到門戶中的 Azure AD,然後在左側窗格中選擇管理單位,然後選擇要分配使用者的管理單位。 選擇左側窗格上的所有使用者,然後選擇"添加成員"。 然後,您可以繼續從右側窗格選擇要分配給管理單位的一個或多個使用者。

        ![選擇管理單位,然後選擇"添加成員"](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. 大量配置

    轉到門戶中的 Azure AD 並選擇管理單位。 選擇要添加使用者的管理單位。 繼續按下所有使用者 -> 從 .csv 檔案添加成員。 然後,您可以下載 CSV 樣本並編輯該檔。 格式很簡單,需要在每行中添加單個 UPN。 檔準備就緒后,將其保存在適當的位置,然後在步驟 3 中上載,如快照中突出顯示的那樣。

    ![大量使用者配置管理單位](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

在上面的示例中,cmdlet Add-AzureAD管理單位成員用於將使用者添加到管理單位。 要添加使用者的管理單元的物件 ID 和要添加的使用者的物件 ID 被視為參數。 突出顯示的部分可以根據需要更改特定環境。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

範例：

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>列出使用者的管理單位

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 門戶中,可以通過訪問 Azure AD >用户来打开用户的配置文件。 單擊使用者以打開使用者的配置檔。

![在 Azure 活動目錄開啟使用者的設定檔](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

選擇左側面板上的**管理單位**以查看已分配使用者的管理單位清單。

![列出使用者的管理單位](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>從 AU 中移除單個使用者

### <a name="azure-portal"></a>Azure 入口網站

有兩種方法可以從管理單位中刪除使用者。 在 Azure 門戶中,可以通過訪問**Azure AD** > **使用者**來打開使用者的配置檔。 選擇使用者以打開使用者的配置檔。 選擇要從中移除使用者的管理單位,然後選擇**從管理單元中刪除**。

![從管理單位移除使用者設定檔](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

還可以通過選擇要從中刪除使用者的管理單位來刪除**Azure AD** > **管理單元**中的使用者。 選擇使用者並選擇 **「刪除成員**」。
  
![在管理單位等級移除使用者](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>大量使用多個使用者

您可以轉到 Azure AD >管理单元,並選擇要從中刪除使用者的管理單位。 按下批量刪除成員。 下載 CSV 樣本,以提供要刪除的使用者清單。

使用相關使用者項目編輯下載的 CSV 樣本。 不要刪除範本的前兩行。 在每行中添加一個使用者 UPN。

![編輯 CSV 檔案,以便從管理單位中移除大量使用者](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

將項目儲存在檔案中後,上傳檔,選擇「**提交**」 。

![提交批次上傳檔案](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>後續步驟

- [將角色配置給管理單位](roles-admin-units-assign-roles.md)
- [新增群組到管理單位](roles-admin-units-add-manage-groups.md)
