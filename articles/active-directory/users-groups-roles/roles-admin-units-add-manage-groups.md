---
title: 在管理單元(預覽)中添加、刪除和列出組 - Azure 活動目錄 |微軟文件
description: 在 Azure 活動目錄中的管理單中管理群組及其角色權限
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
ms.openlocfilehash: 78f45d9e1ca5b8237779fe096154c83749aa08fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428429"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>在 Azure 活動目錄中在管理單元中加入及管理群組

在 Azure 活動目錄 (Azure AD) 中,您可以將組添加到管理單位 (AU) 以進行更精細的管理控制範圍。

有關準備使用 PowerShell 和 Microsoft 圖形進行管理單元管理的步驟,請參閱[入門](roles-admin-units-manage.md#getting-started)。

## <a name="add-groups-to-an-au"></a>新增群組到 AU

### <a name="azure-portal"></a>Azure 入口網站

在預覽中,只能將組單獨分配給管理單位。 沒有將組批量分配給管理單位的選項。 您可以通過門戶中的兩種方式之一將組分配給管理單位:

1. 從**Azure AD >组**「頁

    打開 Azure AD 中的「組概覽」頁,然後選擇要分配給管理單位的組。 在左側,選擇 **「管理單位」** 列出組分配到的管理單位。 在上面,你會發現選項"分配給管理單位",按一下它將在右側提供一個面板來選擇管理單元。

    ![將群組單獨分配給管理單位](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. 從**Azure AD >管理单位>所有群組**頁面

    打開 Azure AD 中的所有組邊欄選項卡>管理單位。 如果已分配給管理單位的組,則它們將顯示在右側。 選擇 **「在頂部添加**」,右側面板將滑動列出 Azure AD 組織中可用的群組。 選擇要分配給管理單位的一個或多個組。

    ![選擇管理單位,然後選擇"添加成員"](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

在此示例中,cmdlet Add-AzureAD管理單位成員用於將組添加到管理單位。 要添加的管理單元的物件 ID 和要添加的組的物件 ID 被視為參數。 突出顯示的部分可以根據需要更改特定環境。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

範例：

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>列出 AU 中群組

### <a name="azure-portal"></a>Azure 入口網站

跳到**Azure AD >门户中的管理单位**。 選擇要為其列出使用者的管理單位。 預設情況下,已在左邊面板上選擇**了所有使用者**。 選擇 **「所有群組**」,在右側,您將找到作為所選管理單位成員的組的清單。

![選擇要移除的管理單位](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

這將有助於您獲取管理單位的所有成員。 如果要顯示管理單位的所有群組,可以使用以下代碼段:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>列出群組的 A

### <a name="azure-portal"></a>Azure 入口網站

在 Azure AD 門戶中,可以通過打開組來打開**組**的詳細資訊。 選擇組以開啟組的設定檔。 選擇 **「管理單位」** 以列出組是成員的所有管理單位。

![列出列的行政單位](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>從 AU 中移除群組

### <a name="azure-portal"></a>Azure 入口網站

有兩種方法可以從 Azure 門戶中的管理單位中刪除組。

開啟**Azure AD** > **組**,並打開要從管理單位中刪除的組設定檔。 選擇左側面板中的 **「管理單位**」 以列出組是成員的所有管理單位。 選擇要從中移除群組的管理單位,然後選擇**從管理單位中移除**。

![從管理單位移除群組](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

或者,您可以轉到**Azure AD** > **管理單位**,並選擇組是成員的管理單位。 選擇左側面板中的**組**以列出成員組。 選擇要從管理單位中刪除的組,然後選擇 **「刪除群組**」 。。

![列出管理儲存格中群組](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>後續步驟

- [將角色配置給管理單位](roles-admin-units-assign-roles.md)
- [管理管理儲存的使用者](roles-admin-units-add-manage-users.md)
