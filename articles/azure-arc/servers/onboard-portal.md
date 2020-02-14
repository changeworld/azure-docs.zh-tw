---
title: 從 Azure 入口網站將混合式機器連線至 Azure
description: 在本文中，您將瞭解如何使用 Azure 入口網站的 Azure Arc for server （預覽）來安裝代理程式並將電腦連線至 Azure。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/12/2020
ms.topic: conceptual
ms.openlocfilehash: 12fc29cf12fba6325af3197e727d94b3073ef2ff
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192307"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>從 Azure 入口網站將混合式機器連線至 Azure

您可以手動執行一組步驟，為您環境中的一或少數 Windows 或 Linux 電腦啟用「適用于伺服器的 Azure Arc」（預覽）。 或者，您可以藉由執行我們提供的範本腳本來使用自動化的方法。 此腳本會自動下載並安裝這兩個代理程式。

此方法需要您擁有電腦的系統管理員許可權，才能安裝和設定代理程式。 在 Linux 上，使用根帳號，而在 Windows 上，您是本機系統管理員群組的成員。

開始之前，請務必檢查[必要條件](overview.md#prerequisites)，並確認您的訂用帳戶和資源符合需求。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="generate-the-installation-script-from-the-azure-portal"></a>從 Azure 入口網站產生安裝腳本

您可以從 Azure 入口網站取得自動化下載和安裝的腳本，以及建立與 Azure Arc 的連線。 若要完成此程式，請執行下列動作：

1. 從瀏覽器移至 [ [Azure 入口網站](https://aka.ms/hybridmachineportal)]。

1. 在 [**機器-Azure Arc** ] 頁面上，選取位於左上方的 [**新增**] 或 [**建立機器-Azure Arc** ] 選項。 

1. 在 [**選取方法**] 頁面上，選取 [**使用互動式腳本新增電腦**] 磚，然後選取 [**產生腳本**]。

1. 在 [**產生腳本**] 頁面上，選取您要在 Azure 中管理電腦的訂用帳戶和資源群組。 選取將儲存電腦中繼資料的 Azure 位置。

    >[!NOTE]
    >適用于伺服器的 Azure Arc （預覽）僅支援下欄區域：
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >
    >在 [總覽][文章中選取區域時，請](overview.md#supported-regions)參閱其他考慮。

1. 在 [**產生腳本**] 頁面上的 [**作業系統**] 下拉式清單中，選取將執行腳本的作業系統。

1. 如果電腦透過 proxy 伺服器進行通訊以連線到網際網路，請選取 **[下一步： Proxy 伺服器]** 。 
1. 在 [ **proxy 伺服器**] 索引標籤上，指定 PROXY 伺服器 IP 位址，或電腦將用來與 Proxy 伺服器通訊的名稱和埠號碼。 請以 `http://<proxyURL>:<proxyport>` 格式輸入值。 
1. 選取 [**審查 + 產生**]。

1. 在 [**審查 + 產生**] 索引標籤上，檢查摘要資訊，然後選取 [**下載**]。 如果您仍然需要進行變更，請選取 [**上一步**]。

## <a name="install-and-validate-the-agent-on-windows"></a>在 Windows 上安裝和驗證代理程式

### <a name="install-manually"></a>手動安裝
您可以藉由執行 Windows Installer 套件*AzureConnectedMachineAgent*，手動安裝連線的電腦代理程式。 

> [!NOTE]
> * 若要安裝或卸載代理程式，您必須具有*系統管理員*許可權。
> * 您必須先下載安裝程式套件，並將其複製到目標伺服器上的資料夾，或從共用的網路資料夾。 如果您在沒有任何選項的情況下執行安裝程式套件，它會啟動安裝程式，您可以依照下列步驟以互動方式安裝代理程式。

如果電腦需要透過 proxy 伺服器與服務通訊，則在安裝代理程式之後，您必須執行本文稍後所述的命令。 這會將 proxy 伺服器系統內容變數設定 `https_proxy`。

下表將強調說明命令列中適用於代理程式的安裝程式所支援的參數。

| 參數 | 描述 |
|:--|:--|
| /? | 傳回命令列選項的清單。 |
| /S | 執行無訊息安裝，而不需要與使用者互動。 |

例如，若要使用 `/?` 參數來執行安裝程式，請輸入 `msiexec.exe /i AzureConnectedMachineAgent.msi /?`。

連線機器代理程式的檔案預設會安裝在*C:\Program Files\AzureConnectedMachineAgent*中。 如果代理程式在安裝完成後無法啟動，請檢查記錄檔以取得詳細錯誤資訊。 記錄檔目錄是 *%Programfiles%\AzureConnectedMachineAgentAgent\logs*。

### <a name="install-with-the-scripted-method"></a>使用腳本方法安裝

1. 登入伺服器。

1. 開啟已提高權限的 PowerShell 命令提示字元。

1. 變更至您將腳本複製到其中的資料夾或共用，然後執行 `./OnboardingScript.ps1` 腳本，在伺服器上執行此工作。

### <a name="configure-the-agent-proxy-setting"></a>設定代理程式 proxy 設定

若要設定 proxy 伺服器環境變數，請執行下列命令：

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>代理程式不支援在此預覽中設定 proxy 驗證。
>

### <a name="configure-agent-communication"></a>設定代理程式通訊

安裝代理程式之後，您必須執行下列命令，將代理程式設定為與 Azure Arc 服務進行通訊：

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>在 Linux 上安裝和驗證代理程式

適用于 Linux 的已連線機器代理程式是以發佈的慣用套件格式（）提供。RPM 或。DEB），裝載于 Microsoft 封裝存放[庫](https://packages.microsoft.com/)中。 [Shell 腳本配套 `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent)會執行下列動作：

- 設定主機電腦從 packages.microsoft.com 下載代理程式套件。
- 安裝混合式資源提供者套件。

（選擇性）您可以包含 `--proxy "{proxy-url}:{proxy-port}"` 參數，以使用您的 proxy 資訊來設定代理程式。

此腳本也包含用來識別支援和不支援之散發套件的邏輯，並會驗證執行安裝所需的許可權。 

下列範例會下載並安裝代理程式：

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

若要下載並安裝代理程式，包括將代理程式設定為透過 proxy 伺服器進行通訊的 `--proxy` 參數，請執行下列命令：

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>設定代理程式通訊

安裝代理程式之後，請執行下列命令，將它設定為與 Azure Arc 服務進行通訊：

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>驗證與 Azure Arc 的連線

在您安裝代理程式並將其設定為連接到 Azure Arc （預覽）之後，請移至 Azure 入口網站，確認伺服器已成功連線。 在[Azure 入口網站](https://aka.ms/hybridmachineportal)中查看您的電腦。

![成功的伺服器連接](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>清除

若要中斷電腦與 Azure Arc （預覽）的連線，請執行下列動作：

1. 前往 [ [Azure 入口網站](https://aka.ms/hybridmachineportal)]，開啟 [伺服器的 Azure Arc （預覽）]。

1. 選取清單中的電腦，選取省略號（ **...** ），然後選取 [**刪除**]。

1. 若要從電腦卸載 Windows 代理程式，請執行下列動作：

    a. 使用具有系統管理員許可權的帳戶登入電腦。  
    b. 在 [**控制台**] 中，選取 [**程式和功能**]。  
    c. 在 [**程式和功能**] 中，選取 [ **Azure 連線的機器代理程式**]，選取 [**卸載**]，然後選取 **[是]**  

    >[!NOTE]
    > 您也可以按兩下 [ **AzureConnectedMachineAgent**安裝程式套件] 來執行 [代理程式安裝精靈]。

    如果您想要編寫移除代理程式的腳本，您可以使用下列範例，它會抓取產品代碼，並使用 Msiexec 命令列 `msiexec /x {Product Code}`來卸載代理程式。 若要這樣做：  
    
    a. 開啟 [登錄編輯程式]。  
    b. 在 [登錄機碼 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`] 下，尋找並複製 [產品碼 GUID]。  
    c. 然後，您可以使用 Msiexec 來卸載代理程式。

    下列範例示範如何卸載代理程式：

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. 若要卸載 Linux 代理程式，請執行下列命令：

      ```bash
      sudo apt purge hybridagent
      ```

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Azure 原則](../../governance/policy/overview.md)來管理您的機器，例如 VM[來賓](../../governance/policy/concepts/guest-configuration.md)設定、確認機器回報至預期的 Log Analytics 工作區、使用[vm 的 Azure 監視器](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)來啟用監視等功能。

- 深入瞭解[Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 當您想要主動監視機器上執行的作業系統和工作負載、使用自動化 runbook 或解決方案（例如更新管理）進行管理，或使用其他 Azure 服務（例如[Azure 資訊安全中心](../../security-center/security-center-intro.md)）時，需要適用于 Windows 和 Linux 的 Log Analytics 代理程式。