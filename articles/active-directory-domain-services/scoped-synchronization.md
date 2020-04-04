---
title: Azure AD 網域服務的作用域同步 |微軟文件
description: 瞭解如何從 Azure AD 設定範圍同步到 Azure 活動目錄域服務託管域
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
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654729"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>設定從 Azure AD 到 Azure 的目錄網域服務的動態同步

要提供身份驗證服務,Azure 活動目錄域服務 (Azure AD DS) 會同步 Azure AD 的使用者和組。 在混合環境中,來自本地活動目錄域服務 (AD DS) 環境的使用者和組可以先使用 Azure AD 連接同步到 Azure AD,然後同步到 Azure AD DS。

默認情況下,Azure AD 目錄中的所有使用者和組將同步到 Azure AD DS 託管域。 如果您有特定需求,則可以選擇僅同步一組定義的使用者。

本文介紹如何創建使用作用域同步的 Azure AD DS 託管域,然後更改或禁用作用域使用者集。

## <a name="scoped-synchronization-overview"></a>範圍同步概述

默認情況下,Azure AD 目錄中的所有使用者和組將同步到 Azure AD DS 託管域。 如果只有少數使用者需要訪問託管域,則只能同步這些使用者帳戶。 此作用域同步基於組。 設定基於組的範圍同步時,只有屬於您指定的組的使用者帳戶同步到 Azure AD DS 託管域。

下表概述了如何使用作用域同步:

| 目前狀態 | 所需狀態 | 必要設定 |
| --- | --- | --- |
| 現有託管域配置為同步所有使用者帳戶和組。 | 您希望僅同步屬於特定組的使用者帳戶。 | 不能從同步所有使用者更改為使用作用域同步。 [刪除現有的託管域](delete-aadds.md),然後按照本文中的步驟重新創建 Azure AD DS 託管域,並配置作用域同步。 |
| 沒有現有的託管域。 | 您想要建立新的受控網域，並只同步屬於特定群組的使用者帳戶。 | 按照本文中的步驟創建 Azure AD DS 託管域,並配置作用域同步。 |
| 現有託管域配置為僅同步屬於特定組的帳戶。 | 您希望修改其使用者應同步到 Azure AD DS 託管域的組的清單。 | 按照本文中的步驟修改作用域同步。 |

您可以使用 Azure 門戶或 PowerShell 設定作用網域同步設定:

| 動作 | | |
|--|--|--|
| 建立 Azure AD DS 託管域並配置作用區同步 | [Azure 入口網站](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| 變更範圍同步 | [Azure 入口網站](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| 關閉作用網域同步 | [Azure 入口網站](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> 更改同步範圍會導致 Azure AD DS 託管域重新同步所有資料。 您必須考量下列事項：
> 
>  * 更改 Azure AD DS 託管域的同步範圍時,將發生完全重新同步。
>  * Azure AD DS 託管域中不再需要的物件將被刪除。 系統會在受控網域中建立新物件。
>  * 重新同步可能需要很長時間才能完成。 同步時間取決於 Azure AD DS 託管域和 Azure AD 目錄中的使用者、組和組成員身份等物件的數量。 針對有數百個物件的大型目錄，重新同步處理可能需要幾天的時間。

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 門戶啟用作用區同步

要在 Azure 門戶中啟用作用網域同步,完成以下步驟:

1. 以[本教學建立與設定 Azure AD DS 實體 。](tutorial-create-instance-advanced.md) 完成同步範圍以外的所有先決條件和部署步驟。
1. 選擇同步步驟的 **「範圍」** 然後選擇 Azure AD 組以同步到 Azure AD DS 實例。

Azure AD DS 託管域最多可能需要一個小時才能完成部署。 在 Azure 門戶中,Azure AD DS 託管域的 **「概述」** 頁顯示整個部署階段的當前狀態。

當 Azure 門戶顯示 Azure AD DS 託管域已完成預配時,需要完成以下任務:

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 要配置 DNS,請在門戶中選擇 Azure AD DS 託管域。 在 **「概述」** 視窗中,系統會提示您自動配置這些 DNS 設定。
* [將密碼同步啟用到 Azure AD 功能服務](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds),以便最終使用者可以使用其公司認證登錄到託管域。

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 門戶修改作用區同步

要修改其使用者應同步到 Azure AD DS 託管域的群組清單,請完成以下步驟:

1. 在 Azure 門戶中,搜尋並選擇**Azure AD 網域服務**。 選擇實體,如*aaddscontoso.com*。
1. 從左側的功能表中選擇 **『同步**』。
1. 要添加組,請選擇 **「選擇**頂部的組」,然後選擇要添加的組。
1. 要從同步作用網域中刪除組,請從當前同步組清單中選擇該組,然後選擇 **「刪除群組**」。
1. 進行所有更改時,選擇 **「保存同步範圍**」。。

更改同步範圍會導致 Azure AD DS 託管域重新同步所有資料。 Azure AD DS 託管域中不再需要的物件將被刪除,並且重新同步可能需要很長時間才能完成。

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>使用 Azure 門戶關閉作用網域同步

要關閉 Azure AD DS 託管域的基於群組的範圍同步,請完成以下步驟:

1. 在 Azure 門戶中,搜尋並選擇**Azure AD 網域服務**。 選擇實體,如*aaddscontoso.com*。
1. 從左側的功能表中選擇 **『同步**』。
1. 將同步範圍從 **「範圍」** 設定為 **「全部**」,然後選擇 **「保存同步範圍**」。

更改同步範圍會導致 Azure AD DS 託管域重新同步所有資料。 Azure AD DS 託管域中不再需要的物件將被刪除,並且重新同步可能需要很長時間才能完成。

## <a name="powershell-script-for-scoped-synchronization"></a>作用域同步的 PowerShell 文稿

要使用 PowerShell 配置作用域同步,請先將以下文稿儲存`Select-GroupsToSync.ps1`到名為的檔案。 此文本設定 Azure AD DS 以同步 Azure AD 中選定的組。 屬於指定群組的所有使用者帳戶都同步到 Azure AD DS 託管域。

本文的其他步驟中使用此腳本。

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

## <a name="enable-scoped-synchronization-using-powershell"></a>使用 PowerShell 啟用範圍同步

使用 PowerShell 完成以下步驟集。 請參考指示以[使用 PowerShell 啟用 Azure Active Directory Domain Services](powershell-create-instance.md)。 此文章中的一些步驟已經過些微修改來設定限域同步處理。

1. 完成本文中的以下任務,以便使用 PowerShell 啟用 Azure AD DS。 停止執行步驟以實際創建託管域。 配置創建 Azure AD DS 託管域的作用域同步。

   * [安裝所需的 PowerShell 模組](powershell-create-instance.md#prerequisites)。
   * [為管理存取建立所需的服務主體與 Azure AD 群組](powershell-create-instance.md#create-required-azure-ad-resources)。
   * [創建支援 Azure 資源(如虛擬網路和子網](powershell-create-instance.md#create-supporting-azure-resources))。

1. 確定要從 Azure AD 同步的組和使用者。 列出要同步到 Azure AD DS 的組的顯示名稱。

1. [從上一節運行文稿](#powershell-script-for-scoped-synchronization),並使用 *-groupsToAdd*參數傳遞要同步的組清單。

   > [!WARNING]
   > 您必須在作用域同步的組清單中包括*AAD DC 管理員*群組。 如果不包括此組,則 Azure AD DS 託管域不可用。

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. 現在創建 Azure AD DS 託管域並啟用基於組的範圍同步。 在 *-屬性*參數中包括 *『篩選同步』和"已啟用」。*

    設定 Azure 訂閱 ID,然後為託管域提供名稱,如*aaddscontoso.com*。 您可以使用[取得-Az 訂閱][Get-AzSubscription]cmdlet 取得訂閱 ID。 將資源群組名稱、虛擬網路名稱和地區設定為前面步驟中使用的建立支援 Azure 資源的值:

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

建立資源並將控制項返回到 PowerShell 提示符需要幾分鐘時間。 Azure AD DS 託管域繼續在後台預配,最多可能需要一個小時才能完成部署。 在 Azure 門戶中,Azure AD DS 託管域的 **「概述」** 頁顯示整個部署階段的當前狀態。

當 Azure 門戶顯示 Azure AD DS 託管域已完成預配時,需要完成以下任務:

* 為虛擬網路更新 DNS 設定，讓虛擬機器可以找到受控網域來進行網域聯結或驗證。
    * 要配置 DNS,請在門戶中選擇 Azure AD DS 託管域。 在 **「概述」** 視窗中,系統會提示您自動配置這些 DNS 設定。
* 如果在支援可用性區域的區域中創建了 Azure AD DS 託管域,請創建網路安全組以限制 Azure AD DS 託管域的虛擬網路中的流量。 創建 Azure 標準負載均衡器,需要放置這些規則。 此網路安全組保護 Azure AD DS,並且託管域正常工作是必需的。
    * 要建立網路安全組和所需規則,請在門戶中選擇 Azure AD DS 託管域。 在 **「概述」** 視窗中,系統會提示您自動建立和設定網路安全組。
* [將密碼同步啟用到 Azure AD 功能服務](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds),以便最終使用者可以使用其公司認證登錄到託管域。

## <a name="modify-scoped-synchronization-using-powershell"></a>使用 PowerShell 變更作用區同步

要修改其使用者應同步到 Azure AD DS 託管域的組清單,請重新運行[PowerShell 文本](#powershell-script-for-scoped-synchronization)並指定新的組清單。 在下面的範例中,要同步的群組不再包括*GroupName2*,現在包括*GroupName3*。

> [!WARNING]
> 您必須在作用域同步的組清單中包括*AAD DC 管理員*群組。 如果不包括此組,則 Azure AD DS 託管域不可用。

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

更改同步範圍會導致 Azure AD DS 託管域重新同步所有資料。 Azure AD DS 託管域中不再需要的物件將被刪除,並且重新同步可能需要很長時間才能完成。

## <a name="disable-scoped-synchronization-using-powershell"></a>使用 PowerShell 關閉作用區同步

要關閉 Azure AD DS 託管域的基於組的範圍同步,請在 Azure AD DS 資源上設定 *「篩選同步」=「已禁用」* 然後更新託管域。 完成後,所有使用者和組都設置為從 Azure AD 同步。

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

更改同步範圍會導致 Azure AD DS 託管域重新同步所有資料。 Azure AD DS 託管域中不再需要的物件將被刪除,並且重新同步可能需要很長時間才能完成。

## <a name="next-steps"></a>後續步驟

要瞭解有關同步過程的更多,請參閱瞭解[Azure AD 網域服務中的同步](synchronization.md)。

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
