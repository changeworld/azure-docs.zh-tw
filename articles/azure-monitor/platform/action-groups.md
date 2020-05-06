---
title: 在 Azure 入口網站中建立和管理動作群組
description: 了解如何在 Azure 入口網站中建立和管理動作群組。
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 5c8808450f8baa6d395ee9c24dbc59dfa919b66d
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801003"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
動作群組是 Azure 訂用帳戶擁有者定義的通知喜好設定集合。 Azure 監視器和服務健康狀態警示使用動作群組來通知使用者警示已被觸發。 根據使用者的需求而定，不同的警示可能使用相同的動作群組或不同的動作群組。 一個訂用帳戶中最多可設定 2,000 個動作群組。

您可以設定一個動作，透過電子郵件或 SMS 來通知某人，他們會收到確認已新增至動作群組的確認訊息。

本文將說明如何在 Azure 入口網站中建立和管理動作群組。

每個動作是由下列屬性所組成：

* **名稱**：動作群組內的唯一識別碼。  
* **動作類型**：執行的動作。 範例包括傳送語音電話、SMS、電子郵件或觸發各種類型的自動化動作。 請參閱本文稍後的類型。
* **詳細資料**：因*動作類型*而異的對應詳細資料。

如需如何使用 Azure Resource Manager 範本設定動作群組的資訊，請參閱[動作群組 Resource Manager 範本](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站建立動作群組

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [**監視**]。 [**監視**] 窗格會將您所有的監視設定和資料合併成一個視圖。

1. 選取 [警示]  ，然後選取 [管理動作]  。

    ![[管理動作] 按鈕](./media/action-groups/manage-action-groups.png)
    
1. 選取 [新增動作群組]****，並填寫各欄位。

    ![「新增動作群組」命令](./media/action-groups/add-action-group.png)
    
1. 在 [**動作組名**] 方塊中輸入名稱，然後在 [**簡短名稱**] 方塊中輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

      ![「新增動作群組」對話方塊](./media/action-groups/action-group-define.png)

1. [**訂**用帳戶] 方塊會與您目前的訂用帳戶會自動填入。 訂用帳戶是要儲存動作群組的位置。

1. 選取要在其中儲存動作群組的**資源群組**。

1. 定義動作清單。 針對每個動作提供下列各項：

    1. **名稱**：輸入此動作的唯一識別碼。

    1. **動作類型**：選取 [自動化 Runbook]、[Azure 函式]、[電子郵件 Azure Resource Manager 角色]、[電子郵件/SMS/推播/語音]、[ITSM]、邏輯應用程式、安全 Webhook、

    1. **詳細資料**：根據動作類型，輸入電話號碼、電子郵件地址、Webhook URI、Azure 應用程式、ITSM 連線或自動化 Runbook。 針對 ITSM 動作，請額外指定 [工作項目]**** 與您 ITSM 工具所需的其他欄位。
    
    1. **一般警示架構**：您可以選擇啟用[一般警示架構](https://aka.ms/commonAlertSchemaDocs)，這可讓您在 Azure 監視器中的所有警示服務上擁有單一可擴充且整合的警示承載。

1. 選取 [確定]**** 來建立動作群組。

## <a name="manage-your-action-groups"></a>管理您的動作群組

建立動作群組之後，您可以從 [**監視**] 窗格中的 [**警示**] 登陸頁面選取 [**管理動作**]，以查看**動作**群組。 選取您要管理的動作群組：

* 新增、編輯或移除動作。
* 刪除動作群組。

## <a name="action-specific-information"></a>動作特定資訊

> [!NOTE]
> 請參閱以下各專案的[監視訂用帳戶服務限制，以監控](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits)數值限制。  

### <a name="automation-runbook"></a>自動化 Runbook
如需 Runbook 承載的限制，請參閱[Azure 訂用帳戶服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

您在動作群組中可能會有有限數目的 Runbook 動作。 

### <a name="azure-app-push-notifications"></a>Azure 應用程式推播通知
您在動作群組中可能會有有限數目的 Azure 應用程式動作。

### <a name="email"></a>電子郵件
下列電子郵件地址將寄出電子郵件。 請確定已適當設定您的電子郵件篩選
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

動作群組中的電子郵件動作數目可能有限。 請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

### <a name="email-azure-resource-manager-role"></a>Azure Resource Manager 角色的電子郵件
傳送電子郵件給訂用帳戶角色的成員。 只會將電子郵件傳送給角色的**Azure AD 使用者**成員。 電子郵件不會傳送至 Azure AD 群組或服務主體。

動作群組中的電子郵件動作數目可能有限。 請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

### <a name="function"></a>函式
在[Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app)中呼叫現有的 HTTP 觸發程式端點。

動作群組中的函式動作數目可能有限。

### <a name="itsm"></a>ITSM
ITSM 動作需要 ITSM 連線。 了解如何建立 [ITSM 連線](../../azure-monitor/platform/itsmc-overview.md)。

您在動作群組中可能會有有限數目的 ITSM 動作。 

### <a name="logic-app"></a>邏輯應用程式
動作群組中的邏輯應用程式動作數目可能有限。

### <a name="secure-webhook"></a>安全 Webhook
[動作群組] [Webhook] 動作可讓您利用 Azure Active Directory 來保護您的動作群組與受保護 Web API （Webhook 端點）之間的連線。 下面將說明利用這項功能的整體工作流程。 如需 Azure AD 應用程式和服務主體的總覽，請參閱[Microsoft 身分識別平臺（v2.0）總覽](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)。

1. 為受保護的 Web API 建立 Azure AD 應用程式。 請參閱＜https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview＞。
    - 將受保護的 API 設定為可由背景程式應用程式呼叫。
    
1. 啟用 [動作群組] 以使用您的 Azure AD 應用程式。

    > [!NOTE]
    > 您必須是[Azure AD 應用程式系統管理員角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)的成員，才能執行此腳本。
    
    - 修改 PowerShell 腳本的 AzureAD 呼叫，以使用您的 Azure AD 租使用者識別碼。
    - 修改 PowerShell 腳本的變數 $myAzureADApplicationObjectId，以使用您 Azure AD 應用程式的物件識別碼
    - 執行修改過的腳本。
    
1. 設定動作群組安全 Webhook 動作。
    - 將腳本中的值 $myApp. ObjectId，並在 Webhook 動作定義的 [應用程式物件識別碼] 欄位中輸入。
    
    ![安全 Webhook 動作](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>安全 Webhook PowerShell 腳本

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

### <a name="sms"></a>SMS
如需其他重要資訊，請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)和[SMS 警示行為](../../azure-monitor/platform/alerts-sms-behavior.md)。 

動作群組中的 SMS 動作數目可能有限。

> [!NOTE]
> 如果 [Azure 入口網站動作群組] 使用者介面無法讓您選取您的國家/地區代碼，則您的國家/地區不支援 SMS。  如果您的國家/地區代碼無法使用，您可以投票，將您的國家/地區新增至[使用者心聲](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)。 在此同時，有個解決方法是讓您的動作群組向協力廠商 SMS 提供者呼叫 webhook，並在您的國家/地區支援。  

支援國家/地區的價格會列在[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)中。
  

### <a name="voice"></a>語音
如需其他重要行為，請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)一文。

動作群組中的語音動作數目可能有限。

> [!NOTE]
> 如果 [Azure 入口網站動作群組] 使用者介面無法讓您選取您的國家/地區代碼，則您的國家/地區不支援語音通話。 如果您的國家/地區代碼無法使用，您可以投票，將您的國家/地區新增至[使用者心聲](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)。  在此同時，有個解決方法是讓您的動作群組向協力廠商語音通話提供者呼叫 webhook，並在您的國家/地區中提供支援。  

支援國家/地區的價格會列在[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)中。

### <a name="webhook"></a>Webhook
Webhook 會使用下列規則來重試。 傳回下列 HTTP 狀態碼時，最多會重試2次 webhook 呼叫：408、429、503、504或 HTTP 端點沒有回應。 第一次重試會在 10 秒後執行。 第二次重試會在 100 秒後執行。 兩次失敗後，沒有任何動作群組會呼叫端點30分鐘。 

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

若要接收這些 IP 位址變更的相關更新，建議您設定服務健康狀態警示，以監視有關動作群組服務的語音總機。

在動作群組中，您可能會有有限數目的 Webhook 動作。



## <a name="next-steps"></a>後續步驟
* 進一步了解 [SMS 警示行為](../../azure-monitor/platform/alerts-sms-behavior.md)。  
* 了解[活動記錄警示 Webhook 結構描述](../../azure-monitor/platform/activity-log-alerts-webhook.md)。  
* 深入了解 [ITSM 連接器](../../azure-monitor/platform/itsmc-overview.md)
* 深入了解警示的[速率限制](../../azure-monitor/platform/alerts-rate-limiting.md)。
* 取得[活動記錄警示的概觀](../../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。
