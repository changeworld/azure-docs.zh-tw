---
title: Azure AD Domain Services 的限域同步處理 |Microsoft Docs
description: 瞭解如何設定從 Azure AD 到 Azure Active Directory Domain Services 受控網域的範圍同步處理
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 9ef7e14cc2a290cc5583e3e599e278f98882152c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654729"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>設定從 Azure AD 到 Azure Active Directory Domain Services 的範圍同步處理

為了提供驗證服務，Azure Active Directory Domain Services （Azure AD DS）會同步處理 Azure AD 的使用者和群組。 在混合式環境中，內部部署 Active Directory Domain Services （AD DS）環境中的使用者和群組可以先使用 Azure AD Connect 同步處理 Azure AD，然後同步處理至 Azure AD DS。

根據預設，Azure AD 目錄中的所有使用者和群組都會同步處理到 Azure AD DS 受控網域。 如果您有特定需求，您可以改為選擇只同步處理一組已定義的使用者。

本文說明如何建立使用範圍同步處理的 Azure AD DS 受控網域，然後變更或停用一組已設定範圍的使用者。

## <a name="scoped-synchronization-overview"></a>限域同步處理總覽

根據預設，Azure AD 目錄中的所有使用者和群組都會同步處理到 Azure AD DS 受控網域。 如果只有少數幾個使用者需要存取受控網域，您只能同步處理那些使用者帳戶。 此限域同步處理是以群組為基礎。 當您設定以群組為基礎的範圍同步處理時，只有屬於您指定之群組的使用者帳戶會同步處理到 Azure AD DS 受控網域。

下表概述如何使用限域同步處理：

| 目前狀態 | 所需狀態 | 必要設定 |
| --- | --- | --- |
| 已將現有的受控網域設定為同步處理所有使用者帳戶和群組。 | 您只想要同步處理屬於特定群組的使用者帳戶。 | 您無法從同步處理所有使用者變更為使用限域同步處理。 [刪除現有的受控網域](delete-aadds.md)，然後依照這篇文章中的步驟，重新建立已設定範圍同步處理的 Azure AD DS 受控網域。 |
| 沒有現有的受控網域。 | 您想要建立新的受控網域，並只同步屬於特定群組的使用者帳戶。 | 請遵循本文中的步驟，建立已設定範圍同步處理的 Azure AD DS 受控網域。 |
| 現有的受控網域已設定為只同步處理屬於特定群組的帳戶。 | 您想要修改其使用者應該同步處理至 Azure AD DS 受控網域的群組清單。 | 請遵循這篇文章中的步驟來修改限域同步處理。 |

您可以使用 Azure 入口網站或 PowerShell 來設定限域的同步處理設定：

| 動作 | | |
|--|--|--|
| 建立 Azure AD DS 受控網域和設定限域同步處理 | [Azure 入口網站](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| 修改限域同步處理 | [Azure 入口網站](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| 停用範圍同步處理 | [Azure 入口網站](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> 變更同步處理的範圍會導致 Azure AD DS 受控網域重新同步處理所有資料。 您必須考量下列事項：
> 
>  * 當您變更 Azure AD DS 受控網域的同步處理範圍時，會進行完整重新同步處理。
>  * 已刪除 Azure AD DS 受控網域中不再需要的物件。 系統會在受控網域中建立新物件。
>  * 重新同步處理可能需要很長的時間才能完成。 同步處理時間取決於 Azure AD DS 受控網域和 Azure AD 目錄中的物件數目，例如使用者、群組和群組成員資格。 針對有數百個物件的大型目錄，重新同步處理可能需要幾天的時間。

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 入口網站啟用限域同步處理

若要在 Azure 入口網站中啟用已設定範圍的同步處理，請完成下列步驟：

1. 依照[教學課程來建立和設定 AZURE AD DS 實例](tutorial-create-instance-advanced.md)。 完成同步處理範圍以外的所有必要條件和部署步驟。
1. 選擇 [在同步處理步驟設定**範圍**]，然後選取要與 Azure AD DS 實例同步的 Azure AD 群組。

Azure AD DS 受控網域最多可能需要一小時的時間來完成部署。 在 Azure 入口網站中，您 Azure AD DS 受控網域的 [概觀]  頁面會顯示整個部署階段的目前狀態。

當 Azure 入口網站顯示 Azure AD DS 受控網域已完成佈建時，必須完成下列工作：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 在入口網站中選取您的 Azure AD DS 受控網域以設定 DNS。 在 [概觀]  視窗中，系統會提示您自動設定這些 DNS 設定。
* [啟用 Azure AD Domain Services 的密碼同步化](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds)，讓使用者可使用他們的公司認證來登入受控網域。

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 入口網站修改限域同步處理

若要修改使用者應該同步處理到 Azure AD DS 受控網域的群組清單，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure AD Domain Services**]。 選擇您的實例，例如*aaddscontoso.com*。
1. 從左側功能表中選取 [**同步**處理]。
1. 若要新增群組，請選擇頂端的 [ **+ 選取群組**]，然後選擇要新增的群組。
1. 若要從同步處理範圍中移除群組，請從目前同步處理的群組清單中選取它，然後選擇 [**移除群組**]。
1. 進行所有變更時，請選取 **[儲存同步處理範圍**]。

變更同步處理的範圍會導致 Azure AD DS 受控網域重新同步處理所有資料。 已刪除 Azure AD DS 受控網域中不再需要的物件，而重新同步處理可能需要很長的時間才能完成。

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 入口網站停用範圍同步處理

若要針對 Azure AD DS 受控網域停用以群組為基礎的限域同步處理，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure AD Domain Services**]。 選擇您的實例，例如*aaddscontoso.com*。
1. 從左側功能表中選取 [**同步**處理]。
1. 將同步處理範圍從 [**範圍**] 設定為 [**全部**]，然後選取 [**儲存同步處理範圍**]。

變更同步處理的範圍會導致 Azure AD DS 受控網域重新同步處理所有資料。 已刪除 Azure AD DS 受控網域中不再需要的物件，而重新同步處理可能需要很長的時間才能完成。

## <a name="powershell-script-for-scoped-synchronization"></a>適用于限域同步處理的 PowerShell 腳本

若要使用 PowerShell 設定限域同步處理，請先將下列腳本儲存`Select-GroupsToSync.ps1`至名為的檔案。 此腳本會將 Azure AD DS 設定為從 Azure AD 同步處理選取的群組。 屬於指定群組的所有使用者帳戶會同步處理至 Azure AD DS 受控網域。

此腳本用於本文中的其他步驟。

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

## <a name="enable-scoped-synchronization-using-powershell"></a>使用 PowerShell 啟用範圍同步處理

使用 PowerShell 來完成下列一組步驟。 請參考指示以[使用 PowerShell 啟用 Azure Active Directory Domain Services](powershell-create-instance.md)。 此文章中的一些步驟已經過些微修改來設定限域同步處理。

1. 完成本文中的下列工作，以使用 PowerShell 啟用 Azure AD DS。 在步驟停止，以實際建立受控網域。 您可以設定限域同步處理，以建立 Azure AD DS 受控網域。

   * [安裝必要的 PowerShell 模組](powershell-create-instance.md#prerequisites)。
   * [建立必要的服務主體，並 Azure AD 群組來進行系統管理存取](powershell-create-instance.md#create-required-azure-ad-resources)。
   * [建立支援的 Azure 資源，例如虛擬網路和子網](powershell-create-instance.md#create-supporting-azure-resources)。

1. 決定您想要從 Azure AD 同步處理的群組和使用者。 建立群組的顯示名稱清單，以同步處理 Azure AD DS。

1. 執行[上一節中的腳本](#powershell-script-for-scoped-synchronization)，並使用 *-groupsToAdd*參數來傳遞要同步處理的群組清單。

   > [!WARNING]
   > 您必須在群組清單中包含*AAD DC Administrators*群組，以進行限域同步處理。 如果您未包含此群組，Azure AD DS 受控網域將無法使用。

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. 現在，建立 Azure AD DS 受控網域，並啟用以群組為基礎的範圍同步處理。 在 *-Properties*參數中包含 *"filteredSync" = "Enabled"* 。

    設定您的 Azure 訂用帳戶識別碼，然後提供受控網域的名稱，例如 aaddscontoso.com  。 您可以使用 [Get-AzSubscriptionn][Get-AzSubscription] Cmdlet 取得訂用帳戶識別碼。 將 [資源組名]、[虛擬網路名稱] 和 [區域] 設定為先前步驟中所使用的值，以建立支援的 Azure 資源：

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

需要幾分鐘的時間來建立資源，並將控制權交還給 PowerShell 提示字元。 Azure AD DS 受控網域會繼續在背景中佈建，且最多可能需要一小時才能完成部署。 在 Azure 入口網站中，您 Azure AD DS 受控網域的 [概觀]  頁面會顯示整個部署階段的目前狀態。

當 Azure 入口網站顯示 Azure AD DS 受控網域已完成佈建時，必須完成下列工作：

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 在入口網站中選取您的 Azure AD DS 受控網域以設定 DNS。 在 [概觀]  視窗中，系統會提示您自動設定這些 DNS 設定。
* 如果您在支援可用性區域的區域中建立了 Azure AD DS 受控網域，請建立網路安全性群組，以限制 Azure AD DS 受控網域的虛擬網路流量。 已建立的 Azure 標準負載平衡器需要執行這些規則。 此網路安全性群組會保護 Azure AD DS，而且能讓受控網域正確運作。
    * 若要建立網路安全性群組和所需的規則，請在入口網站中選取您的 Azure AD DS 受控網域。 在 [概觀]  視窗中，系統會提示您自動設定這些網路安全性群組。
* [啟用 Azure AD Domain Services 的密碼同步化](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds)，讓使用者可使用他們的公司認證來登入受控網域。

## <a name="modify-scoped-synchronization-using-powershell"></a>使用 PowerShell 修改限域同步處理

若要修改使用者應該同步處理到 Azure AD DS 受控網域的群組清單，請重新執行[PowerShell 腳本](#powershell-script-for-scoped-synchronization)，並指定新的群組清單。 在下列範例中，要同步處理的群組不再包含*GroupName2*，現在包含*GroupName3*。

> [!WARNING]
> 您必須在群組清單中包含*AAD DC Administrators*群組，以進行限域同步處理。 如果您未包含此群組，Azure AD DS 受控網域將無法使用。

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

變更同步處理的範圍會導致 Azure AD DS 受控網域重新同步處理所有資料。 已刪除 Azure AD DS 受控網域中不再需要的物件，而重新同步處理可能需要很長的時間才能完成。

## <a name="disable-scoped-synchronization-using-powershell"></a>使用 PowerShell 停用範圍同步處理

若要針對 Azure AD DS 受控網域停用以群組為基礎的限域同步處理，請在 Azure AD DS 資源上設定 *"filteredSync" = "Disabled"* ，然後更新受控網域。 完成時，所有使用者和群組都會設定為從 Azure AD 同步處理。

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

變更同步處理的範圍會導致 Azure AD DS 受控網域重新同步處理所有資料。 已刪除 Azure AD DS 受控網域中不再需要的物件，而重新同步處理可能需要很長的時間才能完成。

## <a name="next-steps"></a>後續步驟

若要深入瞭解同步處理常式，請參閱[瞭解 Azure AD Domain Services 中的同步](synchronization.md)。

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
