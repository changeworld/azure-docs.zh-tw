---
title: 使用 Windows PowerShell DSC 安裝連接的電腦代理
description: 在本文中，您將瞭解如何使用 Azure Arc 將電腦連接到 Azure 的伺服器（預覽版）。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164679"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>如何使用 Windows PowerShell DSC 安裝連接的電腦代理

使用[Windows PowerShell 所需的狀態配置](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7)（DSC），您可以自動安裝 Windows 電腦的軟體和配置。 本文介紹如何使用 DSC 為混合 Windows 電腦上的伺服器連接電腦代理安裝 Azure Arc。

## <a name="requirements"></a>需求

- Windows PowerShell 版本 4.0 或更高版本

- [Azure 連接電腦 DSC](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0)模組

- 將電腦連接到 Azure Arc 的服務主體，用於非互動式伺服器。 如果您尚未為伺服器為 Arc 創建服務主體，請按照"[創建服務主體"](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)下的步驟進行大規模載入。

## <a name="install-the-connectedmachine-dsc-module"></a>安裝連接的機器 DSC 模組

1. 要手動安裝模組，請下載原始程式碼並將專案目錄的內容解壓縮到 。 `$env:ProgramFiles\WindowsPowerShell\Modules folder` 或者，運行以下命令，使用 PowerShellGet（在 PowerShell 5.0 中）從 PowerShell 庫安裝：

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 要確認安裝，請運行以下命令並確保看到 Azure 連接的電腦 DSC 資源可用。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   在輸出中，您應該會看到類似于以下內容的內容：

   ![已連接機器 DSC 模組安裝示例的確認](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>安裝代理並連接到 Azure

此模組中的資源旨在管理 Azure 連接的電腦代理配置。 還包括一個PowerShell腳本`AzureConnectedMachineAgent.ps1`，在`AzureConnectedMachineDsc\examples`資料夾中找到。 它使用社區資源來自動下載和安裝，並與 Azure Arc 建立連接。此腳本執行 Azure 門戶文章中在["將混合電腦連接到 Azure"](onboard-portal.md)中描述的類似步驟。

如果電腦需要通過代理伺服器與服務通信，則在安裝代理後，需要運行[此處](onboard-portal.md#configure-the-agent-proxy-setting)描述的命令。 這會設定 Proxy 伺服器系統環境變數 `https_proxy`。 您可以使用[ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)模組使用 DSC 執行此步驟，而不是手動運行該命令。

>[!NOTE]
>為了允許 DSC 運行，需要將 Windows 配置為接收 PowerShell 遠端命令，即使您正在運行本地主機配置。 若要輕鬆正確地設定您的環境，只要在已提高權限的 PowerShell 終端機中執行 `Set-WsManQuickConfig -Force` 即可。
>

可以使用`Start-DscConfiguration`Cmdlet 將配置文檔（MOF 檔案）應用於電腦。

以下是您傳遞給 PowerShell 腳本使用的參數。

- `TenantId`：表示 Azure AD 專用實例的唯一識別碼 （GUID）。

- `SubscriptionId`：要在 中的電腦的 Azure 訂閱的訂閱 ID （GUID）。

- `ResourceGroup`：您希望連接的電腦所屬的資源組名稱。

- `Location`：請參閱[支援的 Azure 區域](overview.md#supported-regions)。 此位置可以與資源組的位置相同或不同。

- `Tags`：應應用於連接的電腦資源的標記字串陣列。

- `Credential`：具有**應用程式 Id**和**密碼**的 PowerShell 憑據物件，用於使用[服務主體](onboard-service-principal.md)大規模註冊電腦。 

1. 在 PowerShell 主控台中，導航到保存`.ps1`檔的資料夾。

2. 執行下列 PowerShell 命令編譯 MOF 文件 (如需如何編譯 DSC 組態的資訊，請參閱 [DSC 組態](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)：

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 這將在名為`localhost.mof file`的新資料夾中創建 一`C:\dsc`個 。

在安裝好代理程式並將其設定為連線至適用於伺服器的 Azure Arc (預覽) 之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://aka.ms/hybridmachineportal)中檢視您的機器。

## <a name="adding-to-existing-configurations"></a>添加到現有配置

可以將此資源添加到現有的 DSC 配置中，以表示電腦的端到端配置。 例如，您可能希望將此資源添加到設置安全作業系統設置的配置中。

PowerShell 庫中的[CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0)模組可用於創建示例配置的[複合資源](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7)，以進一步簡化組合配置。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Azure 策略](../../governance/policy/overview.md)管理電腦，用於 VM[來賓配置](../../governance/policy/concepts/guest-configuration.md)、驗證電腦是否報告到預期的日誌分析工作區、使用 VM 啟用 Azure[監視器](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)等操作。

- 瞭解有關[日誌分析代理](../../azure-monitor/platform/log-analytics-agent.md)的更多資訊。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。