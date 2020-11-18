---
title: 在 Azure 入口網站中建立和管理動作群組
description: 了解如何在 Azure 入口網站中建立和管理動作群組。
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a5d685e49d941d7b6febbc220cdbfbcb631c4496
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94746358"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
動作群組是 Azure 訂用帳戶擁有者定義的通知喜好設定集合。 Azure 監視器和服務健康狀態警示使用動作群組來通知使用者警示已被觸發。 根據使用者的需求而定，不同的警示可能使用相同的動作群組或不同的動作群組。 一個訂用帳戶中最多可設定 2,000 個動作群組。

本文將說明如何在 Azure 入口網站中建立和管理動作群組。

每個動作是由下列屬性所組成：

* **輸入**：已執行的通知或動作。 範例包括傳送語音電話、SMS、電子郵件或觸發各種類型的自動化動作。 請參閱本文稍後的類型。
* **Name**：動作群組內的唯一識別碼。
* **詳細資料**：依 *類型* 而異的對應詳細資料。

如需如何使用 Azure Resource Manager 範本設定動作群組的資訊，請參閱[動作群組 Resource Manager 範本](./action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站建立動作群組

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [監視器]。 [監視器] 頁面會將您的所有監視設定與資料合併在一個檢視中。

1. 選取 [ **警示**]，然後選取 [ **管理動作**]。

    ![管理動作按鈕](./media/action-groups/manage-action-groups.png)
    
1. 選取 [ **新增動作群組**]，並在嚮導體驗中填寫相關的欄位。

    ![「新增動作群組」命令](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>設定基本動作群組設定

在 [ **專案詳細資料**] 底下：

選取用來儲存動作群組的 **訂** 用帳戶和 **資源群組** 。

在 [執行個體詳細資料] 下方：

1. 輸入 **動作組名**。

1. 輸入 **顯示名稱**。 使用這個群組傳送通知時，會使用顯示名稱來取代完整的動作組名。

      ![「新增動作群組」對話方塊](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>設定通知

1. 按一下 [ **下一步：通知] >** 按鈕移至 [ **通知** ] 索引標籤，或選取畫面頂端的 [ **通知** ] 索引標籤。

1. 定義觸發警示時要傳送的通知清單。 針對每個通知提供下列各項：

    a. **通知類型**：選取您想要傳送的通知類型。 可用的選項包括：
      * 電子郵件 Azure Resource Manager 角色-傳送電子郵件給指派給特定訂用帳戶層級的 ARM 角色的使用者。
      * 電子郵件/SMS/推送/語音-將這些通知類型傳送給特定收件者。
    
    b. **名稱**：輸入通知的唯一名稱。

    c. **詳細資料**：根據選取的通知類型，輸入電子郵件地址、電話號碼等等。
    
    d. **一般警示結構描述**：您可以選擇啟用 [一般警示結構描述](./alerts-common-schema.md)，這可讓您在 Azure 監視器中的所有警示服務上擁有單一可擴充且整合的警示承載。

    ![[通知] 索引標籤](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>設定動作

1. 按一下 [ **下一步：動作] >** 按鈕移至 [ **動作** ] 索引標籤，或選取畫面頂端的 [ **動作** ] 索引標籤。

1. 定義觸發警示時要觸發的動作清單。 針對每個動作提供下列各項：

    a. **動作類型**：選取自動化 Runbook、Azure FUNCTION、ITSM、邏輯應用程式、安全 Webhook、webhook。
    
    b. **名稱**：輸入動作的唯一名稱。

    c. **詳細資料**：根據動作類型，輸入 webhook URI、Azure 應用程式、ITSM 連線或自動化 runbook。 針對 ITSM 動作，請額外指定 [工作項目] 與您 ITSM 工具所需的其他欄位。
    
    d. **一般警示結構描述**：您可以選擇啟用 [一般警示結構描述](./alerts-common-schema.md)，這可讓您在 Azure 監視器中的所有警示服務上擁有單一可擴充且整合的警示承載。
    
    ![[動作] 索引標籤](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>建立動作群組

1. 您可以視需要瀏覽 [標記] 設定。 這可讓您將索引鍵/值組與您分類的動作群組建立關聯，而且是適用于任何 Azure 資源的功能。

    ![[標記] 索引標籤](./media/action-groups/action-group-4-tags.png)
    
1. 按一下 [檢閱 + 建立] 以檢閱設定。 這會快速驗證您的輸入，以確保選取所有必要的欄位。 如果發生問題，則會在這裡回報。 完成設定之後，請按一下 [建立] 以布 **建** 動作群組。
    
    ![[審核 + 建立] 索引標籤](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> 當您設定動作以透過電子郵件或 SMS 通知某人時，他們會收到確認已新增至動作群組的確認。

## <a name="manage-your-action-groups"></a>管理您的動作群組

建立動作群組之後，您可以從 [監視器] 窗格的 [警示] 登陸頁面中選取 [管理動作]，以檢視 [動作群組]。 選取您要管理的動作群組：

* 新增、編輯或移除動作。
* 刪除動作群組。

## <a name="action-specific-information"></a>動作特定資訊

> [!NOTE]
> 如需以下每個項目的數值限制，請參閱[監視的訂用帳戶服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits)。  

### <a name="automation-runbook"></a>自動化 Runbook
請參閱 [Azure 訂用帳戶服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)，了解 Runbook 承載的限制。

您在動作群組中可以有有限數量的 Runbook 動作。 

### <a name="azure-app-push-notifications"></a>Azure 應用程式推播通知
您在動作群組中可以有有限數量的 Azure 應用程式動作。

### <a name="email"></a>電子郵件
下列電子郵件地址將寄出電子郵件。 請確定已適當設定您的電子郵件篩選
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

您在動作群組中可以有有限數量的電子郵件動作。 請參閱[速率限制資訊](./alerts-rate-limiting.md)一文。

### <a name="email-azure-resource-manager-role"></a>寄送電子郵件給 Azure Resource Manager 角色
傳送電子郵件給訂用帳戶角色的成員。 只會將電子郵件傳送給 **Azure AD 使用者** 角色成員。 不會將電子郵件傳送給 Azure AD 群組或服務主體。

通知電子郵件只會傳送到 *主要電子郵件* 位址。

您在動作群組中可以有有限數量的電子郵件動作。 請參閱[速率限制資訊](./alerts-rate-limiting.md)一文。

### <a name="function"></a>函式
在 [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app) 中呼叫現有的 HTTP 觸發程序端點。

您在動作群組中可以有有限數量的 Function 動作。

### <a name="itsm"></a>ITSM
ITSM 動作需要 ITSM 連線。 了解如何建立 [ITSM 連線](./itsmc-overview.md)。

您在動作群組中可以有有限數量的 ITSM 動作。 

### <a name="logic-app"></a>邏輯應用程式
您在動作群組中可以有有限數量的邏輯應用程式動作。

### <a name="secure-webhook"></a>安全 Webhook
動作群組 Webhook 動作可讓您利用 Azure Active Directory 來保護您的動作群組與受保護 Web API (Webhook 端點) 之間的連線。 利用這項功能的整體工作流程說明如下。 如需 Azure AD 應用程式和服務主體的概觀，請參閱 [Microsoft 身分識別平台 (v2.0) 概觀](../../active-directory/develop/v2-overview.md)。

1. 為受保護 Web API 建立 Azure AD 應用程式。 請參閱 [受保護的 WEB API：應用程式註冊](../../active-directory/develop/scenario-protected-web-api-app-registration.md)。
    - 設定要 [由 daemon 應用程式呼叫](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)的受保護 API。
    
2. 啟用 [動作群組] 以使用您的 Azure AD 應用程式。

    > [!NOTE]
    > 您必須是 [Azure AD 應用程式系統管理員角色](../../active-directory/roles/permissions-reference.md#available-roles)的成員，才能執行此指令碼。
    
    - 修改 PowerShell 指令碼的 Connect-AzureAD 呼叫，以使用您的 Azure AD 租用戶識別碼。
    - 修改 PowerShell 腳本的變數 $myAzureADApplicationObjectId，以使用 Azure AD 應用程式的物件識別碼。
    - 執行修改過的指令碼。
    
3. 設定動作群組安全 Webhook 動作。
    - 從指令碼複製值 $myApp.ObjectId，並在 Webhook 動作定義的 [應用程式物件識別碼] 欄位中輸入。
    
    ![安全 Webhook 動作](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>安全 Webhook PowerShell 指令碼

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
如需其他重要資訊，請參閱[速率限制資訊](./alerts-rate-limiting.md)和 [SMS 警示行為](./alerts-sms-behavior.md)。 

您在動作群組中可以有有限數量的 SMS 動作。

> [!NOTE]
> 如果 Azure 入口網站動作群組使用者介面無法讓您選取您的國家/區域代碼，則您的國家/區域不支援 SMS。  如果您的國家/區域代碼無法使用，您可以投票，將您的國家/區域新增至 [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)。 在此同時，有個因應措施是讓您的動作群組向第三方 SMS 提供者 (可在您的國家/區域提供支援) 呼叫 Webhook。  

支援的國家/區域定價會列在 [Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)。
  

### <a name="voice"></a>語音
針對其他重要行為，請參閱[速率限制資訊](./alerts-rate-limiting.md)一文。

您在動作群組中可以有有限數量的語音動作。

> [!NOTE]
> 如果 Azure 入口網站動作群組使用者介面無法讓您選取您的國家/區域代碼，則您的國家/區域不支援語音電話。 如果您的國家/區域代碼無法使用，您可以投票，將您的國家/區域新增至 [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)。  在此同時，有個因應措施是讓您的動作群組向第三方語音電話提供者 (可在您的國家/區域提供支援) 呼叫 Webhook。  

支援的國家/區域定價會列在 [Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="webhook"></a>Webhook
Webhook 會使用下列規則來處理
- 最多嘗試3次 webhook 呼叫。
- 如果在超時期間內未收到回應，或傳回下列其中一個 HTTP 狀態碼，則會重試呼叫：408、429、503或504。
- 第一個呼叫會等待10秒進行回應。
- 第二次和第三次嘗試會等待30秒進行回應。
- 在3次嘗試呼叫 webhook 失敗之後，沒有任何動作群組會呼叫端點15分鐘。

來源 IP 位址範圍：

 - 13.66.60.119/32
 - 13.66.143.220/30
 - 13.66.202.14/32
 - 13.66.248.225/32
 - 13.66.249.211/32
 - 13.67.10.124/30
 - 13.69.109.132/30
 - 13.71.199.112/30
 - 13.77.53.216/30
 - 13.77.172.102/32
 - 13.77.183.209/32
 - 13.78.109.156/30
 - 13.84.49.247/32
 - 13.84.51.172/32
 - 13.84.52.58/32
 - 13.86.221.220/30
 - 13.106.38.142/32
 - 13.106.38.148/32
 - 13.106.54.3/32
 - 13.106.54.19/32
 - 13.106.57.181/32
 - 13.106.57.196/31
 - 20.38.149.132/30
 - 20.42.64.36/30
 - 20.43.121.124/30
 - 20.44.17.220/30
 - 20.45.123.236/30
 - 20.72.27.152/30
 - 20.150.172.228/30
 - 20.192.238.124/30
 - 20.193.202.4/30
 - 40.68.195.137/32
 - 40.68.201.58/32
 - 40.68.201.65/32
 - 40.68.201.206/32
 - 40.68.201.211/32
 - 40.68.204.18/32
 - 40.115.37.106/32
 - 40.121.219.215/32
 - 40.121.221.62/32
 - 40.121.222.201/32
 - 40.121.223.186/32
 - 51.104.9.100/30
 - 52.183.20.244/32
 - 52.183.31.0/32
 - 52.183.94.59/32
 - 52.184.145.166/32
 - 191.233.50.4/30
 - 191.233.207.64/26
 - 2603:1000:4:402::178/125
 - 2603:1000:104:402::178/125
 - 2603:1010:6:402::178/125
 - 2603:1010:101:402::178/125
 - 2603:1010:304:402::178/125
 - 2603:1010:404:402::178/125
 - 2603:1020:5:402::178/125
 - 2603:1020:206:402::178/125
 - 2603:1020:305:402::178/125
 - 2603:1020:405:402::178/125
 - 2603:1020:605:402::178/125
 - 2603:1020:705:402::178/125
 - 2603:1020:805:402::178/125
 - 2603:1020:905:402::178/125
 - 2603：1020： a04：402：： 178/125
 - 2603：1020： b04：402：： 178/125
 - 2603：1020： c04：402：： 178/125
 - 2603：1020： d04：402：： 178/125
 - 2603：1020： e04：402：： 178/125
 - 2603：1020： f04：402：： 178/125
 - 2603：1020：1004：800：： f8/125
 - 2603:1020:1104:400::178/125
 - 2603：1030： f:400：： 978/125
 - 2603:1030:10:402::178/125
 - 2603:1030:104:402::178/125
 - 2603：1030：107：400：： f0/125
 - 2603:1030:210:402::178/125
 - 2603：1030：40b：400：： 978/125
 - 2603：1030：40c：402：： 178/125
 - 2603：1030：504：802：： f8/125
 - 2603:1030:608:402::178/125
 - 2603:1030:807:402::178/125
 - 2603：1030： a07：402：： 8f8/125
 - 2603：1030： b04：402：： 178/125
 - 2603：1030： c06：400：： 978/125
 - 2603：1030： f05：402：： 178/125
 - 2603:1030:1005:402::178/125
 - 2603:1040:5:402::178/125
 - 2603:1040:207:402::178/125
 - 2603:1040:407:402::178/125
 - 2603:1040:606:402::178/125
 - 2603:1040:806:402::178/125
 - 2603:1040:904:402::178/125
 - 2603：1040： a06：402：： 178/125
 - 2603：1040： b04：402：： 178/125
 - 2603：1040： c06：402：： 178/125
 - 2603：1040： d04：800：： f8/125
 - 2603：1040： f05：402：： 178/125
 - 2603:1040:1104:400::178/125
 - 2603:1050:6:402::178/125
 - 2603：1050：403：400：： 1f8/125

若要接收與這些 IP 位址變更有關的更新，建議您設定服務健康狀態警示，這會監視動作群組服務的資訊通知。

您在動作群組中可以有有限數量的 Webhook 動作。

對來源 IP 位址進行頻繁的更新，在 Webhook 中可能相當耗時。 使用 **服務** 標籤進行 *ActionGroup* 有助於將經常更新 IP 位址的複雜性降至最低。 上述共用的來源 IP 位址範圍前置詞是由 Microsoft 自動管理，由 **服務標記** 所包含。

#### <a name="service-tag"></a>服務標記
服務標籤代表來自指定 Azure 服務的一組 IP 位址前置詞。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標籤，將頻繁更新 ActionGroup 的網路安全性規則的複雜性降至最低。

1. 在 Azure 入口網站 [搜尋 *網路安全性群組*] 下的 [Azure 服務] 下。
2. 按一下 [ **新增** ] 並建立網路安全性群組。

   1. 新增資源組名，然後輸入 *實例詳細資料*。
   1. 按一下 [ **審核 + 建立** ]，然後按一下 [ *建立*]。
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="如何建立網路安全性群組的範例。"border="true":::

3. 移至 [資源群組]，然後按一下您已建立的 *網路安全性群組* 。

    1. 選取 [ *輸入安全性規則*]。
    1. 按一下 [新增]。
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="如何新增服務標記的範例。"border="true":::

4. 新視窗會在右窗格中開啟。
    1.  選取來源： **服務標記**
    1.  來源服務標記： **ActionGroup**
    1.  按一下 [新增] 。
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="如何新增服務標記的範例。"border="true":::

## <a name="next-steps"></a>後續步驟
* 進一步了解 [SMS 警示行為](./alerts-sms-behavior.md)。  
* 了解[活動記錄警示 Webhook 結構描述](./activity-log-alerts-webhook.md)。  
* 深入瞭解 [ITSM Connector](./itsmc-overview.md)。
* 深入了解警示的[速率限制](./alerts-rate-limiting.md)。
* 取得[活動記錄警示的概觀](./alerts-overview.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](../../service-health/alerts-activity-log-service-notifications-portal.md)。
