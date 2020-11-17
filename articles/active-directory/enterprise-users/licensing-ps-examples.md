---
title: 適用于群組授權的 PowerShell 和圖形範例-Azure AD |Microsoft Docs
description: Azure Active Directory 以群組為基礎之授權的 PowerShell + 圖形範例和案例
services: active-directory
keywords: Azure AD 授權
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 046a19d41364f7e4db3edcb011c8c65519924b3c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650320"
---
# <a name="powershell-and-graph-examples-for-group-based-licensing-in-azure-ad"></a>Azure AD 中以群組為基礎之授權的 PowerShell 和圖形範例

您可以透過 [Azure 入口網站](https://portal.azure.com)取得群組型授權的完整功能，而且目前有一些有用的工作可使用現有的 [MSOnline PowerShell Cmdlet](/powershell/module/msonline) 和 Microsoft Graph 來執行。 本文件會提供可行功能的範例。

> [!NOTE]
> 開始執行 Cmdlet 之前，請先確定您是透過執行 Cmdlet，先連接到您的組織 `Connect-MsolService` 。

> [!WARNING]
> 此程式碼是基於示範目的而提供的範例。 如果您想要在您的環境中使用它，請考慮在小規模或個別的測試組織中先測試它。 您可能需要調整程式碼以符合您環境的特定需求。

## <a name="assign-licenses-to-a-group"></a>將授權指派給群組

使用下列範例，利用 Microsoft Graph 將授權指派給群組：

```
POST https://graph.microsoft.com/v1.0/groups/1ad75eeb-7e5a-4367-a493-9214d90d54d0/assignLicense
Content-type: application/json
{
  "addLicenses": [
    {
      "disabledPlans": [ "11b0131d-43c8-4bbb-b2c8-e80f9a50834a" ],
      "skuId": "c7df2760-2c81-4ef7-b578-5b5392b571df"
    },
    {
      "disabledPlans": [ "a571ebcc-fqe0-4ca2-8c8c-7a284fd6c235" ],
      "skuId": "sb05e124f-c7cc-45a0-a6aa-8cf78c946968"
    }
  ],
  "removeLicenses": []
}

```
輸出：
```
HTTP/1.1 202 Accepted
Content-type: application/json
location: https://graph.microsoft.com/v2/d056d009-17b3-4106-8173-cd3978ada898/directoryObjects/1ad75eeb-7e5a-4367-a493-9214d90d54d0/Microsoft.DirectoryServices.Group

{
  "id": "1ad75eeb-7e5a-4367-a493-9214d90d54d0",
  "deletedDateTime": null,
  "classification": null,
  "createdDateTime": "2018-04-18T22:05:03Z",
  "securityEnabled": true,

}
```

## <a name="view-product-licenses-assigned-to-a-group"></a>檢視指派給群組的產品授權

[Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0) Cmdlet 可用來擷取群組物件並檢查「授權」屬性︰它會列出目前指派給群組的所有產品授權。

```powershell
(Get-MsolGroup -ObjectId 99c4216a-56de-42c4-a4ac-e411cd8c7c41).Licenses
| Select SkuPartNumber
```
輸出：
```
SkuPartNumber
-------------
ENTERPRISEPREMIUM
EMSPREMIUM
```

> [!NOTE]
> 此資料只會列出產品 (SKU) 資訊。 您無法列出授權中已停用的服務方案。

使用下列範例來取得 Microsoft Graph 中的相同資料。

```
GET https://graph.microsoft.com/v1.0/groups/99c4216a-56de-42c4-a4ac-e411cd8c7c41?$select=assignedLicenses
```
輸出：
```
HTTP/1.1 200 OK
{
  "value": [
{
  "assignedLicenses": [
     {
          "accountId":"f1b45b40-57df-41f7-9596-7f2313883635",
          "skuId":"c7df2760-2c81-4ef7-b578-5b5392b571df",
      "disabledPlans":[]
     },
     {
          "accountId":"f1b45b40-57df-41f7-9596-7f2313883635",
          "skuId":" b05e124f-c7cc-45a0-a6aa-8cf78c946968",
      "disabledPlans":[]
     },
  ],
}
  ]
}
```

## <a name="get-all-groups-with-licenses"></a>取得具有授權的所有群組

您可以執行下列命令來尋找所有已獲得授權的群組︰
```powershell
Get-MsolGroup -All | Where {$_.Licenses}
```
您可以顯示更多有關已指派之產品的詳細資料︰
```powershell
Get-MsolGroup -All | Where {$_.Licenses} | Select `
    ObjectId, `
    DisplayName, `
    @{Name="Licenses";Expression={$_.Licenses | Select -ExpandProperty SkuPartNumber}}
```

輸出：
```
ObjectId                             DisplayName              Licenses
--------                             -----------              --------
7023a314-6148-4d7b-b33f-6c775572879a EMS E5 – Licensed users  EMSPREMIUM
cf41f428-3b45-490b-b69f-a349c8a4c38e PowerBi - Licensed users POWER\_BI\_STANDARD
962f7189-59d9-4a29-983f-556ae56f19a5 O365 E3 - Licensed users ENTERPRISEPACK
c2652d63-9161-439b-b74e-fcd8228a7074 EMSandOffice             {ENTERPRISEPREMIUM,EMSPREMIUM}
```

## <a name="get-statistics-for-groups-with-licenses"></a>取得具有授權之群組的統計資料
您可以針對具有授權的群組提出基本統計資料報告。 下列範例中，指令碼列出使用者總人數、群組已對其指派授權的使用者人數，以及群組無法對其指派授權的使用者人數。

```powershell
#get all groups with licenses
Get-MsolGroup -All | Where {$_.Licenses}  | Foreach {
    $groupId = $_.ObjectId;
    $groupName = $_.DisplayName;
    $groupLicenses = $_.Licenses | Select -ExpandProperty SkuPartNumber
    $totalCount = 0;
    $licenseAssignedCount = 0;
    $licenseErrorCount = 0;

    Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full info about each user in the group
    Get-MsolUser -ObjectId {$_.ObjectId} |     Foreach {
        $user = $_;
        $totalCount++

        #check if any licenses are assigned via this group
        if($user.Licenses | ? {$_.GroupsAssigningLicense -ieq $groupId })
        {
            $licenseAssignedCount++
        }
        #check if user has any licenses that failed to be assigned from this group
        if ($user.IndirectLicenseErrors | ? {$_.ReferencedObjectId -ieq $groupId })
        {
            $licenseErrorCount++
        }     
    }

    #aggregate results for this group
    New-Object Object |
                    Add-Member -NotePropertyName GroupName -NotePropertyValue $groupName -PassThru |
                    Add-Member -NotePropertyName GroupId -NotePropertyValue $groupId -PassThru |
                    Add-Member -NotePropertyName GroupLicenses -NotePropertyValue $groupLicenses -PassThru |
                    Add-Member -NotePropertyName TotalUserCount -NotePropertyValue $totalCount -PassThru |
                    Add-Member -NotePropertyName LicensedUserCount -NotePropertyValue $licenseAssignedCount -PassThru |
                    Add-Member -NotePropertyName LicenseErrorCount -NotePropertyValue $licenseErrorCount -PassThru

    } | Format-Table
```


輸出：
```
GroupName         GroupId                              GroupLicenses       TotalUserCount LicensedUserCount LicenseErrorCount
---------         -------                              -------------       -------------- ----------------- -----------------
Dynamics Licen... 9160c903-9f91-4597-8f79-22b6c47eafbf AAD_PREMIUM_P2                   0                 0                 0
O365 E5 - base... 055dcca3-fb75-4398-a1b8-f8c6f4c24e65 ENTERPRISEPREMIUM                2                 2                 0
O365 E5 - extr... 6b14a1fe-c3a9-4786-9ee4-3a2bb54dcb8e ENTERPRISEPREMIUM                3                 3                 0
EMS E5 - all s... 7023a314-6148-4d7b-b33f-6c775572879a EMSPREMIUM                       2                 2                 0
PowerBi - Lice... cf41f428-3b45-490b-b69f-a349c8a4c38e POWER_BI_STANDARD                2                 2                 0
O365 E3 - all ... 962f7189-59d9-4a29-983f-556ae56f19a5 ENTERPRISEPACK                   2                 2                 0
O365 E5 - EXO     102fb8f4-bbe7-462b-83ff-2145e7cdd6ed ENTERPRISEPREMIUM                1                 1                 0
Access to Offi... 11151866-5419-4d93-9141-0603bbf78b42 STANDARDPACK                     4                 3                 1
```

## <a name="get-all-groups-with-license-errors"></a>取得具有授權錯誤的所有群組
若要尋找其所包含的使用者無法獲得授權指派的群組︰
```powershell
Get-MsolGroup -All -HasLicenseErrorsOnly $true
```
輸出：
```
ObjectId                             DisplayName             GroupType Description
--------                             -----------             --------- -----------
11151866-5419-4d93-9141-0603bbf78b42 Access to Office 365 E1 Security  Users who should have E1 licenses
```
使用下列命令從 Microsoft Graph 取得相同的資料
```
GET https://graph.microsoft.com/v1.0/groups?$filter=hasMembersWithLicenseErrors+eq+true
```
輸出：
```
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.Group",
      "objectType": "Group",
      "id": "11151866-5419-4d93-9141-0603bbf78b42",
      ... # other group properties.
    },
    {
      "odata.type": "Microsoft.DirectoryServices.Group",
      "objectType": "Group",
      "id": "c57cdc98-0dcd-4f90-a82f-c911b288bab9",
      ...
    },
    ... # other groups with license errors.
  ]
"odata.nextLink":"https://graph.microsoft.com/v1.0/ groups?$filter=hasMembersWithLicenseErrors+eq+true&$skipToken=<encodedPageToken>"
}
```


## <a name="get-all-users-with-license-errors-in-a-group"></a>取得群組中具有授權錯誤的所有使用者

若群組中包含某些授權相關錯誤，您現在可以列出這些錯誤所影響到的使用者。 使用者也可能有來自其他群組的錯誤。 不過，此範例所列出的結果僅限於與有問題之群組有關的錯誤，其方法是對使用者每個 **IndirectLicenseError** 項目的 **ReferencedObjectId** 屬性進行檢查。

```powershell
#a sample group with errors
$groupId = '11151866-5419-4d93-9141-0603bbf78b42'

#get all user members of the group
Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full information about user objects
    Get-MsolUser -ObjectId {$_.ObjectId} |
    #filter out users without license errors and users with license errors from other groups
    Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId} |
    #display id, name and error detail. Note: we are filtering out license errors from other groups
    Select ObjectId, `
           DisplayName, `
           @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId} | Select -ExpandProperty Error}}
```

輸出：

```powershell
ObjectId                             DisplayName      License Error
--------                             -----------      ------------
6d325baf-22b7-46fa-a2fc-a2500613ca15 Catherine Gibson MutuallyExclusiveViolation
```

使用下列內容從 Microsoft Graph 取得相同的資料：

```powershell
GET https://graph.microsoft.com/v1.0/groups/11151866-5419-4d93-9141-0603bbf78b42/membersWithLicenseErrors
```

輸出：
```powershell
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.User",
      "objectType": "User",
      "id": "6d325baf-22b7-46fa-a2fc-a2500613ca15",
      ... # other user properties.
    },
    ... # other users.
  ],
  "odata.nextLink":"https://graph.microsoft.com/v1.0/groups/11151866-5419-4d93-9141-0603bbf78b42/membersWithLicenseErrors?$skipToken=<encodedPageToken>"
}

```

## <a name="get-all-users-with-license-errors-in-the-entire-organization"></a>取得整個組織中具有授權錯誤的所有使用者

下列指令碼可用來取得具有一或多個群組之授權錯誤的所有使用者。 此指令碼會將每位使用者的每個授權錯誤各列印在一個資料列中，以方便您清楚識別每個錯誤的來源。

> [!NOTE]
> 此腳本會列舉組織中的所有使用者，對於大型組織而言可能不是最佳的。

```powershell
Get-MsolUser -All | Where {$_.IndirectLicenseErrors } | % {   
    $user = $_;
    $user.IndirectLicenseErrors | % {
            New-Object Object |
                Add-Member -NotePropertyName UserName -NotePropertyValue $user.DisplayName -PassThru |
                Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                Add-Member -NotePropertyName GroupId -NotePropertyValue $_.ReferencedObjectId -PassThru |
                Add-Member -NotePropertyName LicenseError -NotePropertyValue $_.Error -PassThru
        }
    }  
```

輸出：

```powershell
UserName         UserId                               GroupId                              LicenseError
--------         ------                               -------                              ------------
Anna Bergman     0d0fd16d-872d-4e87-b0fb-83c610db12bc 7946137d-b00d-4336-975e-b1b81b0666d0 MutuallyExclusiveViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 f2503e79-0edc-4253-8bed-3e158366466b CountViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 11151866-5419-4d93-9141-0603bbf78b42 MutuallyExclusiveViolation
Drew Fogarty     f2af28fc-db0b-4909-873d-ddd2ab1fd58c 1ebd5028-6092-41d0-9668-129a3c471332 MutuallyExclusiveViolation
```

以下是另一個版本的指令碼，它只會針對包含授權錯誤的群組進行搜尋。 這個指令碼可能更適合用於預期不會有多少群組有問題的情況。

```powershell
$groupIds = Get-MsolGroup -All -HasLicenseErrorsOnly $true
    foreach ($groupId in $groupIds) {
    Get-MsolGroupMember -All -GroupObjectId $groupId.ObjectID |
        Get-MsolUser -ObjectId {$_.ObjectId} |
        Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId.ObjectID} |
        Select DisplayName, `
               ObjectId, `
               @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId.ObjectID} | Select -ExpandProperty Error}}
 
    } 
``` 

## <a name="check-if-user-license-is-assigned-directly-or-inherited-from-a-group"></a>檢查使用者的授權是透過直接指派還是群組繼承而得到

對於使用者物件，您可以檢查特定產品授權是群組所指派還是直接指派的。

下列兩個函式範例可用來分析個別使用者的指派類型︰

```powershell
#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
            if ($license.GroupsAssigningLicense.Count -eq 0)
            {
                return $true
            }

            #If the collection contains the ID of the user object, this means the license is assigned directly
            #Note: the license may also be assigned through one or more groups in addition to being assigned directly
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                if ($assignmentSource -ieq $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a group
function UserHasLicenseAssignedFromGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                #Note: the license may also be assigned directly in addition to being inherited
                if ($assignmentSource -ine $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
```

此腳本會在組織中的每個使用者上執行這些函式，並使用 SKU 識別碼作為輸入-在此範例中，我們有興趣 *Enterprise Mobility + Security* 的授權，在我們的組織中是以 ID *contoso： EMS* 表示：

```powershell
#the license SKU we are interested in. use Get-MsolAccountSku to see a list of all identifiers in your organization
$skuId = "contoso:EMS"

#find all users that have the SKU license assigned
Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
    ObjectId, `
    @{Name="SkuId";Expression={$skuId}}, `
    @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
    @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
```

輸出：

```powershell
ObjectId                             SkuId       AssignedDirectly AssignedFromGroup
--------                             -----       ---------------- -----------------
157870f6-e050-4b3c-ad5e-0f0a377c8f4d contoso:EMS             True             False
1f3174e2-ee9d-49e9-b917-e8d84650f895 contoso:EMS            False              True
240622ac-b9b8-4d50-94e2-dad19a3bf4b5 contoso:EMS             True              True
```

Graph 沒有直接顯示結果的方式，但您可以從這個 API 看到它：

```powershell
GET https://graph.microsoft.com/v1.0/users/e61ff361-5baf-41f0-b2fd-380a6a5e406a?$select=licenseAssignmentStates
```

輸出：

```powershell
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.User",
      "objectType": "User",
      "id": "e61ff361-5baf-41f0-b2fd-380a6a5e406a",
      "licenseAssignmentState":[
        {
          "skuId": "157870f6-e050-4b3c-ad5e-0f0a377c8f4d",
          "disabledPlans":[],
          "assignedByGroup": null, # assigned directly.
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "1f3174e2-ee9d-49e9-b917-e8d84650f895",
          "disabledPlans":[],
          "assignedByGroup": "e61ff361-5baf-41f0-b2fd-380a6a5e406a", # assigned by this group.
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "240622ac-b9b8-4d50-94e2-dad19a3bf4b5", 
          "disabledPlans":[
            "e61ff361-5baf-41f0-b2fd-380a6a5e406a"
          ],
          "assignedByGroup": "e61ff361-5baf-41f0-b2fd-380a6a5e406a",
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "240622ac-b9b8-4d50-94e2-dad19a3bf4b5",
          "disabledPlans":[],
          "assignedByGroup": null, # It is the same license as the previous one. It means the license is assigned directly once and inherited from group as well.
          "state": " Active ",
          "error": " None"
        }
      ],
      ...
    }
  ],
}

```

## <a name="remove-direct-licenses-for-users-with-group-licenses"></a>移除具有群組授權之使用者的直接授權

此腳本的目的是要從已從群組繼承相同授權的使用者移除不必要的直接授權;例如， [轉換成群組型授權](licensing-groups-migrate-users.md)的一部分。
> [!NOTE]
> 請務必先驗證，要移除的直接授權所啟用的服務功能，沒有比繼承的授權所啟用的功能多。 否則，移除直接授權可能會停用使用者對服務和資料的存取權。 目前無法透過 PowerShell 來檢查哪些服務是透過繼承授權來啟用，哪些則是透過直接授權來啟用。 在指令碼中，我們會指定已知從群組所繼承而來的最低層級服務，然後就此進行檢查，以確定使用者不會意外失去服務的存取權。

```powershell
#BEGIN: Helper functions used by the script

#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
        if ($license.GroupsAssigningLicense.Count -eq 0)
        {
            return $true
        }

        #If the collection contains the ID of the user object, this means the license is assigned directly
        #Note: the license may also be assigned through one or more groups in addition to being assigned directly
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            if ($assignmentSource -ieq $user.ObjectId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a specific group
function UserHasLicenseAssignedFromThisGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
            #Note: the license may also be assigned directly in addition to being inherited
            if ($assignmentSource -ieq $groupId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}

#Returns the license object corresponding to the skuId. Returns NULL if not found
function GetUserLicense
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)
    #we look for the specific license SKU in all licenses assigned to the user
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

#produces a list of disabled service plan names for a set of plans we want to leave enabled
function GetDisabledPlansForSKU
{
    Param([string]$skuId, [string[]]$enabledPlans)

    $allPlans = Get-MsolAccountSku | where {$_.AccountSkuId -ieq $skuId} | Select -ExpandProperty ServiceStatus | Where {$_.ProvisioningStatus -ine "PendingActivation" -and $_.ServicePlan.TargetClass -ieq "User"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName
    $disabledPlans = $allPlans | Where {$enabledPlans -inotcontains $_}

    return $disabledPlans
}

function GetUnexpectedEnabledPlansForUser
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [string[]]$expectedDisabledPlans)

    $license = GetUserLicense $user $skuId

    $extraPlans = @();

    if($license -ne $null)
    {
        $userDisabledPlans = $license.ServiceStatus | where {$_.ProvisioningStatus -ieq "Disabled"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName

        $extraPlans = $expectedDisabledPlans | where {$userDisabledPlans -notcontains $_}
    }
    return $extraPlans
}
#END: helper functions

#BEGIN: executing the script
#the group to be processed
$groupId = "48ca647b-7e4d-41e5-aa66-40cab1e19101"

#license to be removed - Office 365 E3
$skuId = "contoso:ENTERPRISEPACK"

#minimum set of service plans we know are inherited from groups - we want to make sure that there aren't any users who have more services enabled
#which could mean that they may lose access after we remove direct licenses
$servicePlansFromGroups = ("EXCHANGE_S_ENTERPRISE", "SHAREPOINTENTERPRISE", "OFFICESUBSCRIPTION")

$expectedDisabledPlans = GetDisabledPlansForSKU $skuId $servicePlansFromGroups

#process all members in the group and get full info about each user in the group looping through group members. 
Get-MsolGroupMember -All -GroupObjectId $groupId | Get-MsolUser -ObjectId {$_.ObjectId} | Foreach {
        $user = $_;
        $operationResult = "";

        #check if Direct license exists on the user
        if (UserHasLicenseAssignedDirectly $user $skuId)
        {
            #check if the license is assigned from this group, as expected
            if (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
            {
                #check if there are any extra plans we didn't expect - we are being extra careful not to remove unexpected services
                $extraPlans = GetUnexpectedEnabledPlansForUser $user $skuId $expectedDisabledPlans
                if ($extraPlans.Count -gt 0)
                {
                    $operationResult = "User has extra plans that may be lost - license removal was skipped. Extra plans: $extraPlans"
                }
                else
                {
                    #remove the direct license from user
                    Set-MsolUserLicense -ObjectId $user.ObjectId -RemoveLicenses $skuId
                    $operationResult = "Removed direct license from user."   
                }

            }
            else
            {
                $operationResult = "User does not inherit this license from this group. License removal was skipped."
            }
        }
        else
        {
            $operationResult = "User has no direct license to remove. Skipping."
        }

        #format output
        New-Object Object |
                    Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                    Add-Member -NotePropertyName OperationResult -NotePropertyValue $operationResult -PassThru
    } | Format-Table
#END: executing the script
```

輸出：

```powershell
UserId                               OperationResult
------                               ---------------
7c7f860f-700a-462a-826c-f50633931362 Removed direct license from user.
0ddacdd5-0364-477d-9e4b-07eb6cdbc8ea User has extra plans that may be lost - license removal was skipped. Extra plans: SHAREPOINTWAC
aadbe4da-c4b5-4d84-800a-9400f31d7371 User has no direct license to remove. Skipping.
```
> [!NOTE]
> 執行上述腳本之前，請先根據您的測試環境，更新變數的值並將其設為 `$skuId` `$groupId` 目標，以移除直接授權。 

## <a name="next-steps"></a>後續步驟

若要深入了解透過群組管理授權的功能集，請參閱下列文章：

* [什麼是 Azure Active Directory 中以群組為基礎的授權？](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [將授權指派給 Azure Active Directory 中的群組](../users-groups-roles/licensing-groups-assign.md)
* [識別及解決 Azure Active Directory 中群組的授權問題](licensing-groups-resolve-problems.md)
* [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](licensing-groups-migrate-users.md)
* [如何使用 Azure Active Directory 中的群組型授權在產品授權之間移轉使用者](licensing-groups-change-licenses.md)
* [Azure Active Directory 群組型授權其他案例 (英文)](../users-groups-roles/licensing-group-advanced.md)
* [Azure Active Directory 群組型授權的 PowerShell 範例](licensing-ps-examples.md)
