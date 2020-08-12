---
title: 使用 Windows PowerShell DSC 安裝連線的電腦代理程式
description: 在本文中，您將瞭解如何使用適用于伺服器的 Azure Arc，將機器連接到 Azure (preview) 使用 Windows PowerShell DSC。
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: cdda3e6681d3e6912d031c45f5c6da9e92814e8f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120995"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>如何使用 Windows PowerShell DSC 安裝連線的電腦代理程式

使用[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) ，您可以將 windows 電腦的軟體安裝和設定自動化。 本文說明如何使用 DSC，為混合式 Windows 電腦上已連線的電腦代理程式安裝 Azure Arc。

## <a name="requirements"></a>規格需求

- Windows PowerShell 4.0 版或更新版本

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC 模組

- 用來以非互動方式將電腦連線至 Azure Arc 的服務主體。 如果您尚未為伺服器的 Arc 建立服務主體，請遵循[建立服務主體以進行大規模](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)上線一節中的步驟。

## <a name="install-the-connectedmachine-dsc-module"></a>安裝 ConnectedMachine DSC 模組

1. 若要手動安裝模組，請下載原始程式碼，並將專案目錄的內容解壓縮至 `$env:ProgramFiles\WindowsPowerShell\Modules folder` 。 或者，使用在 PowerShell 5.0) 中的 PowerShellGet (，執行下列命令從 PowerShell 資源庫安裝：

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 若要確認安裝，請執行下列命令，並確定您看到可用的 Azure 已連線電腦 DSC 資源。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   在輸出中，您應該會看到類似下面的內容：

   ![確認已連線的電腦 DSC 模組安裝範例](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>安裝代理程式並連線至 Azure

此課程模組中的資源是設計來管理 Azure 連線的機器代理程式設定。 此外，也包含 PowerShell 腳本 `AzureConnectedMachineAgent.ps1` ，可在資料夾中找到 `AzureConnectedMachineDsc\examples` 。 它會使用「社區」資源將下載和安裝自動化，並建立與 Azure Arc 的連線。此腳本會執行[從 Azure 入口網站將混合式機器連線至 Azure](onboard-portal.md)一文中所述的類似步驟。

如果電腦需要透過 proxy 伺服器與服務通訊，則在安裝代理程式之後，您必須執行[這裡](manage-agent.md#update-or-remove-proxy-settings)所述的命令。 這會設定 Proxy 伺服器系統環境變數 `https_proxy`。 您可以使用[ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)模組，透過 DSC 執行此步驟，而不是手動執行命令。

>[!NOTE]
>為了讓 DSC 能夠執行，Windows 必須設定為接收 PowerShell 遠端命令，即使您是在執行 localhost 設定時也一樣。 若要輕鬆正確地設定您的環境，只要在已提高權限的 PowerShell 終端機中執行 `Set-WsManQuickConfig -Force` 即可。
>

您可以使用 Cmdlet 將設定檔 (MOF 檔案) 套用至電腦 `Start-DscConfiguration` 。

以下是您傳遞給 PowerShell 腳本以使用的參數。

- `TenantId`：唯一識別碼 (GUID) ，代表您 Azure AD 的專用實例。

- `SubscriptionId`：訂用帳戶識別碼 (您的 Azure 訂用帳戶的 GUID) 您想要讓機器在其中。

- `ResourceGroup`：您要讓連線電腦所屬的資源組名。

- `Location`：請參閱[支援的 Azure 區域](overview.md#supported-regions)。 此位置可與資源群組的位置相同或不同。

- `Tags`：應套用至已連線電腦資源之標記的字串陣列。

- `Credential`：具有**ApplicationId**和**密碼**的 PowerShell 認證物件，用來使用[服務主體](onboard-service-principal.md)來大規模註冊電腦。 

1. 在 PowerShell 主控台中，流覽至您儲存檔案的資料夾 `.ps1` 。

2. 執行下列 PowerShell 命令編譯 MOF 文件 (如需如何編譯 DSC 組態的資訊，請參閱 [DSC 組態](/powershell/scripting/dsc/configurations/configurations?view=powershell-7)：

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 這會 `localhost.mof file` 在名為的新資料夾中建立 `C:\dsc` 。

在安裝好代理程式並將其設定為連線至適用於伺服器的 Azure Arc (預覽) 之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://aka.ms/hybridmachineportal)中檢視您的機器。

## <a name="adding-to-existing-configurations"></a>新增至現有的設定

此資源可以新增至現有的 DSC 設定，以代表機器的端對端設定。 例如，您可能想要將此資源新增至設定安全作業系統設定的設定。

PowerShell 資源庫中的[CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0)模組可以用來建立範例設定的[複合資源](/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7)，以進一步簡化組合設定。

## <a name="next-steps"></a>後續步驟

- 了解如何使用 [Azure 原則](../../governance/policy/overview.md)，針對例如 VM [來賓設定](../../governance/policy/concepts/guest-configuration.md)、確認機器回報至預期的 Log Analytics 工作區、使用 [Azure 監視器與 VM](../../azure-monitor/insights/vminsights-enable-policy.md) 啟用監視等等項目，管理您的機器。

- 深入了解 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。