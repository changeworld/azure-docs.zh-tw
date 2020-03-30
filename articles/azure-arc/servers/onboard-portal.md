---
title: 從 Azure 入口網站將混合式機器連線至 Azure
description: 在本文中，您將了解如何從 Azure 入口網站使用適用於伺服器的 Azure Arc (預覽) 來安裝代理程式並將機器連線至 Azure。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 40885e1de4ff4c16d2a50399c654d8596396ab53
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366377"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>從 Azure 入口網站將混合式機器連線至 Azure

您可以手動執行一組步驟，為環境中的一部或少數幾部 Windows 或 Linux 機器啟用「適用於伺服器的 Azure Arc」(預覽)。 或者，您也可以藉由執行我們所提供的範本指令碼來使用自動化方法。 此指令碼會自動下載並安裝這兩個代理程式。

若要使用這種方法，您必須擁有機器上的系統管理員權限，才能安裝和設定代理程式。 在 Linux 上請使用根帳號，在 Windows 上則必須是本機系統管理員群組的成員。

開始之前，請務必檢閱[必要條件](overview.md#prerequisites)並確認訂用帳戶和資源已符合需求。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="generate-the-installation-script-from-the-azure-portal"></a>從 Azure 入口網站產生安裝指令碼

用來自動下載和安裝以及用來與 Azure Arc 建立連線的指令碼，可從 Azure 入口網站取得。 若要完成此程序，請執行下列動作：

1. 在瀏覽器中，移至 [Azure 入口網站](https://aka.ms/hybridmachineportal)。

1. 在 [機器 - Azure Arc]**** 頁面上，選取左上角的 [新增]****，或選取中間窗格底部的 [建立機器 - Azure Arc]**** 選項。 

1. 在 [選取方法]**** 頁面上，選取 [使用互動式指令碼新增機器]**** 圖格，然後選取 [產生指令碼]****。

1. 在 [產生指令碼]**** 頁面上，選取您要在 Azure 中用來管理機器的訂用帳戶和資源群組。 選取要用來儲存機器中繼資料的 Azure 位置。

    >[!NOTE]
    >適用於伺服器的 Azure Arc (預覽) 僅支援下欄區域：
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >
    >在"概述"一文中[在此處](overview.md#supported-regions)選擇區域時，請查看其他注意事項。

1. 在 [產生指令碼]**** 頁面的 [作業系統]**** 下拉式清單中，選取要作為指令碼執行平台的作業系統。

1. 如果電腦正在通過代理伺服器進行通信以連接到互聯網，請選擇"**下一步：代理伺服器**"。 
1. 在 [Proxy 伺服器]**** 索引標籤上，指定要供機器用來與 Proxy 伺服器通訊的 Proxy 伺服器 IP 位址或名稱與連接埠號碼。 請以 `http://<proxyURL>:<proxyport>` 格式輸入值。 
1. 選取 [檢閱 + 產生]****。

1. 在 [檢閱 + 產生]**** 索引標籤上檢閱摘要資訊，然後選取 [下載]****。 如果您還需要變更，請選取 [上一步]****。

## <a name="install-and-validate-the-agent-on-windows"></a>在 Windows 上安裝和驗證代理程式

### <a name="install-manually"></a>手動安裝

您可以執行 Windows Installer 套件「AzureConnectedMachineAgent.msi」** 來手動安裝 Azure Connected Machine 代理程式。 

> [!NOTE]
> * 若要安裝或解除安裝此代理程式，您必須具有「系統管理員」** 權限。
> * 您必須先下載安裝程式套件，並將其複製到目標伺服器上的資料夾，或從共用的網路資料夾執行此動作。 如果您在執行此安裝程式套件時未使用任何選項，則會啟動以互動方式安裝代理程式的安裝精靈。

如果機器需要透過 Proxy 伺服器與服務通訊，則在安裝代理程式之後，您必須執行本文稍後所述的命令。 這會設定 Proxy 伺服器系統環境變數 `https_proxy`。

如果您不熟悉 Windows 安裝程式包的命令列選項，請查看[Msiexec 標準命令列選項](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options)和[Msiexec 命令列選項](https://docs.microsoft.com/windows/win32/msi/command-line-options)。

例如，使用`/?`參數運行安裝程式以查看説明和快速參考選項。 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

要靜默安裝代理並在資料夾中創建設置日誌檔，`C:\Support\Logs`請運行以下命令。

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

預設情況下，已連接的電腦代理的檔安裝在*C：*程式檔_Azure連接電腦代理*中。 如果此代理程式在安裝完成之後無法啟動，請檢查記錄以取得詳細的錯誤資訊。 記錄目錄是「%Programfiles%\AzureConnectedMachineAgentAgent\logs」**。

### <a name="install-with-the-scripted-method"></a>使用指令碼方法來安裝

1. 登入伺服器。

1. 開啟已提高權限的 PowerShell 命令提示字元。

1. 變更至作為指令碼複製目的地的資料夾或共用，然後在伺服器上執行 `./OnboardingScript.ps1` 指令碼來執行此工作。

### <a name="configure-the-agent-proxy-setting"></a>設定代理程式 Proxy 設定

若要設定 Proxy 伺服器環境變數，請執行下列命令：

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>本代理程式不支援在此預覽版中設定 Proxy 驗證。
>

### <a name="configure-agent-communication"></a>設定代理程式通訊

在安裝代理程式後，您必須執行下列命令，將代理程式設定為與 Azure Arc 服務進行通訊：

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>在 Linux 上安裝和驗證代理程式

適用於 Linux 的 Connected Machine 代理程式會以發行版本慣用的套件格式來提供 (.RPM 或 .DEB)，其裝載位置在 Microsoft 的[套件存放庫](https://packages.microsoft.com/)。 [殼層指令碼套件組合 `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) 會執行下列動作：

- 設定主機電腦使其從 packages.microsoft.com 下載代理程式套件。
- 安裝混合式資源提供者套件。

(選擇性) 您可以納入 `--proxy "{proxy-url}:{proxy-port}"` 參數，以使用 Proxy 資訊來設定代理程式。

此指令碼也包含用來識別可支援和不支援發行版本的邏輯，並會驗證用來執行安裝所需的權限。 

下列範例會下載並安裝代理程式：

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

若要下載並安裝代理程式，包括用來將代理程式設定為透過 Proxy 伺服器進行通訊的 `--proxy` 參數，請執行下列命令：

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>設定代理程式通訊

安裝好代理程式後，請執行下列命令將其設定為與 Azure Arc 服務進行通訊：

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>確認與 Azure Arc 的連線

在安裝好代理程式並將其設定為連線至適用於伺服器的 Azure Arc (預覽) 之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://aka.ms/hybridmachineportal)中檢視您的機器。

![成功的伺服器連線](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Azure 策略](../../governance/policy/overview.md)管理電腦，用於 VM[來賓配置](../../governance/policy/concepts/guest-configuration.md)、驗證電腦是否報告到預期的日誌分析工作區、使用 VM 啟用 Azure[監視器](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)等操作。

- 瞭解有關[日誌分析代理](../../azure-monitor/platform/log-analytics-agent.md)的更多資訊。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。
