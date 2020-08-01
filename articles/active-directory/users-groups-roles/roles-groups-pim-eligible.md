---
title: 使用 Azure AD 中的 Privileged Identity Management，將角色指派給群組 |Microsoft Docs
description: 預覽用來委派身分識別管理的自訂 Azure AD 角色。 在 Azure 入口網站、PowerShell 或圖形 API 中管理 Azure 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343da87048cf43c04a137376e9a7f24270ce729a
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476018"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>使用 Privileged Identity Management 將角色指派給群組

本文說明如何使用 Azure AD Privileged Identity Management （PIM）將 Azure Active Directory （Azure AD）角色指派給群組。

> [!NOTE]
> 您必須使用 Privileged Identity Management 的更新版本，才能使用 PIM 將群組指派給 Azure AD 角色。 如果您的 Azure AD 組織會利用 Privileged Identity Management API，您可能會在較舊版本的 PIM 上。 若是如此，請聯繫別名 pim_preview@microsoft.com 以移動您的組織並更新您的 API。 若要深入瞭解，請[AZURE AD PIM 中的角色和功能](../privileged-identity-management/azure-ad-roles-features.md)。

## <a name="using-azure-ad-admin-center"></a>使用 Azure AD 系統管理中心

1. 以特殊權限角色管理員或您組織中的全域管理員登入[Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) 。

1. 選取**Privileged Identity Management**  >  **Azure AD 角色**  >  **角色**  >  **新增指派**

1. 選取角色，然後選取群組。 只會顯示符合角色指派（角色可指派群組）資格的群組，而不是所有群組。

    ![選取您要為其指派角色的使用者](./media/roles-groups-pim-eligible/select-member.png)

1. 選取所需的成員資格設定。 針對需要啟用的角色，選擇 [**符合資格**]。 根據預設，使用者會永久符合資格，但您也可以設定使用者資格的開始和結束時間。 完成後，請按 [儲存] 並新增來完成角色指派。

    ![選取您要為其指派角色的使用者](./media/roles-groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>使用 PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>下載 Azure AD Preview PowerShell 模組

若要安裝 Azure AD #PowerShell 模組，請使用下列 Cmdlet：

```powershell
install-module azureadpreview
import-module azureadpreview
```

若要確認模組已準備好可供使用，請使用下列 Cmdlet：

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>將群組指派為角色的合格成員

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>後續步驟

- [使用雲端群組來管理角色指派](roles-groups-concept.md)
- [針對指派給雲端群組的角色進行疑難排解](roles-groups-faq-troubleshooting.md)
- [在 Privileged Identity Management 中設定 Azure AD 系統管理員角色設定](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中指派 Azure 資源角色](../privileged-identity-management/pim-resource-roles-assign-roles.md)
