---
title: 在 Azure 入口網站中建立和管理動作群組
description: 了解如何在 Azure 入口網站中建立和管理動作群組。
author: dkamstra
ms.topic: conceptual
ms.date: 2/18/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6ba48f3c40e45afa02e03a7589e968cca723118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249512"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
動作群組是 Azure 訂用帳戶擁有者定義的通知喜好設定集合。 Azure 監視器和服務健康狀態警示使用動作群組來通知使用者警示已被觸發。 根據使用者的需求而定，不同的警示可能使用相同的動作群組或不同的動作群組。 一個訂用帳戶中最多可設定 2,000 個動作群組。

您配置了一個操作來通過電子郵件或短信通知某人，他們收到一個確認，指示他們已添加到操作組。

本文將說明如何在 Azure 入口網站中建立和管理動作群組。

每個動作是由下列屬性所組成：

* **名稱**：操作組中的唯一識別碼。  
* **操作類型**：執行的操作。 範例包括傳送語音電話、SMS、電子郵件或觸發各種類型的自動化動作。 請參閱本文稍後的類型。
* **詳細資訊**： 因*操作類型*而異的相應詳細資訊。

如需如何使用 Azure Resource Manager 範本設定動作群組的資訊，請參閱[動作群組 Resource Manager 範本](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站建立動作群組

1. 在[Azure 門戶](https://portal.azure.com)中，搜索 並選擇**監視器**。 "**監視器"** 窗格將所有監視設置和資料合併到一個視圖中。

1. 選取 [警示]****，然後選取 [管理動作]****。

    ![管理操作按鈕](./media/action-groups/manage-action-groups.png)
    
1. 選取 [新增動作群組]****，並填寫各欄位。

    ![「新增動作群組」命令](./media/action-groups/add-action-group.png)
    
1. 在 **"操作"組名稱**框中輸入名稱，並在 **"短名稱"** 框中輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

      ![「新增動作群組」對話方塊](./media/action-groups/action-group-define.png)

1. **訂閱**框自動填滿當前訂閱。 訂用帳戶是要儲存動作群組的位置。

1. 選擇保存操作組**的資源組**。

1. 定義動作清單。 為每個操作提供以下內容：

    1. **名稱**：輸入此動作的唯一識別碼。

    1. **動作類型**：選取電子郵件/簡訊/推送/語音、邏輯應用程式、Webhook、ITSM 或自動化 Runbook。

    1. **詳細資料**：根據動作類型，輸入電話號碼、電子郵件地址、Webhook URI、Azure 應用程式、ITSM 連線或自動化 Runbook。 針對 ITSM 動作，請額外指定 [工作項目]**** 與您 ITSM 工具所需的其他欄位。
    
    1. **通用警報架構**：您可以選擇啟用[通用警報架構](https://aka.ms/commonAlertSchemaDocs)，這提供了在 Azure 監視器中的所有警報服務中具有單個可擴展和統一的警報負載的優勢。

1. 選取 [確定]**** 來建立動作群組。

## <a name="manage-your-action-groups"></a>管理您的動作群組

創建操作組後，可以通過在 **"監視器"** 窗格中的 **"警報"** 登錄頁中選擇 **"管理操作**"來查看**操作組**。 選取您要管理的動作群組：

* 新增、編輯或移除動作。
* 刪除動作群組。

## <a name="action-specific-information"></a>動作特定資訊

> [!NOTE]
> 有關以下每個專案的數位限制，請參閱[訂閱服務限制。](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits)  

### <a name="automation-runbook"></a>自動化運行簿
有關 Runbook 有效負載的限制，請參閱[Azure 訂閱服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

操作組中的 Runbook 運算元量可能有限。 

### <a name="azure-app-push-notifications"></a>Azure 應用推送通知
操作組中的 Azure 應用運算元量可能有限。

### <a name="email"></a>電子郵件
下列電子郵件地址將寄出電子郵件。 請確定已適當設定您的電子郵件篩選
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

操作組中的電子郵件運算元量可能有限。 請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

### <a name="email-azure-resource-manager-role"></a>通過電子郵件發送 Azure 資源管理器角色
向訂閱角色的成員發送電子郵件。 電子郵件將僅發送給角色的**Azure AD 使用者**成員。 電子郵件不會發送到 Azure AD 組或服務主體。

操作組中的電子郵件運算元量可能有限。 請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

### <a name="function"></a>函式
調用[Azure 函數](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app)中的現有 HTTP 觸發器終結點。

操作組中的函數運算元量可能有限。

### <a name="itsm"></a>ITSM
ITSM 動作需要 ITSM 連線。 了解如何建立 [ITSM 連線](../../azure-monitor/platform/itsmc-overview.md)。

操作組中的 ITSM 運算元量可能有限。 

### <a name="logic-app"></a>邏輯應用程式
操作組中可能具有數量有限的邏輯應用操作。

### <a name="secure-webhook"></a>安全網路鉤
操作組 Webhook 操作使您能夠利用 Azure 活動目錄來保護操作組與受保護的 Web API（Webhook 終結點）之間的連接。 下面介紹了利用此功能的總體工作流。 有關 Azure AD 應用程式和服務主體的概述，請參閱[Microsoft 標識平臺 （v2.0） 概述](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)。

1. 為受保護的 Web API 創建 Azure AD 應用程式。 請參閱＜https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview＞。
    - 將受保護的 API 配置為由守護進程應用調用。
    
1. 使操作組能夠使用 Azure AD 應用程式。

    > [!NOTE]
    > 您必須是 Azure AD[應用程式管理員角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)的成員才能執行此腳本。
    
    - 修改 PowerShell 腳本的連接 AzureAD 調用以使用 Azure AD 租戶 ID。
    - 修改 PowerShell 腳本的變數$myAzureADApplicationObjectId以使用 Azure AD 應用程式的物件識別碼
    - 運行修改後的腳本。
    
1. 配置操作組安全 Webhook 操作。
    - 從腳本複製值$myApp.ObjectId，並在 Webhook 操作定義中的"應用程式物件 ID"欄位中輸入該值。
    
    ![安全 Webhook 操作](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>安全 Webhook 電源外殼腳本

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
有關其他重要資訊，請參閱[速率限制資訊和](./../../azure-monitor/platform/alerts-rate-limiting.md) [SMS 警報行為](../../azure-monitor/platform/alerts-sms-behavior.md)。

操作組中的 SMS 運算元量可能有限。  

### <a name="voice"></a>語音
請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

操作組中的語音運算元量可能有限。

### <a name="webhook"></a>Webhook
使用以下規則重試 Webhook。 返回以下 HTTP 狀態碼時，Webhook 調用最多重試 2 次：408、429、503、504 或 HTTP 終結點未回應。 第一次重試會在 10 秒後執行。 第二次重試會在 100 秒後執行。 兩次失敗後，任何操作組將不會調用終結點 30 分鐘。 

來源 IP 位址範圍
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

要接收有關這些 IP 位址更改的更新，我們建議您佈建服務運行狀況警報，該警報監視有關操作組服務的語音總機。

操作組中的 Webhook 運算元量可能有限。



## <a name="next-steps"></a>後續步驟
* 進一步了解 [SMS 警示行為](../../azure-monitor/platform/alerts-sms-behavior.md)。  
* 了解[活動記錄警示 Webhook 結構描述](../../azure-monitor/platform/activity-log-alerts-webhook.md)。  
* 深入了解 [ITSM 連接器](../../azure-monitor/platform/itsmc-overview.md)
* 深入了解警示的[速率限制](../../azure-monitor/platform/alerts-rate-limiting.md)。
* 取得[活動記錄警示的概觀](../../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。
