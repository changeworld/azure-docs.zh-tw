---
title: Azure 自動化 Windows 虛擬桌面 (傳統) 調整工作階段主機
description: 如何使用 Azure 自動化自動調整 Windows 虛擬桌面 (傳統) 工作階段主機。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd14af6c95654708f339f4a68cd333d0e3162553
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078175"
---
# <a name="scale-windows-virtual-desktop-classic-session-hosts-using-azure-automation"></a>使用 Azure 自動化調整 Windows 虛擬桌面 (傳統) 工作階段主機

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。

您可以藉由調整虛擬機器 (VM) 來降低 Windows 虛擬桌面部署總成本。 這表示在離峰使用時間將工作階段主機 VM 關閉並解除配置，然後在尖峰時間將 VM 恢復啟動並重新配置。

在本文中，您將瞭解使用 Azure 自動化帳戶和 Azure 邏輯應用程式所建立的調整工具，此工具會在您的 Windows 虛擬桌面環境中自動調整工作階段主機 Vm 的規模。 若要了解如何使用調整工具，請直接跳至[必要條件](#prerequisites)。

## <a name="how-the-scaling-tool-works"></a>調整工具的運作方式

調整工具為想要最佳化其工作階段主機 VM 成本的客戶提供低成本的自動化選項。

使用調整工具可以：

- 根據尖峰和離峰上班時間來排程要啟動和停止的 VM。
- 根據每個 CPU 核心的工作階段數目來擴增 VM。
- 在離峰時間縮減 VM，讓維持在執行狀態的工作階段主機 VM 數目最少。

調整工具會使用 Azure 自動化帳戶、PowerShell runbook、webhook 和 Azure 邏輯應用程式的組合來運作。 當工具執行時，Azure 邏輯應用程式會呼叫 webhook 來啟動 Azure 自動化 runbook。 接著 Runbook 會建立作業。

在尖峰使用時間，作業會檢查工作階段的目前數目，以及每個主機集區中目前執行中工作階段主機的 VM 容量。 它會使用此資訊來計算執行中的工作階段主機 Vm 是否可根據為**CreateOrUpdateAzLogicApp.ps1**檔定義的*SessionThresholdPerCPU*參數，支援現有的會話。 如果工作階段主機 VM 無法支援現有的工作階段，此作業會啟動主機集區中的其他工作階段主機 VM。

>[!NOTE]
>*SessionThresholdPerCPU* 不會限制 VM 上的工作階段數目。 此參數只會決定何時需要啟動新的 VM 以對連線進行負載平衡。 若要限制工作階段數目，您必須遵循 [RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) 的指示，以相應地設定 *MaxSessionLimit* 參數。

在離峰使用時間期間，作業會根據 *MinimumNumberOfRDSH* 參數判斷應關閉多少工作階段主機 vm。 如果您將 *LimitSecondsToForceLogOffUser* 參數設定為非零的正值，則作業會將工作階段主機 vm 設定為清空模式，以防止新的會話連接到主機。 作業接著會通知任何目前已登入的使用者儲存其工作、等候設定的時間，然後強制使用者登出。當工作階段主機 VM 上的所有使用者會話都已登出之後，作業將會關閉 VM。 VM 關機之後，作業將會重設其工作階段主機清空模式。

>[!NOTE]
>如果您手動將工作階段主機 VM 設定為清空模式，此作業將不會管理工作階段主機 VM。 如果工作階段主機 VM 正在執行並設定為清空模式，則會將其視為無法使用，這會讓作業啟動額外的 Vm 來處理負載。 建議您在手動將任何 Azure Vm 設定為清空模式之前，先標記這些 Vm。 當您稍後建立 Azure 邏輯應用程式排程器時，可以使用 *MaintenanceTagName* 參數來命名標記。 標記可協助您區別這些 Vm 與調整工具所管理的 Vm。 設定維護標記也可防止調整工具變更 VM，直到您移除標記為止。

如果您將 *LimitSecondsToForceLogOffUser* 參數設定為零，則工作會允許指定群組原則中的會話設定，來處理登出使用者會話的作業。 若要查看這些群組原則，請**Computer Configuration**移至系統管理範本 Windows 元件的電腦設定  >  **原則**  >  **Administrative Templates**  >  **Windows Components**  >  **遠端桌面服務**  >  **遠端桌面工作階段主機**  >  **會話時間限制**。 如果工作階段主機 VM 上有任何使用中會話，此作業會讓工作階段主機 VM 繼續執行。 如果沒有任何使用中的會話，此作業將會關閉工作階段主機 VM。

在任何時間，此作業也會將主機集區的 *MaxSessionLimit* 納入考慮，以判斷目前的會話數目是否超過最大容量的90%。 如果是，此作業將會啟動額外的工作階段主機 Vm。

作業會根據設定的週期間隔定期執行。 您可以根據您的 Windows 虛擬桌面環境大小來變更此間隔，但請記住，啟動和關閉 Vm 可能需要一些時間，因此請記得考慮延遲。 建議您將週期間隔設定為每 15 分鐘一次。

不過，此工具有下列限制：

- 此解決方案只適用于集區多會話工作階段主機 Vm。
- 此解決方案會管理任何區域中的 Vm，但只能在與您的 Azure 自動化帳戶和 Azure 邏輯應用程式相同的訂用帳戶中使用。
- Runbook 中作業的執行時間上限是3小時。 如果啟動或停止主機集區中的 Vm 所花費的時間超過此，則作業將會失敗。 如需詳細資訊，請參閱 [共用資源](../../automation/automation-runbook-execution.md#fair-share)。

>[!NOTE]
>調整工具會控制目前正在調整的主機集區的負載平衡模式。 此工具會針對尖峰和離峰時段使用廣度優先的負載平衡模式。

## <a name="prerequisites"></a>Prerequisites

開始設定調整工具之前，請先確定您已備妥下列項目：

- [Windows 虛擬桌面租用戶與主機集區](create-host-pools-arm-template.md)
- 透過 Windows 虛擬桌面服務設定和註冊的工作階段主機集區 VM
- 對 Azure 訂閱具有[參與者存取權](../../role-based-access-control/role-assignments-portal.md)的使用者

您用來部署工具的機器必須具備：

- Windows PowerShell 5.1 或更新版本
- Microsoft Az PowerShell 模組

如果一切就緒，讓我們開始吧。

## <a name="create-or-update-an-azure-automation-account"></a>建立或更新 Azure 自動化帳戶

>[!NOTE]
>如果您已經有 Azure 自動化的帳戶，且 runbook 執行較舊版本的調整腳本，您只需要遵循下列指示來確認它已更新。

首先，您需要 Azure 自動化帳戶來執行 PowerShell Runbook。 此區段描述的程式是有效的，即使您有想要用來設定 PowerShell runbook 的現有 Azure 自動化帳戶。 以下是設定方式：

1. 開啟 Windows PowerShell。

2. 執行下列 Cmdlet 來登入您的 Azure 帳戶。

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >您的帳戶必須具有您要部署調整工具之 Azure 訂用帳戶的參與者許可權。

3. 執行下列 Cmdlet 來下載用來建立 Azure 自動化帳戶的指令碼：

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. 執行下列 Cmdlet 來執行腳本，並建立 Azure 自動化帳戶。 您可以填入參數的值，或將它們加上批註以使用其預設值。

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. Cmdlet 的輸出將包含 Webhook URI。 請務必保留 URI 的記錄，因為當您設定 Azure 邏輯應用程式的執行排程時，將會使用它做為參數。

6. 如果您指定了 Log Analytics 的參數 **WorkspaceName** ，則 Cmdlet 的輸出也會包含 Log Analytics 工作區識別碼和其主要金鑰。 請務必記住 URI，因為當您設定 Azure 邏輯應用程式的執行排程時，稍後將需要再次使用它作為參數。

7. 設定 Azure 自動化帳戶之後，請登入您的 Azure 訂閱，並檢查以確定您的 Azure 自動化帳戶和相關的 Runbook 已出現在您指定的資源群組中，如下圖所示：

    >[!div class="mx-imgBorder"]
    >![Azure 總覽頁面的影像，其中顯示新建立的 Azure 自動化帳戶和 runbook。](media/automation-account.png)

    若要檢查您的 Webhook 是否在其應所在的位置，請選取您的 Runbook 名稱。 接下來，移至 Runbook 的 [資源] 區段，然後選取 [Webhook]。

## <a name="create-an-azure-automation-run-as-account"></a>建立 Azure 自動化執行身分帳戶

現在您已經有 Azure 自動化帳戶，您也需要建立 Azure 自動化執行帳戶（如果您還沒有的話）。 此帳戶可讓工具存取您的 Azure 資源。

[Azure 自動化執行身份帳戶](../../automation/manage-runas-account.md)提供在 azure 中使用 azure Cmdlet 來管理資源的驗證。 當您建立執行帳戶時，它會在 Azure Active Directory 中建立新的服務主體使用者，並將參與者角色指派給訂用帳戶層級的服務主體使用者。 Azure 執行身分帳戶是使用憑證和服務主體名稱安全地進行驗證的絕佳方式，而不需要在認證物件中儲存使用者名稱和密碼。 若要深入瞭解執行身份帳戶驗證，請參閱 [限制執行帳戶的許可權](../../automation/manage-runas-account.md#limit-run-as-account-permissions)。

任何屬於訂用帳戶管理員角色成員和訂用帳戶共同管理員的使用者，都可以建立執行身分帳戶。

若要在您的 Azure 自動化帳戶中建立執行身份帳戶：

1. 在 Azure 入口網站中，選取 [所有服務]  。 在資源清單中，輸入並選取 [ **自動化帳戶**]。

2. 在 [ **自動化帳戶** ] 頁面上，選取您 Azure 自動化帳戶的名稱。

3. 在視窗左側的窗格中，選取 [**帳戶設定**] 區段底下的 [**執行身份帳戶**]。

4. 選取 [ **Azure 執行帳戶**]。 當 [ **新增 Azure 執行身份帳戶** ] 窗格出現時，請參閱總覽資訊，然後選取 [ **建立** ] 以開始帳戶建立程式。

5. 請稍候幾分鐘，讓 Azure 建立執行身分帳戶。 您可以在 [通知] 下方的功能表中追蹤建立進度。

6. 當程式完成時，它會在指定的 Azure 自動化帳戶中建立名為 **AzureRunAsConnection** 的資產。 選取 [ **Azure 執行帳戶**]。 連線資產會保存應用程式識別碼、租用戶識別碼、訂閱識別碼，以及憑證指紋。 請記下應用程式識別碼，因為您稍後會用到它。 您也可以在 [ **連接** ] 頁面上找到相同的資訊。 若要移至此頁面，請在視窗左側的窗格 **中選取 [** **共用資源** ] 區段底下的 [連線]，然後按一下名為 **AzureRunAsConnection**的連線資產。

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>在 Windows 虛擬桌面中建立角色指派

接下來，您必須建立角色指派，讓 **AzureRunAsConnection** 可以與 Windows 虛擬桌面互動。 請務必使用 PowerShell 以透過有權限建立角色指派的帳戶登入。

首先，下載並匯入 [Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 執行下列 PowerShell Cmdlet 以連線至 Windows 虛擬桌面並顯示您的租用戶。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

# If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
# Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"

Get-RdsTenant
```

當您發現具有您想要調整之主機集區的租使用者時，請遵循 [建立 Azure 自動化執行身份帳戶](#create-an-azure-automation-run-as-account) 中的指示，收集 **AZURERUNASCONNECTION** 應用程式識別碼，並使用您在下列 Cmdlet 中從上一個 Cmdlet 取得的 Windows 虛擬桌面租使用者名稱，以建立角色指派：

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId "<applicationid>" -TenantName "<tenantname>"
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>建立 Azure Logic 應用程式與執行排程

最後，您必須建立 Azure Logic 應用程式，並為新的調整工具設定執行排程。

1. 開啟 Windows PowerShell。

2. 執行下列 Cmdlet 來登入您的 Azure 帳戶。

    ```powershell
    Login-AzAccount
    ```

3. 執行下列 Cmdlet 來下載用來建立 Azure 邏輯應用程式的腳本。

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. 執行下列 Cmdlet，以使用具有 RDS 擁有者或 RDS 參與者權限的帳戶登入 Windows 虛擬桌面。

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

    # If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
    # Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"
    ```

5. 執行下列 PowerShell 腳本，為您的主機集區建立 Azure 邏輯應用程式和執行排程

    >[!NOTE]
    >您必須為每個想要自動調整的主機集區執行此腳本，但您只需要一個 Azure 自動化帳戶。

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $RDBrokerURL = (Get-RdsContext).DeploymentUrl
    $WVDTenant = Get-RdsTenant | Out-GridView -OutputMode:Single -Title "Select your WVD tenant"
    $WVDHostPool = Get-RdsHostPool -TenantName $WVDTenant.TenantName | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURI' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "RDBrokerURL"                   = $RDBrokerURL                             # Optional. Default: "https://rdbroker.wvd.microsoft.com"
         "TenantGroupName"               = $WVDTenant.TenantGroupName               # Optional. Default: "Default Tenant Group"
         "TenantName"                    = $WVDTenant.TenantName
         "HostPoolName"                  = $WVDHostPool.HostPoolName
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    執行腳本之後，Azure 邏輯應用程式應該會出現在資源群組中，如下圖所示。

    >[!div class="mx-imgBorder"]
    >![範例 Azure Logic 應用程式概觀頁面範例。](../media/logic-app.png)

    若要變更執行排程（例如變更週期間隔或時區），請移至 Azure 邏輯應用程式自動調整排程器，然後選取 [ **編輯** ] 以移至 Azure 邏輯應用程式設計工具。

    >[!div class="mx-imgBorder"]
    >![Azure 邏輯應用程式設計工具的影像。 [週期] 和 [webhook] 功能表可讓使用者編輯週期時間和 webhook 檔案開啟。](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>管理調整工具

現在您已經建立調整工具，且可以存取其輸出。 本節說明一些可能有幫助的功能。

### <a name="view-job-status"></a>檢視作業狀態

您可以檢視所有 Runbook 作業的摘要狀態，或在 Azure 入口網站中檢視特定 Runbook 作業的深入狀態。

在您選取的 Azure 自動化帳戶右側的 [作業統計資料] 下，您可以查看所有 runbook 作業的摘要清單。 開啟視窗左側的 [作業] 頁面，會顯示目前的工作狀態、開始時間和完成時間。

>[!div class="mx-imgBorder"]
>![作業狀態頁面的螢幕擷取畫面。](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>檢視記錄和調整工具輸出

您可以藉由開啟您的 runbook 並選取工作，來查看相應放大和相應縮小作業的記錄。

流覽至裝載 Azure 自動化帳戶的資源群組中的 runbook，然後選取 **[總覽**]。 在 [總覽] 頁面上，選取 [ **最近的工作** ] 下的作業來查看其調整工具輸出，如下圖所示。

>[!div class="mx-imgBorder"]
>![縮放工具的輸出視窗影像。](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>檢查 runbook 腳本版本號碼

您可以開啟 Azure 自動化帳戶中的 runbook 檔案，然後選取 [ **View**]，以檢查您正在使用的 runbook 腳本版本。 Runbook 的腳本將會出現在畫面的右側。 在腳本中，您會在區段下的格式看到版本號碼 `v#.#.#` `SYNOPSIS` 。 您可以在 [這裡](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/basicScale.ps1#L1)找到最新的版本號碼。 如果您在 runbook 腳本中沒有看到版本號碼，這表示您執行的是舊版腳本，您應該立即更新。 如果您需要更新 runbook 腳本，請遵循 [建立或更新 Azure 自動化帳戶](#create-or-update-an-azure-automation-account)中的指示。

### <a name="reporting-issues"></a>回報問題

當您報告問題時，您必須提供下列資訊來協助我們進行疑難排解：

- 在作業中的 **所有 [記錄** ] 索引標籤中造成問題的完整記錄。 若要瞭解如何取得記錄檔，請遵循 [查看記錄和調整工具輸出](#view-logs-and-scaling-tool-output)中的指示。 如果記錄檔中有任何敏感性或私用資訊，您可以先將其移除，再將問題提交給我們。

- 您所使用之 runbook 腳本的版本。 若要瞭解如何取得版本號碼，請參閱[檢查 runbook 腳本版本號碼](#check-the-runbook-script-version-number)。

- 您 Azure 自動化帳戶中安裝的每個 PowerShell 模組的版本號碼。 若要尋找這些模組，請開啟 Azure 自動化帳戶，在視窗左側窗格中的 [**共用資源**] 區段下選取 [**模組**]，然後搜尋模組的名稱。
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - >omsingestionapi
    - RDInfra. RDPowershell

- [執行身份帳戶](#create-an-azure-automation-run-as-account)的到期日。 若要尋找此資訊，請開啟您的 Azure 自動化帳戶，然後在視窗左側窗格中的 [**帳戶設定**] 下選取 [**執行身份帳戶**]。 到期日應位於 **Azure 執行帳戶**下。

### <a name="log-analytics"></a>Log Analytics

如果您決定使用 Log Analytics，您可以在 Log Analytics 工作區的**記錄**檔中，于 [**自訂**記錄檔] 底下的 [自訂記錄檔] 中，查看名為**WVDTenantScale_CL**的所有記錄資料。 我們列出了一些您可能會覺得有用的查詢範例。

- 若要查看主機集區的所有記錄，請輸入下列查詢

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 若要查看目前正在執行的工作階段主機 Vm 和主機集區中的作用中使用者會話總數，請輸入下列查詢

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 若要查看主機集區中所有工作階段主機 Vm 的狀態，請輸入下列查詢

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 若要查看任何錯誤和警告，請輸入下列查詢

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>報告問題

調整工具的問題報告目前正由 Microsoft 支援服務處理。 當您提出問題報告時，請務必遵循回報 [問題](#reporting-issues)中的指示。 如果您有關于此工具的意見反應，或想要要求新功能，請在 [RDS github 頁面](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool)上開啟標示為「4-WVD-調整工具」的 GitHub 問題。
