---
title: 縮放會話承載 Azure 自動化 - Azure
description: 如何使用 Azure 自動化自動縮放 Windows 虛擬桌面工作階段主機。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349873"
---
# <a name="scale-session-hosts-using-azure-automation"></a>使用 Azure 自動化擴展工作階段主機

您可以通過擴展虛擬機器 （VM） 來降低 Windows 虛擬桌面部署總成本。 這意味著在非高峰使用時間內關閉和釋放工作階段主機 VM，然後在尖峰時間重新打開並重新分配它們。

在本文中，您將瞭解使用 Azure 自動化和 Azure 邏輯應用構建的縮放工具，該工具將自動縮放 Windows 虛擬桌面環境中的工作階段主機虛擬機器。 要瞭解如何使用縮放工具，請跳到[先決條件](#prerequisites)。

## <a name="how-the-scaling-tool-works"></a>縮放工具的工作原理

擴展工具為希望優化其工作階段主機 VM 成本的客戶提供了低成本的自動化選項。

您可以使用縮放工具：
 
- 根據高峰和非高峰上班時間安排 VM 啟動和停止。
- 根據每個 CPU 內核的會話數橫向擴展 VM。
- 在非高峰時間以 VM 進行縮放，使工作階段主機 VM 的最小數量處於運行狀態。

縮放工具使用 Azure 自動化 PowerShell 運行簿、Webhook 和 Azure 邏輯應用的組合來運行。 該工具運行時，Azure 邏輯應用調用 Webhook 以啟動 Azure 自動化運行簿。 然後，Runbook 創建一個作業。

在高峰使用期間，作業會檢查每個主機池的當前正在運行的工作階段主機的當前會話數和 VM 容量。 它使用此資訊計算正在運行的工作階段主機 VM 是否可以基於為**createazurelogicapp.ps1**檔定義的*會話閾值PerCPU*參數支援現有會話。 如果工作階段主機 VM 不支援現有會話，作業將在主機池中啟動其他工作階段主機 VM。

>[!NOTE]
>*會話閾值PerCPU*不限制 VM 上的會話數。 此參數僅確定何時需要啟動新 VM 來負載平衡連接。 要限制會話數，您需要按照說明["設置-RdsHostPool"](/powershell/module/windowsvirtualdesktop/set-rdshostpool/)相應地配置*MaxSessionLimit*參數。

在非高峰使用時間內，作業根據*最小數量RDSH*參數確定應關閉的工作階段主機 VM。 作業將工作階段主機 VM 設置為耗盡模式，以防止連接到主機的新會話。 如果將 *"限制秒秒到ForceLogOffUser"* 參數設置為非零正值，作業將通知當前登錄的任何使用者保存其工作，等待配置的時間量，然後強制使用者登出。一旦工作階段主機 VM 上的所有使用者會話都已登出，作業將關閉 VM。

如果將 *"限制秒秒到ForceLogOffUser"* 參數設置為零，作業將允許指定群組原則中的會話配置設置來處理登出使用者會話。 要查看這些群組原則，請訪問**電腦配置** > **策略** > **管理範本** > **Windows 元件** > **終端服務** > **終端伺服器** > **會話時間限制**。 如果工作階段主機 VM 上有任何活動會話，則作業將使工作階段主機 VM 保持運行。 如果沒有活動會話，作業將關閉工作階段主機 VM。

作業根據設置的定期間隔定期運行。 您可以根據 Windows 虛擬桌面環境的大小更改此間隔，但請記住，啟動和關閉虛擬機器可能需要一些時間，因此請記住考慮延遲。 我們建議將定期間隔設置為每 15 分鐘一次。

但是，該工具也有以下限制：

- 此解決方案僅適用于池工作階段主機 VM。
- 此解決方案管理任何區域中的 VM，但只能在與 Azure 自動化帳戶和 Azure 邏輯應用相同的訂閱中使用。

>[!NOTE]
>縮放工具控制正在縮放的主機池的負載平衡模式。 它將其設置為高峰和非尖峰時間的廣度優先負載平衡。

## <a name="prerequisites"></a>Prerequisites

在開始設置縮放工具之前，請確保已準備好以下事項：

- [Windows 虛擬桌面租戶和主機池](create-host-pools-arm-template.md)
- 在 Windows 虛擬桌面服務中配置和註冊的工作階段主機池 VM
- 在 Azure 訂閱上具有[參與者存取權限](../role-based-access-control/role-assignments-portal.md)的使用者

用於部署該工具的電腦必須具有： 

- Windows PowerShell 5.1 或更高版本
- 微軟 Az PowerShell 模組

如果你已經做好了一切準備，那就讓我們開始吧。

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶

首先，您需要一個 Azure 自動化帳戶來運行 PowerShell 運行簿。 以下是設置帳戶的方式：

1. 以系統管理員身分開啟 Windows PowerShell。
2. 運行以下 Cmdlet 以登錄到 Azure 帳戶。

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >您的帳戶必須具有要在 Azure 訂閱上部署縮放工具的參與者許可權。

3. 運行以下 Cmdlet 以下載用於創建 Azure 自動化帳戶的腳本：

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. 運行以下 Cmdlet 以執行腳本並創建 Azure 自動化帳戶：

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Cmdlet 的輸出將包括一個 webhook URI。 請確保保留 URI 的記錄，因為在為 Azure 邏輯應用設置執行計畫時，會將其用作參數。

6. 設置 Azure 自動化帳戶後，請登錄到 Azure 訂閱並進行檢查，以確保 Azure 自動化帳戶和相關 Runbook 已顯示在指定的資源組中，如下圖所示：

   ![顯示新創建的自動化帳戶和 Runbook 的 Azure 概覽頁的圖像。](media/automation-account.png)

  要檢查 Webhook 是否位於其應該的位置，請選擇 Runbook 的名稱。 接下來，轉到 Runbook 的資源部分並選擇**Webhook。**

## <a name="create-an-azure-automation-run-as-account"></a>創建 Azure 自動化作為帳戶運行

現在，您已經擁有了 Azure 自動化帳戶，還需要創建 Azure 自動化運行為帳戶來訪問 Azure 資源。

[Azure 自動化運行為帳戶](../automation/manage-runas-account.md)提供用於使用 Azure Cmdlet 管理 Azure 中的資源的身份驗證。 創建"運行為"帳戶時，它會在 Azure 活動目錄中創建新的服務主體使用者，並在訂閱級別將參與者角色分配給服務主體使用者，Azure 運行作為帳戶是使用證書和服務主體名稱，無需在憑據物件中存儲使用者名和密碼。 要瞭解有關"作為身份驗證運行"的更多內容，請參閱[限制"作為帳戶運行"許可權](../automation/manage-runas-account.md#limiting-run-as-account-permissions)。

任何作為訂閱管理員角色的成員和訂閱共同管理員的使用者都可以按照下一節的說明創建"運行為"帳戶。

要在 Azure 帳戶中創建"運行"帳戶，請執行如下設置：

1. 在 Azure 門戶中，選擇 **"所有服務**"。 在資源清單中，輸入並選擇 **"自動化帳戶**"。

2. 在 **"自動化帳戶"** 頁上，選擇自動化帳戶的名稱。

3. 在視窗左側的窗格中，在"帳戶設置"部分下選擇 **"以帳戶身份運行**"。

4. 選擇**Azure 以帳戶身份運行**。 當"**添加 Azure 運行為帳戶"** 窗格出現時，查看概覽資訊，然後選擇 **"創建"** 以啟動帳戶創建過程。

5. 等待幾分鐘，Azure 創建"以運行為帳戶"。" 您可以在"通知"下跟蹤功能表中的創建進度。

6. 該過程完成後，它將在指定的自動化帳戶中創建名為 AzureRunAsConnect 的資產。 連接資產包含應用程式 ID、租戶 ID、訂閱 ID 和證書指紋。 請記住應用程式 ID，因為稍後將使用它。

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>在 Windows 虛擬桌面中建立角色指派

接下來，您需要創建角色指派，以便 AzureRunAsConnect 可以與 Windows 虛擬桌面進行交互。 請確保使用 PowerShell 使用具有創建角色指派許可權的帳戶登錄。

首先，下載並導入[Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以便如果您尚未在 PowerShell 會話中使用。 執行下列 PowerShell Cmdlet 以連線至 Windows 虛擬桌面並顯示您的租用戶。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

當您找到具有要縮放的主機池的租戶時，請按照[創建 Azure 自動化帳戶](#create-an-azure-automation-account)中的說明操作，並使用以下 Cmdlet 中以前的 Cmdlet 中的租戶名稱創建角色指派：

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>創建 Azure 邏輯應用和執行計畫

最後，您需要創建 Azure 邏輯應用並為新的縮放工具設置執行計畫。

1.  以管理員身份打開 Windows 電源外殼

2.  運行以下 Cmdlet 以登錄到 Azure 帳戶。

     ```powershell
     Login-AzAccount
     ```

3. 運行以下 Cmdlet 以在本地電腦上下載 createazurelogicapp.ps1 指令檔。

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. 運行以下 Cmdlet 以使用具有 RDS 擁有者或 RDS 參與者許可權的帳戶登錄到 Windows 虛擬桌面。

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 運行以下 PowerShell 腳本以創建 Azure 邏輯應用和執行計畫。

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

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

     運行腳本後，邏輯應用應顯示在資源組中，如下圖所示。

     ![示例 Azure 邏輯應用的概覽頁的圖像。](media/logic-app.png)

要更改執行計畫（如更改定期間隔或時區），請轉到自動縮放計畫程式並選擇 **"編輯"** 以轉到邏輯應用設計器。

![邏輯應用設計器的圖像。 允許使用者編輯重複次數和 Webhook 檔打開的定期和 Webhook 功能表。](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>管理縮放工具

現在，您已經創建了縮放工具，您可以訪問其輸出。 本節介紹您可能認為有用的一些功能。

### <a name="view-job-status"></a>檢視作業狀態

您可以查看所有 Runbook 作業的匯總狀態，或在 Azure 門戶中查看特定 Runbook 作業的更深入狀態。

在所選自動化帳戶的右側"作業統計資訊"下，您可以查看所有 Runbook 作業的摘要清單。 打開視窗左側的**作業**頁將顯示當前作業狀態、開始時間和完成時間。

![作業狀態頁的螢幕截圖。](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>查看日誌和縮放工具輸出

您可以通過打開 Runbook 並選擇作業的名稱來查看橫向擴展和擴展操作的日誌。

導航到託管 Azure 自動化帳戶的資源組中的 Runbook（預設名稱為 WVDAutoScaleRunbook），然後選擇 **"概述**"。 在概覽頁上，選擇"最近作業"下的作業以查看其縮放工具輸出，如下圖所示。

![縮放工具的輸出視窗的圖像。](media/tool-output.png)

## <a name="report-issues"></a>報告問題

如果縮放工具遇到任何問題，可以在[RDS GitHub 頁](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)中報告這些問題。
