---
title: 使用 Azure 自動化調整工作階段主機 - Azure
description: 如何使用 Azure 自動化自動調整 Windows 虛擬桌面工作階段主機。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f659a40cbb9e3ef2d0e7fe4e527518a76507d5ee
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745702"
---
# <a name="scale-session-hosts-using-azure-automation"></a>使用 Azure 自動化調整工作階段主機

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 2019 年秋季版本。

您可以藉由調整虛擬機器 (VM) 來降低 Windows 虛擬桌面部署總成本。 這表示在離峰使用時間將工作階段主機 VM 關閉並解除配置，然後在尖峰時間將 VM 恢復啟動並重新配置。

在本文中，您將了解以 Azure 自動化和 Azure Logic Apps 建立的調整工具，這些工具將會自動調整 Windows 虛擬桌面環境中的工作階段主機虛擬機器。 若要了解如何使用調整工具，請直接跳至[必要條件](#prerequisites)。

## <a name="report-issues"></a>報告問題

調整工具的問題報告目前是由 GitHub 處理，而非由 Microsoft 支援服務處理。 如果您遇到任何與調整工具有關的問題，您可以在 [RDS GitHub 頁面](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)上開啟以「4a-WVD-scaling-logicapps」為標籤的 GitHub 問題。

## <a name="how-the-scaling-tool-works"></a>調整工具的運作方式

調整工具為想要最佳化其工作階段主機 VM 成本的客戶提供低成本的自動化選項。

使用調整工具可以：
 
- 根據尖峰和離峰上班時間來排程要啟動和停止的 VM。
- 根據每個 CPU 核心的工作階段數目來擴增 VM。
- 在離峰時間縮減 VM，讓維持在執行狀態的工作階段主機 VM 數目最少。

調整工具會使用 Azure 自動化 PowerShell Runbook、Webhook 和 Azure Logic Apps 的組合來運作。 當此工具執行時，Azure Logic Apps 會呼叫 Webhook 來啟動 Azure 自動化 Runbook。 接著 Runbook 會建立作業。

在尖峰使用時間，作業會檢查工作階段的目前數目，以及每個主機集區中目前執行中工作階段主機的 VM 容量。 它會使用此資訊，根據為 **createazurelogicapp.ps1** 檔案定義的 *SessionThresholdPerCPU* 參數，來計算執行中工作階段主機 VM 是否會支援現有的工作階段。 如果工作階段主機 VM 無法支援現有的工作階段，此作業會啟動主機集區中的其他工作階段主機 VM。

>[!NOTE]
>*SessionThresholdPerCPU* 不會限制 VM 上的工作階段數目。 此參數只會決定何時需要啟動新的 VM 以對連線進行負載平衡。 若要限制工作階段數目，您必須遵循 [RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) 的指示，以相應地設定 *MaxSessionLimit* 參數。

在離峰使用時間，作業會根據 *MinimumNumberOfRDSH* 參數決定哪些工作階段主機 VM 應關閉。 作業會將工作階段主機 VM 設定為清空模式，以防止新工作階段連線至主機。 如果您將 *LimitSecondsToForceLogOffUser* 參數設定為非零的正值，此作業會通知任何目前登入的使用者儲存其工作、等候所設定的時間長度，然後強制將使用者登出。當工作階段主機 VM 上所有的使用者工作階段都已登出後，此作業將會關閉 VM。

如果您將 *LimitSecondsToForceLogOffUser* 參數設定為零，此作業將會允許在指定群組原則中的工作階段設定來處理使用者工作階段的登出。 若要查看這些群組原則，請移至 [電腦設定] > [原則] > [管理範本] > [Windows 元件] > [終端服務] > [終端伺服器] > [工作階段時間限制]。 如果工作階段主機 VM 上有任何使用中會話，此作業會讓工作階段主機 VM 繼續執行。 如果沒有作用中的工作階段，此作業將會關閉工作階段主機 VM。

作業會根據設定的週期間隔定期執行。 您可以根據 Windows 虛擬桌面環境的大小來變更此間隔，但請記住，啟動和關閉虛擬機器可能需要一些時間，因此請記得將延遲時間納入考量。 建議您將週期間隔設定為每 15 分鐘一次。

不過，此工具有下列限制：

- 此解決方案只適用於集區工作階段主機 VM。
- 此解決方案可管理任何區域中的 VM，但是只能用於與 Azure 自動化帳戶和 Azure Logic Apps 相同的訂閱中。

>[!NOTE]
>調整工具可控制調整中主機集區的負載平衡模式。 它會針對尖峰和離峰時間將該模式設定為廣度優先負載平衡。

## <a name="prerequisites"></a>Prerequisites

開始設定調整工具之前，請先確定您已備妥下列項目：

- [Windows 虛擬桌面租用戶與主機集區](create-host-pools-arm-template.md)
- 透過 Windows 虛擬桌面服務設定和註冊的工作階段主機集區 VM
- 對 Azure 訂閱具有[參與者存取權](../../role-based-access-control/role-assignments-portal.md)的使用者

您用來部署工具的機器必須具備： 

- Windows PowerShell 5.1 或更新版本
- Microsoft Az PowerShell 模組

如果一切就緒，讓我們開始吧。

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶

首先，您需要 Azure 自動化帳戶來執行 PowerShell Runbook。 以下說明如何設定您的帳戶：

1. 以系統管理員身分開啟 Windows PowerShell。
2. 執行下列 Cmdlet 來登入您的帳戶。

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >您的帳戶必須對您想要部署調整工具的 Azure 訂閱擁有參與者權限。

3. 執行下列 Cmdlet 來下載用來建立 Azure 自動化帳戶的指令碼：

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. 執行下列 Cmdlet 來執行指令碼，並建立 Azure 自動化帳戶：

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Cmdlet 的輸出將包含 Webhook URI。 請務必保留 URI 的記錄，因為當您設定 Azure Logic Apps的執行排程時，會使用該記錄做為參數。

6. 設定 Azure 自動化帳戶之後，請登入您的 Azure 訂閱，並檢查以確定您的 Azure 自動化帳戶和相關的 Runbook 已出現在您指定的資源群組中，如下圖所示：

![Azure 概觀頁面圖，顯示新建立的自動化帳戶和 Runbook。](../media/automation-account.png)

  若要檢查您的 Webhook 是否在其應所在的位置，請選取您的 Runbook 名稱。 接下來，移至 Runbook 的 [資源] 區段，然後選取 [Webhook]。

## <a name="create-an-azure-automation-run-as-account"></a>建立 Azure 自動化執行身分帳戶

現在您已經有 Azure 自動化帳戶，但還須建立 Azure 自動化執行身分帳戶，才能存取您的 Azure 資源。

[Azure 自動化執行身分帳戶](../../automation/manage-runas-account.md)會提供驗證，以透過 Azure Cmdlet 管理 Azure 中的資源。 當您建立執行身分帳戶時，它會在 Azure Active Directory 中建立新的服務主體使用者，並在訂閱層級將參與者角色指派給服務主體使用者，Azure 執行身分帳戶是安全使用憑證和服務主體名稱進行驗證的絕佳方式，因為系統不需要將使用者名稱和密碼儲存在認證物件中。 若要深入瞭解執行身分驗證，請參閱[限制執行身分帳戶權限](../../automation/manage-runas-account.md#limit-run-as-account-permissions)。

任何身為訂閱管理員角色成員和訂閱共同管理員的使用者，都可以遵循下一節的指示來建立執行身分帳戶。

若要在您的 Azure 帳戶中建立執行身分帳戶：

1. 在 Azure 入口網站中，選取 [所有服務]。 在資源清單中，輸入並選取 [自動化帳戶]。

2. 在 [自動化帳戶] 頁面上，選取您的自動化帳戶名稱。

3. 在視窗左邊的窗格中，選取 [帳戶設定] 區段下的 [執行身分帳戶]。

4. 選取 [Azure 執行身分帳戶]。 當 [新增 Azure 執行身分帳戶] 窗格出現時，請檢閱概觀資訊，然後選取 [建立] 以開始帳戶建立流程。

5. 請稍候幾分鐘，讓 Azure 建立執行身分帳戶。 您可以在 [通知] 下方的功能表中追蹤建立進度。

6. 流程完成時，系統會在指定的自動化帳戶中建立名為 AzureRunAsConnection 的資產。 連線資產會保存應用程式識別碼、租用戶識別碼、訂閱識別碼，以及憑證指紋。 請記下應用程式識別碼，因為您稍後會用到它。

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>在 Windows 虛擬桌面中建立角色指派

接下來，您必須建立角色指派，讓 AzureRunAsConnection 能夠與 Windows 虛擬桌面互動。 請務必使用 PowerShell 以透過有權限建立角色指派的帳戶登入。

首先，下載並匯入 [Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 執行下列 PowerShell Cmdlet 以連線至 Windows 虛擬桌面並顯示您的租用戶。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

當您找到您想要調整其主機集區的租用戶時，請遵循[建立 Azure 自動化帳戶](#create-an-azure-automation-account)中的指示，並在下列 Cmdlet 中使用您從上一個 Cmdlet 取得的租用戶名稱來建立角色指派：

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>建立 Azure Logic 應用程式與執行排程

最後，您必須建立 Azure Logic 應用程式，並為新的調整工具設定執行排程。

1.  以管理員身分開啟 Windows PowerShell

2.  執行下列 Cmdlet 來登入您的帳戶。

     ```powershell
     Login-AzAccount
     ```

3. 執行下列 Cmdlet，以在本機電腦上下載 createazurelogicapp.ps1 指令檔檔案。

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. 執行下列 Cmdlet，以使用具有 RDS 擁有者或 RDS 參與者權限的帳戶登入 Windows 虛擬桌面。

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 執行下列 PowerShell 指令碼以建立 Azure Logic 應用程式和執行排程。

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id
     
     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id
     
     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location
     
     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName
     
     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName
     
     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"
     
     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name
     
     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     執行指令碼之後，Logic 應用程式應該會出現在資源群組中，如下圖所示。

     ![範例 Azure Logic 應用程式概觀頁面範例。](../media/logic-app.png)

若要對執行排程進行變更 (例如變更週期間隔或時區)，請移至自動調整排程器，然後選取 [編輯] 以移至 Logic Apps 設計工具。

![Logic Apps 設計工具的影像。 隨即開啟Recurrence 與 Webhook 功能表，可讓使用者編輯週期時間與 Webhook 檔案。](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>管理調整工具

現在您已經建立調整工具，且可以存取其輸出。 本節說明一些可能有幫助的功能。

### <a name="view-job-status"></a>檢視作業狀態

您可以檢視所有 Runbook 作業的摘要狀態，或在 Azure 入口網站中檢視特定 Runbook 作業的深入狀態。

在所選自動化帳戶的右邊，您可以在 [作業統計資料] 下方，看到所有 Runbook 作業摘要清單。 開啟視窗左側的 [作業] 頁面，會顯示目前的工作狀態、開始時間和完成時間。

![作業狀態頁面的螢幕擷取畫面。](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>檢視記錄和調整工具輸出

您可以藉由開啟 Runbook 並選取您的作業名稱，來檢視向外延展和相應縮小作業的記錄。

在裝載 Azure 自動化帳戶的資源群組中，瀏覽至 Runbook (預設名稱是 WVDAutoScaleRunbook)，然後選取 [概觀]。 在概觀頁面上，選取 [最近的作業] 底下的作業，以檢視其調整工具輸出，如下圖所示。

![縮放工具的輸出視窗影像。](../media/tool-output.png)

