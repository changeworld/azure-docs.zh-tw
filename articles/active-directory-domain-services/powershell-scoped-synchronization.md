---
title: 使用 PowerShell 進行 Azure AD Domain Services 的限域同步處理 |Microsoft Docs
description: 瞭解如何使用 Azure AD PowerShell 來設定從 Azure AD 到 Azure Active Directory Domain Services 受控網域的限域同步處理
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: iainfou
ms.openlocfilehash: 197ae37b0c63b19ebe4dcdf2732169be0f357a07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294078"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 設定從 Azure AD 到 Azure Active Directory Domain Services 的限域同步處理

為了提供驗證服務，Azure Active Directory Domain Services (Azure AD DS) 從 Azure AD 同步處理使用者和群組。 在混合式環境中，內部部署 Active Directory Domain Services (AD DS) 環境中的使用者和群組，可以先使用 Azure AD 同步處理至 Azure AD Connect，然後同步處理至 Azure AD DS。

根據預設，Azure AD 目錄中的所有使用者和群組都會同步至 Azure AD DS 受控網域。 如果您有特定需求，您可以改為選擇只同步處理一組已定義的使用者。

本文說明如何建立使用限域同步處理的受控網域，然後使用 Azure AD PowerShell 來變更或停用設定範圍的使用者集合。 您也可以 [使用 Azure 入口網站完成這些步驟][scoped-sync]。

## <a name="before-you-begin"></a>開始之前

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程，以 [建立並設定 Azure Active Directory Domain Services 受控網域][tutorial-create-instance]。
* 您需要 Azure AD 租使用者中的 *全域系統管理員* 許可權，才能變更 Azure AD DS 同步處理範圍。

## <a name="scoped-synchronization-overview"></a>限域同步處理總覽

根據預設，Azure AD 目錄中的所有使用者和群組都會同步至受控網域。 如果只有少數使用者需要存取受控網域，您只能同步處理這些使用者帳戶。 此範圍的同步處理是以群組為基礎。 當您設定以群組為基礎的限域同步處理時，只會將屬於您所指定群組的使用者帳戶同步至受控網域。 嵌套群組不會進行同步處理，只會同步處理您選取的特定群組。

當您建立受控網域或部署之後，您可以變更同步處理範圍。 您現在也可以在現有的受控網域上變更同步處理的範圍，而不需要重新建立。

若要深入瞭解同步處理常式，請參閱 [瞭解 Azure AD Domain Services 中的同步][concepts-sync]處理。

> [!WARNING]
> 變更同步處理的範圍會導致受控網域重新同步處理所有資料。 您必須考量下列事項：
>
>  * 當您變更受控網域的同步處理範圍時，會進行完整重新同步處理。
>  * 受控網域中不再需要的物件會被刪除。 系統會在受控網域中建立新物件。

## <a name="powershell-script-for-scoped-synchronization"></a>適用于限域同步處理的 PowerShell 腳本

若要使用 PowerShell 設定限域同步處理，請先將下列腳本儲存到名為的檔案中 `Select-GroupsToSync.ps1` 。

此腳本會設定 Azure AD DS，將選取的群組從 Azure AD 同步處理。 屬於指定群組一部分的所有使用者帳戶都會同步處理到受控網域。

此腳本適用于本文中的其他步驟。

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>啟用限域同步處理

若要為受控網域啟用以群組為基礎的限域同步處理，請完成下列步驟：

1. 先在 Azure AD DS 資源上設定 *"filteredSync" = "Enabled"* ，然後更新受控網域。

    出現提示時，請使用[AzureAD][Connect-AzureAD] Cmdlet 指定*全域管理員*的認證來登入您的 Azure AD 租使用者：

    ```powershell
    // Connect to your Azure AD tenant
    Connect-AzureAD

    // Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    // Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    // Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. 現在指定應將其使用者同步至受控網域的群組清單。

    執行 `Select-GroupsToSync.ps1` 腳本，並指定要同步處理的群組清單。在下列範例中，要同步處理的群組是 *GroupName1* 和 *GroupName2*。

    > [!WARNING]
    > 您必須將 *AAD DC 系統管理員* 群組包含在限域同步處理的群組清單中。 如果您未包含此群組，受控網域將無法使用。

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

變更同步處理的範圍會導致受控網域重新同步處理所有資料。 受控網域中不再需要的物件會被刪除，而重新同步處理可能需要很長的時間才能完成。

## <a name="modify-scoped-synchronization"></a>修改限域同步處理

若要修改使用者應同步至受控網域的群組清單，請執行 `Select-GroupsToSync.ps1` 腳本，並指定新的群組清單以進行同步處理。

在下列範例中，要同步處理的群組不再包含 *GroupName2*，現在包含 *GroupName3*。

> [!WARNING]
> 您必須將 *AAD DC 系統管理員* 群組包含在限域同步處理的群組清單中。 如果您未包含此群組，受控網域將無法使用。

出現提示時，請使用[AzureAD][Connect-AzureAD] Cmdlet 指定*全域管理員*的認證來登入您的 Azure AD 租使用者：

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

變更同步處理的範圍會導致受控網域重新同步處理所有資料。 受控網域中不再需要的物件會被刪除，而重新同步處理可能需要很長的時間才能完成。

## <a name="disable-scoped-synchronization"></a>停用限域同步處理

若要停用受控網域的群組型限域同步處理，請在 Azure AD DS 資源上設定 *"filteredSync" = "Disabled* "，然後更新受控網域。 完成時，所有使用者和群組都會設定為從 Azure AD 同步處理。

出現提示時，請使用[AzureAD][Connect-AzureAD] Cmdlet 指定*全域管理員*的認證來登入您的 Azure AD 租使用者：

```powershell
// Connect to your Azure AD tenant
Connect-AzureAD

// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

變更同步處理的範圍會導致受控網域重新同步處理所有資料。 受控網域中不再需要的物件會被刪除，而重新同步處理可能需要很長的時間才能完成。

## <a name="next-steps"></a>接下來的步驟

若要深入瞭解同步處理常式，請參閱 [瞭解 Azure AD Domain Services 中的同步](synchronization.md)處理。

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
