---
title: 在 Azure 事件方格中使用 Azure AD 的安全 WebHook 傳遞
description: 說明如何使用 Azure 事件方格將事件傳遞至受 Azure Active Directory 保護的 HTTPS 端點
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: dd898fadf718509504d44df36572ac75050b02d6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371659"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>將事件發佈至 Azure Active Directory 受保護的端點 (機器翻譯)

本文說明如何利用 Azure Active Directory 來保護事件訂用帳戶與 webhook 端點之間的連線。 如需 Azure AD 應用程式和服務主體的概觀，請參閱 [Microsoft 身分識別平台 (v2.0) 概觀](../active-directory/develop/v2-overview.md)。

本文使用 Azure 入口網站進行示範，不過也可以使用 CLI、PowerShell 或 Sdk 來啟用此功能。


## <a name="create-an-azure-ad-application"></a>建立 Azure AD 應用程式

首先，建立受保護端點的 Azure AD 應用程式。 請參閱＜ https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview ＞。
    - 將受保護 API 設定為可由精靈應用程式呼叫。
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>啟用事件方格以使用您的 Azure AD 應用程式
本節說明如何啟用事件方格以使用您的 Azure AD 應用程式。 

> [!NOTE]
> 您必須是 [Azure AD 應用程式系統管理員角色](../active-directory/roles/permissions-reference.md#available-roles)的成員，才能執行此指令碼。

### <a name="connect-to-your-azure-tenant"></a>連接到您的 Azure 租使用者
首先，使用命令連接到您的 Azure 租使用者 `Connect-AzureAD` 。 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>建立 Microsoft EventGrid 服務主體
執行下列腳本，以建立 **EventGrid** 的服務主體（如果尚未存在）。 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>為您的應用程式建立角色   
執行下列腳本，為您的 Azure AD 應用程式建立角色。 在此範例中，角色名稱是： **AzureEventGridSecureWebhook**。 修改 PowerShell 腳本 `$myTenantId` ，以使用您的 Azure AD 租使用者識別碼，以及 `$myAzureADApplicationObjectId` 使用 Azure AD 應用程式的物件識別碼

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
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

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>將事件方格服務主體新增至角色    
現在，執行 `New-AzureADServiceAppRoleAssignment` 命令以將事件方格服務主體指派給您在上一個步驟中建立的角色。 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

執行下列命令來輸出您將使用下一個步驟的資訊。 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>設定事件訂用帳戶

在事件訂用帳戶的建立流程中，選取端點類型「webhook」。 當您指定端點 URI 之後，請按一下 [建立事件訂閱] 分頁頂端的 [其他功能] 索引標籤。

![選取端點類型 webhook](./media/secure-webhook-delivery/select-webhook.png)

在 [其他功能] 索引標籤中，選取 [使用 AAD 驗證] 的方塊，並設定租使用者識別碼和應用程式識別碼：

* 從腳本的輸出中複製 Azure AD 租使用者識別碼，然後在 [AAD 租使用者識別碼] 欄位中輸入該識別碼。
* 從腳本的輸出中複製 Azure AD 的應用程式識別碼，然後在 [AAD 應用程式識別碼] 欄位中輸入該識別碼。

    ![安全 Webhook 動作](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>後續步驟

* 如需關於監視事件傳遞的資訊，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 如需驗證金鑰的詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
