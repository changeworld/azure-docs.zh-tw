---
title: 使用 Windows PowerShell DSC 安裝已連線的機器代理程式
description: 在本文中，您將瞭解如何使用已啟用 Azure Arc 的伺服器，使用 Windows PowerShell DSC 將機器連線到 Azure。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 5349ff870be324c0137d2adcaf201ecdac286cbc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887646"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>如何使用 Windows PowerShell DSC 來安裝已連線的機器代理程式

您可以使用 [Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted) (DSC) ，將 Windows 電腦的軟體安裝和設定自動化。 本文說明如何使用 DSC，在混合式 Windows 電腦上安裝已啟用 Azure Arc 的伺服器連線的機器代理程式。

## <a name="requirements"></a>規格需求

- Windows PowerShell 版本4.0 或更高版本

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc) DSC 模組

- 用來以非互動方式將電腦連線到 Azure Arc 啟用伺服器的服務主體。 如果您尚未針對已啟用 Arc 的伺服器建立服務主體，請遵循「 [建立服務主體以進行大規模](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 上線」一節中的步驟。

## <a name="install-the-connectedmachine-dsc-module"></a>安裝 ConnectedMachine DSC 模組

1. 若要手動安裝模組，請下載原始程式碼，並將專案目錄的內容解壓縮至 `$env:ProgramFiles\WindowsPowerShell\Modules folder` 。 或者，使用 PowerShell 5.0) 中的 PowerShellGet (，執行下列命令以從 PowerShell 資源庫安裝：

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 若要確認安裝，請執行下列命令，並確定您看到可用的 Azure Connected Machine DSC 資源。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   在輸出中，您應該會看到類似下列的內容：

   ![確認已連線的電腦 DSC 模組安裝範例](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>安裝代理程式並連線至 Azure

此課程模組中的資源是設計來管理 Azure Connected Machine 代理程式設定。 也包含 PowerShell 腳本，可 `AzureConnectedMachineAgent.ps1` 在資料夾中找到 `AzureConnectedMachineDsc\examples` 。 它會使用社區資源將下載和安裝自動化，並建立與 Azure Arc 的連接。此腳本會執行從 Azure 入口網站文章將 [混合式機器連線到 Azure](onboard-portal.md) 中所述的類似步驟。

如果電腦需要透過 proxy 伺服器與服務通訊，則在安裝代理程式之後，您必須執行 [此處](manage-agent.md#update-or-remove-proxy-settings)所述的命令。 這會設定 Proxy 伺服器系統環境變數 `https_proxy`。 您可以使用 [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) 模組，透過 DSC 執行此步驟，而不是手動執行命令。

>[!NOTE]
>若要允許 DSC 執行，您必須將 Windows 設定為接收 PowerShell 遠端命令，即使是在執行 localhost 設定時也一樣。 若要輕鬆正確地設定您的環境，只要在已提高權限的 PowerShell 終端機中執行 `Set-WsManQuickConfig -Force` 即可。
>

您可以使用 Cmdlet 將設定檔 (MOF 檔案) 套用至電腦 `Start-DscConfiguration` 。

以下是您傳遞給要使用之 PowerShell 腳本的參數。

- `TenantId`： (GUID) 的唯一識別碼，代表 Azure AD 的專用實例。

- `SubscriptionId`：訂用帳戶識別碼 (您要用來執行機器的 Azure 訂用帳戶的 GUID) 。

- `ResourceGroup`：您想要連接的電腦所屬的資源組名。

- `Location`：請參閱 [支援的 Azure 區域](overview.md#supported-regions)。 此位置可與資源群組的位置相同或不同。

- `Tags`：應套用至已連線電腦資源之標記的字串陣列。

- `Credential`：具有 **ApplicationId** 和 **密碼** 的 PowerShell credential 物件，可用來使用 [服務主體](onboard-service-principal.md)來大規模註冊電腦。

1. 在 PowerShell 主控台中，流覽至您儲存檔案的資料夾 `.ps1` 。

2. 執行下列 PowerShell 命令編譯 MOF 文件 (如需如何編譯 DSC 組態的資訊，請參閱 [DSC 組態](/powershell/scripting/dsc/configurations/configurations)：

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 這會 `localhost.mof file` 在名為的新資料夾中建立 `C:\dsc` 。

當您安裝代理程式並將它設定為連接到已啟用 Azure Arc 的伺服器之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://aka.ms/hybridmachineportal)中檢視您的機器。

## <a name="adding-to-existing-configurations"></a>新增至現有的設定

此資源可以新增至現有的 DSC 設定，以代表機器的端對端設定。 例如，您可能想要將此資源新增至設定安全作業系統設定的設定。

PowerShell 資源庫中的 [CompositeResource](https://www.powershellgallery.com/packages/compositeresource) 模組可以用來建立範例設定的 [複合資源](/powershell/scripting/dsc/resources/authoringResourceComposite) ，以進一步簡化合並設定。

## <a name="next-steps"></a>後續步驟

- 了解如何使用 [Azure 原則](../../governance/policy/overview.md)，針對例如 VM [來賓設定](../../governance/policy/concepts/guest-configuration.md)、確認機器回報至預期的 Log Analytics 工作區、使用 [Azure 監視器與 VM](../../azure-monitor/insights/vminsights-enable-policy.md) 啟用監視等等項目，管理您的機器。

- 深入了解 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。
