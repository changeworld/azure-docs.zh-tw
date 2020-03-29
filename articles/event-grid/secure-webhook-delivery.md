---
title: 在 Azure 事件網格中使用 Azure AD 安全 WebHook 傳遞
description: 描述如何使用 Azure 事件網格將事件傳遞到受 Azure 活動目錄保護的 HTTPS 終結點
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931545"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>將事件發佈到 Azure 活動目錄保護終結點

本文介紹如何利用 Azure 活動目錄來保護事件訂閱和 Webhook 終結點之間的連接。 有關 Azure AD 應用程式和服務主體的概述，請參閱[Microsoft 標識平臺 （v2.0） 概述](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)。

本文使用 Azure 門戶進行演示，但也可以使用 CLI、PowerShell 或 SDK 啟用該功能。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>創建 Azure AD 應用程式

首先為受保護的終結點創建 Azure AD 應用程式。 請參閱＜https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview＞。
    - 將受保護的 API 配置為由守護進程應用調用。
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>啟用事件網格以使用 Azure AD 應用程式

使用下面的 PowerShell 腳本在 Azure AD 應用程式中創建角色和服務原則。 您將需要 Azure AD 應用程式中的租戶 ID 和物件識別碼：

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. 修改 PowerShell 腳本的$myTenantId以使用 Azure AD 租戶 ID。
1. 修改 PowerShell 腳本的$myAzureADApplicationObjectId以使用 Azure AD 應用程式的物件識別碼
1. 運行修改後的腳本。

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>配置事件訂閱

在事件訂閱的創建流中，選擇終結點類型"Web 掛鉤"。 為終結點 URI 提供後，按一下創建事件訂閱邊欄選項卡頂部的其他功能選項卡。

![選擇終結點類型 Webhook](./media/secure-webhook-delivery/select-webhook.png)

在"其他功能"選項卡中，選中"使用 AAD 身份驗證"核取方塊並配置租戶 ID 和應用程式 ID：

* 從腳本的輸出複製 Azure AD 租戶 ID，並在 AAD 租戶 ID 欄位中輸入它。
* 從腳本的輸出中複製 Azure AD 應用程式 ID，並在 AAD 應用程式 ID 欄位中輸入它。

    ![安全 Webhook 操作](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>後續步驟

* 如需關於監視事件傳遞的資訊，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 如需驗證金鑰的詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
