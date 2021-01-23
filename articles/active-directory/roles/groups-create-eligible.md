---
title: 建立群組以在 Azure Active Directory 中指派角色 |Microsoft Docs
description: 瞭解如何在 Azure AD 中建立可指派角色的群組。 在 Azure 入口網站、PowerShell 或圖形 API 中管理 Azure 角色。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f06a1e9ea83fc7ad758ad17245ffa5d7ca973f6
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742093"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>在 Azure Active Directory 中建立可指派角色的群組

您只能將角色指派給使用設定為 True 的 ' isAssignableToRole ' 屬性所建立的群組，或是在 Azure AD 入口網站中建立， **Azure AD 角色可以指派給開啟的群組** 。 此群組屬性會讓群組之一可以指派給 Azure Active Directory (Azure AD) 中的角色。 本文說明如何建立這種特殊的群組。 **注意：** 將 isAssignableToRole 屬性設定為 true 的群組不能是動態成員資格類型。 如需詳細資訊，請參閱 [使用群組管理 Azure AD 角色指派](groups-concept.md)。

## <a name="using-azure-ad-admin-center"></a>使用 Azure AD 系統管理中心

1. 使用 Azure AD 組織的特殊權限角色管理員或全域系統管理員許可權登入 [Azure AD admin center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。
1. 選取 [**群組**  >  **所有群組**]  >  **新群組**。

    [![開啟 Azure Active Directory 並建立新的群組。](./media/groups-create-eligible/new-group.png "開啟 Azure Active Directory 並建立新的群組。")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. 在 [ **新增群組** ] 索引標籤上，提供群組類型、名稱和描述。
1. 開啟 **Azure AD 角色可以指派給群組**。 只有特殊許可權角色管理員和全域管理員可以看到此切換參數，因為這些只有兩個可設定交換器的角色。

    [![讓新群組符合角色指派的資格](./media/groups-create-eligible/eligible-switch.png "讓新群組符合角色指派的資格")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. 選取群組的成員和擁有者。 您也可以選擇將角色指派給群組，但是這裡不需要指派角色。

    [![將成員新增至角色可指派的群組並指派角色。](./media/groups-create-eligible/specify-members.png "將成員新增至角色可指派的群組並指派角色。")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. 指定成員和擁有者之後，請選取 [ **建立**]。

    [![[建立] 按鈕位於頁面的底部。](./media/groups-create-eligible/create-button.png "[建立] 按鈕位於頁面的底部。")](./media/groups-create-eligible/create-button.png#<lightbox>)

系統會以您指派給群組的任何角色來建立群組。

## <a name="using-powershell"></a>使用 PowerShell

### <a name="install-the-azure-ad-preview-module"></a>安裝 Azure AD preview 模組

```powershell
install-module azureadpreview
import-module azureadpreview
```

若要確認模組可供使用，請發出下列命令：

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>建立可指派給角色的群組

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

若為這種類型的群組，則 `isPublic` 一律為 false，而且 `isSecurityEnabled` 一律會是 true。

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>將一個群組的使用者和服務主體複製到可指派角色的群組

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>在 Azure AD 中建立可指派角色的群組

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

若為這種類型的群組，則 `isPublic` 一律為 false，而且 `isSecurityEnabled` 一律會是 true。

## <a name="next-steps"></a>後續步驟

- [將角色指派給雲端群組](groups-assign-role.md)
- [使用雲端群組來管理角色指派](groups-concept.md)
- [對指派給雲端群組的角色進行疑難排解](groups-faq-troubleshooting.md)
