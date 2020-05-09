---
title: 調整工作階段主機 Azure 自動化-Azure
description: 如何使用 Azure 自動化自動調整 Windows 虛擬桌面工作階段主機。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12b56519ab3fe9319f00841bd8a0cc795d99e950
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615457"
---
# <a name="scale-session-hosts-using-azure-automation"></a>使用 Azure 自動化調整工作階段主機規模

>[!IMPORTANT]
>此內容適用于不支援 Azure Resource Manager Windows 虛擬桌面物件的秋季2019版。

您可以藉由調整您的虛擬機器（Vm），降低總 Windows 虛擬桌面部署成本。 這表示關閉和解除配置工作階段主機 Vm 期間的離峰使用時數，然後將它們重新開啟，並在尖峰時間重新配置它們。

在本文中，您將瞭解以 Azure 自動化和 Azure Logic Apps 為基礎的調整工具，其會自動調整 Windows 虛擬桌面環境中的工作階段主機虛擬機器。 若要瞭解如何使用調整工具，請直接跳至[必要條件](#prerequisites)。

## <a name="how-the-scaling-tool-works"></a>調整工具的運作方式

調整工具為想要優化其工作階段主機 VM 成本的客戶提供低成本的自動化選項。

您可以使用調整工具來執行下列動作：
 
- 根據尖峰和離峰上班時間，排程要啟動和停止的 Vm。
- 根據每個 CPU 核心的會話數目相應放大 Vm。
- 在離峰時段相應縮小 Vm，使工作階段主機 Vm 的最小數目維持在執行狀態。

調整工具會使用 Azure 自動化 PowerShell runbook、webhook 和 Azure Logic Apps 的組合來運作。 當此工具執行時，Azure Logic Apps 會呼叫 webhook 來啟動 Azure 自動化 runbook。 Runbook 接著會建立作業。

在尖峰使用時間期間，作業會檢查目前會話的數目，以及每個主機集區目前正在執行之工作階段主機的 VM 容量。 它會使用此資訊來計算執行中的工作階段主機 Vm 是否可以根據針對**createazurelogicapp**檔案所定義的*SessionThresholdPerCPU*參數來支援現有會話。 如果工作階段主機 Vm 無法支援現有的會話，此作業會啟動主機集區中的其他工作階段主機 Vm。

>[!NOTE]
>*SessionThresholdPerCPU*不會限制 VM 上的會話數目。 此參數只會決定何時需要啟動新的 Vm，以對連線進行負載平衡。 若要限制會話數目，您必須遵循 RdsHostPool 的指示，以適當地[設定](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) *MaxSessionLimit*參數。

在離峰使用時間期間，作業會根據*MinimumNumberOfRDSH*參數決定應關閉哪些工作階段主機 vm。 作業會將工作階段主機 Vm 設定為清空模式，以防止新會話連接到主機。 如果您將*LimitSecondsToForceLogOffUser*參數設定為非零的正值，此作業將會通知任何目前已登入的使用者儲存其工作、等候設定的時間量，然後強制使用者登出。當工作階段主機 VM 上的所有使用者會話都已登出後，此作業將會關閉 VM。

如果您將*LimitSecondsToForceLogOffUser*參數設定為零，此作業將會允許指定群組原則中的會話設定，處理登出使用者會話。 若要查看這些群組原則，請移至 [電腦設定**原則** > **] 系統管理範本** > **Windows 元件** > **終端** > **機** > 服務] [終端機**伺服器** > **會話時間限制**]。 如果工作階段主機 VM 上有任何使用中會話，此作業會讓工作階段主機 VM 繼續執行。 如果沒有作用中的會話，此作業將會關閉工作階段主機 VM。

作業會根據設定的週期間隔定期執行。 您可以根據 Windows 虛擬桌面環境的大小來變更此間隔，但請記住，啟動和關閉虛擬機器可能需要一些時間，因此請記得考慮延遲。 建議您將週期間隔設定為每15分鐘一次。

不過，此工具也有下列限制：

- 此解決方案只適用于集區工作階段主機 Vm。
- 此解決方案會管理任何區域中的 Vm，但只能在與您的 Azure 自動化帳戶和 Azure Logic Apps 相同的訂用帳戶中使用。

>[!NOTE]
>調整工具可控制要調整之主機集區的負載平衡模式。 它會將其設定為尖峰和離峰時段的廣度優先負載平衡。

## <a name="prerequisites"></a>Prerequisites

開始設定調整工具之前，請確定您已備妥下列專案：

- [Windows 虛擬桌面租使用者和主機集](create-host-pools-arm-template.md)區
- 已設定並向 Windows 虛擬桌面服務註冊的工作階段主機集區 Vm
- 具有 Azure 訂用帳戶之[參與者存取權](../../role-based-access-control/role-assignments-portal.md)的使用者

您用來部署工具的機器必須具備： 

- Windows PowerShell 5.1 或更新版本
- Microsoft Az PowerShell 模組

如果一切就緒，讓我們開始吧。

## <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶

首先，您需要 Azure 自動化帳戶來執行 PowerShell runbook。 以下說明如何設定您的帳戶：

1. 以系統管理員身分開啟 Windows PowerShell。
2. 執行下列 Cmdlet 來登入您的 Azure 帳戶。

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >您的帳戶必須具有您想要部署調整工具之 Azure 訂用帳戶的參與者許可權。

3. 執行下列 Cmdlet 來下載用來建立 Azure 自動化帳戶的腳本：

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. 執行下列 Cmdlet 來執行腳本，並建立 Azure 自動化帳戶：

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Cmdlet 的輸出會包含 webhook URI。 請務必保留 URI 的記錄，因為當您設定 Azure 邏輯應用程式的執行排程時，會使用它做為參數。

6. 設定 Azure 自動化帳戶之後，請登入您的 Azure 訂用帳戶，並檢查以確定您的 Azure 自動化帳戶和相關的 runbook 已出現在您指定的資源群組中，如下圖所示：

![Azure 總覽頁面的影像，其中顯示新建立的自動化帳戶和 runbook。](../media/automation-account.png)

  若要檢查您的 webhook 是否應為其所在位置，請選取您的 runbook 名稱。 接下來，移至您的 runbook 資源區段，然後選取 [ **webhook**]。

## <a name="create-an-azure-automation-run-as-account"></a>建立 Azure 自動化執行身分帳戶

現在您已有 Azure 自動化帳戶，您也必須建立 Azure 自動化執行身分帳戶，才能存取您的 Azure 資源。

[Azure 自動化執行身分帳戶](../../automation/manage-runas-account.md)會使用 azure Cmdlet 提供驗證來管理 azure 中的資源。 當您建立執行身分帳戶時，它會在 Azure Active Directory 中建立新的服務主體使用者，並在訂用帳戶層級將參與者角色指派給服務主體使用者，Azure 執行身分帳戶是安全地使用憑證和服務主體名稱進行驗證的絕佳方式，而不需要將使用者名稱和密碼儲存在認證物件中。 若要深入瞭解執行身分驗證，請參閱[限制執行身分帳戶許可權](../../automation/manage-runas-account.md#limiting-run-as-account-permissions)。

任何身為訂用帳戶管理員角色成員和訂閱共同管理員的使用者，都可以遵循下一節的指示來建立執行身分帳戶。

若要在您的 Azure 帳戶中建立執行身分帳戶：

1. 在 [Azure 入口網站中，選取 [**所有服務**]。 在資源清單中，輸入並選取 [**自動化帳戶**]。

2. 在 [**自動化帳戶**] 頁面上，選取自動化帳戶的名稱。

3. 在視窗左側的窗格中，選取 [帳戶設定] 區段下的 [**執行身分帳戶**]。

4. 選取 [ **Azure 執行身分帳戶**]。 當 [**新增 Azure 執行身分帳戶**] 窗格出現時，請參閱總覽資訊，然後選取 [**建立**] 以開始帳戶建立程式。

5. 請稍候幾分鐘，讓 Azure 建立執行身分帳戶。 您可以在 [通知] 底下的功能表中追蹤建立進度。

6. 當程式完成時，它會在指定的自動化帳戶中建立名為 AzureRunAsConnection 的資產。 連接資產會保存應用程式識別碼、租使用者識別碼、訂用帳戶識別碼和憑證指紋。 請記住 [應用程式識別碼]，因為您稍後將會用到它。

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>在 Windows 虛擬桌面中建立角色指派

接下來，您必須建立角色指派，讓 AzureRunAsConnection 可以與 Windows 虛擬桌面互動。 請務必使用 PowerShell，以有權建立角色指派的帳戶登入。

首先，下載並匯入[Windows 虛擬桌面 powershell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 powershell 會話中使用（如果您還沒有這麼做）。 執行下列 PowerShell Cmdlet 以連線至 Windows 虛擬桌面並顯示您的租用戶。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

當您找到您想要調整之主機集區的租使用者時，請遵循[建立 Azure 自動化帳戶](#create-an-azure-automation-account)中的指示，並使用您從上一個 Cmdlet 取得的租使用者名稱來建立角色指派：

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>建立 Azure 邏輯應用程式和執行排程

最後，您必須建立 Azure 邏輯應用程式，並為新的調整工具設定執行排程。

1.  以系統管理員身分開啟 Windows PowerShell

2.  執行下列 Cmdlet 來登入您的 Azure 帳戶。

     ```powershell
     Login-AzAccount
     ```

3. 執行下列 Cmdlet，以在本機電腦上下載 createazurelogicapp 腳本檔案。

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. 執行下列 Cmdlet，以具有 RDS 擁有者或 RDS 參與者許可權的帳戶登入 Windows 虛擬桌面。

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 執行下列 PowerShell 腳本，以建立 Azure 邏輯應用程式和執行排程。

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

     執行腳本之後，邏輯應用程式應該會出現在資源群組中，如下圖所示。

     ![範例 Azure 邏輯應用程式的 [總覽] 頁面影像。](../media/logic-app.png)

若要對執行排程進行變更（例如變更週期間隔或時區），請移至自動調整排程器並選取 [**編輯**]，移至 [Logic Apps 設計工具]。

![Logic Apps 設計工具的影像。 可讓使用者編輯週期和 webhook 檔案的 [週期] 和 [Webhook] 功能表開啟。](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>管理您的調整工具

既然您已建立調整工具，就可以存取其輸出。 本節說明一些您可能會覺得有用的功能。

### <a name="view-job-status"></a>檢視作業狀態

您可以在 Azure 入口網站中查看所有 runbook 作業的摘要狀態，或查看更深入的特定 runbook 作業狀態。

在您選取的自動化帳戶右側的 [作業統計資料] 底下，您可以查看所有 runbook 作業的摘要清單。 開啟視窗左側的 [**作業**] 頁面，會顯示目前的工作狀態、開始時間和完成時間。

![[作業狀態] 頁面的螢幕擷取畫面。](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>查看記錄和調整工具輸出

您可以藉由開啟 runbook 並選取您的作業名稱，來查看向外延展和相應縮小操作的記錄。

在裝載 Azure 自動化帳戶的資源群組中，流覽至 runbook （預設名稱是 WVDAutoScaleRunbook），然後選取 **[總覽**]。 在 [總覽] 頁面上，選取 [最近使用的作業] 底下的作業，以查看其調整工具輸出，如下圖所示。

![縮放工具的輸出視窗影像。](../media/tool-output.png)

## <a name="report-issues"></a>回報問題

如果您遇到任何與調整工具有關的問題，可以在[RDS GitHub 頁面](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)上報告。
