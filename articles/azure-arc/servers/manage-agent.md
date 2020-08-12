---
title: 管理適用於伺服器的 Azure Arc (預覽) 代理程式
description: 本文描述適用於伺服器的 Azure Arc Connected Machine 代理程式生命週期期間，您通常會執行的不同管理工作。
ms.date: 07/30/2020
ms.topic: conceptual
ms.openlocfilehash: b7fcaca2188ef0e1e3c8c65226f8b383576082ba
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121284"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>管理和維護 Connected Machine 代理程式

在針對 Windows 或 Linux 初始部署適用於伺服器的 Azure Arc (預覽) Connected Machine 代理程式之後，如果代理程式已達到其生命週期的淘汰階段，您可能需要重新設定代理程式、升級、或是從電腦中移除。 您可以手動或自動的方式輕鬆地管理這些例行維護工作，後者可以降低操作錯誤和費用。

## <a name="upgrading-agent"></a>升級代理程式

您可以根據需求，以手動或自動方式將適用於 Windows 和 Linux 的 Azure Connected Machine 代理程式升級為最新版本。 下表描述執行代理程式升級的支援方法。

| 作業系統 | 升級方法 |
|------------------|----------------|
| Windows | 手動<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows 代理程式

適用於 Windows 的 Connected Machine 代理程式更新套件可從以下位置取得：

* Microsoft Update

* [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx)

* 您可以從 Microsoft 下載中心取得 [Windows 代理程式的 Windows Installer 套件](https://aka.ms/AzureConnectedMachineAgent)。

代理程式可以遵循各種不同的方法來進行升級，以支援您的軟體更新管理程序。 除了從 Microsoft Update 取得以外，您可以從命令提示字元、指令碼或其他自動化解決方案，或透過執行 `AzureConnectedMachine.msi` 從 UI 精靈手動下載並執行。

> [!NOTE]
> * 若要升級此代理程式，您必須具有「系統管理員」權限。
> * 若要手動升級，您必須先下載安裝程式套件，並將其複製到目標伺服器上的資料夾，或從共用的網路資料夾執行此動作。 

如果您不熟悉 Windows Installer 套件的命令列選項，請參閱 [Msiexec 標準命令列選項](/windows/win32/msi/standard-installer-command-line-options)和 [Msiexec 命令列選項](/windows/win32/msi/command-line-options)。

#### <a name="to-upgrade-using-the-setup-wizard"></a>若要使用安裝精靈進行升級

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行 **AzureConnectedMachineAgent.msi** 以啟動「安裝精靈」。

「安裝精靈」會探索先前版本是否存在，然後自動執行代理程式的升級。 當升級完成時，「安裝精靈」會自動關閉。

#### <a name="to-upgrade-from-the-command-line"></a>若要從命令列升級

1. 以具有系統管理權限的帳戶登入電腦。

2. 若要以無訊息模式升級代理程式，並在 `C:\Support\Logs` 資料夾中建立安裝記錄檔，請執行下列命令。

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 代理程式

若要將 Linux 電腦上的代理程式更新為最新版本，會牽涉到兩個命令。 一個命令是用來以存放庫中最新可用套件清單來更新本機套件索引，另一個命令是用來升級本機套件。

您可以從 Microsoft 的 [套件存放庫](https://packages.microsoft.com/)下載最新的代理程式套件。

> [!NOTE]
> 若要升級代理程式，您必須擁有「根」存取權限，或具有使用 Sudo 提升權限的帳戶。

#### <a name="upgrade-ubuntu"></a>升級 Ubuntu

1. 若要使用存放庫中所做的最新變更來更新本機套件索引，請執行下列命令：

    ```bash
    apt update
    ```

2. 若要升級您的系統，請執行下列命令：

    ```bash
    apt upgrade
    ```

[apt](https://help.ubuntu.com/lts/serverguide/apt.html) 命令的動作 (例如安裝和移除套件) 都會記錄在 `/var/log/dpkg.log` 記錄檔中。

#### <a name="upgrade-red-hatcentosamazon-linux"></a>升級 Red Hat/CentOS/Amazon Linux

1. 若要使用存放庫中所做的最新變更來更新本機套件索引，請執行下列命令：

    ```bash
    yum check-update
    ```

2. 若要升級您的系統，請執行下列命令：

    ```bash
    yum update
    ```

[yum](https://access.redhat.com/articles/yum-cheat-sheet) 命令的動作 (例如安裝和移除套件) 都會記錄在 `/var/log/yum.log` 記錄檔中。 

#### <a name="upgrade-suse-linux-enterprise"></a>升級 SUSE Linux Enterprise

1. 若要使用存放庫中所做的最新變更來更新本機套件索引，請執行下列命令：

    ```bash
    zypper refresh
    ```

2. 若要升級您的系統，請執行下列命令：

    ```bash
    zypper update
    ```

[zypper](https://en.opensuse.org/Portal:Zypper) 命令的動作 (例如安裝和移除套件) 都會記錄在 `/var/log/zypper.log` 記錄檔中。 

## <a name="about-the-azcmagent-tool"></a>關於 Azcmagent 工具

Azcmagent 工具 (Azcmagent.exe) 是用來在安裝或安裝之後修改代理程式的初始設定期間，設定適用於伺服器的 Azure Arc (預覽) Connected Machine 代理程式。 Azcmagent.exe 提供命令列參數以自訂代理程式及檢視其狀態：

* **Connect** - 將機器連線至 Azure Arc

* **Disconnect** - 中斷機器與 Azure Arc 的連線

* **Reconnect** - 將中斷連線的機器重新連線至 Azure Arc

* **Show** - 檢視代理程式狀態及其設定屬性 (資源群組名稱、訂閱帳戶識別碼、版本等等)，有助於針對代理程式的問題進行疑難排解。

* **-h 或 --help** - 顯示可用的命令列參數

    例如，若要查看 **Reconnect** 參數的詳細說明，請輸入 `azcmagent reconnect -h`。 

* **-v 或 --verbose** - 啟用詳細資訊記錄

您可以在以互動方式登入的情況下，手動執行 **Connect**、**Disconnect** 和 **Reconnect**，或使用您用來將多個代理程式上線的相同服務主體或使用 Microsoft 身分識別平台[存取權杖](../../active-directory/develop/access-tokens.md)，將其自動化。 如果您未使用服務主體向適用於伺服器的 Azure Arc (預覽) 註冊機器，請參閱下列[文章](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)以建立服務主體。

>[!NOTE]
>您必須具備 Linux 電腦的*根*存取權限，才能執行**azcmagent**。

### <a name="connect"></a>連線

此參數會指定 Azure Resource Manager 中的資源，代表已在 Azure 中建立機器。 資源是在指定的訂用帳戶和資源群組中，而機器的相關資料則是儲存在 `--location` 設定所指定的 Azure 區域中。 如果未指定，預設資源名稱就是這部機器的主機名稱。

接著會下載與系統指派機器身分識別對應的憑證，並儲存在本機。 完成此步驟之後，Azure Connected Machine Metadata Service 和來賓設定代理程式會開始與適用於伺服器的 Azure Arc (預覽) 進行同步處理。

若要使用服務主體進行連線，請執行下列命令：

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

若要使用存取權杖進行連線，請執行下列命令：

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

若要使用您的提升權限登入認證 (互動式) 進行連線，請執行下列命令：

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>中斷連接

此參數會指定 Azure Resource Manager 中的資源，代表已在 Azure 中刪除機器。 不會從機器刪除代理程式，必須以個別步驟來完成。 機器中斷連線之後，如果您想要向適用於伺服器的 Azure Arc (預覽) 重新註冊，請使用 `azcmagent connect`，就會在 Azure 中為其建立新的資源。

若要使用服務主體中斷連線，請執行下列命令：

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

若要使用存取權杖中斷連線，請執行下列命令：

`azcmagent disconnect --access-token <accessToken>`

若要使用您的提升權限登入認證 (互動式) 中斷連線，請執行下列命令：

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>重新連接

> [!WARNING]
> 此 `reconnect` 命令已被取代，不應使用。 此命令將會在未來的代理程式版本中移除，而現有的代理程式將無法完成重新連接要求。 相反地，請[中斷](#disconnect)電腦的連線，然後再重新[連接](#connect)。

此參數會重新連線已向適用於伺服器的 Azure Arc (預覽) 註冊或已與其連線的機器。 如果機器已關閉至少 45 天讓其憑證過期，這可能是必要的。 此參數會使用所提供的驗證選項來擷取新認證，該認證與代表這部機器的 Azure Resource Manager 資源對應。

此命令需要比 [Azure Connected Machine 上線](agent-overview.md#required-permissions)角色更高的權限。

若要使用服務主體進行重新連線，請執行下列命令：

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

若要使用存取權杖進行重新連線，請執行下列命令：

`azcmagent reconnect --access-token <accessToken>`

若要使用您的提升權限登入認證 (互動式) 進行重新連線，請執行下列命令：

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>移除代理程式

執行下列其中一種方法，從機器解除安裝 Windows 或 Linux Connected Machine 代理程式。 移除代理程式並不會向適用於伺服器的 Arc (預覽) 取消註冊機器，這是當您不再需要在 Azure 中管理機器時所執行的個別程序。

### <a name="windows-agent"></a>Windows 代理程式

下列兩種方法都會移除代理程式，但是不會移除機器上的 C:\Program Files\AzureConnectedMachineAgent 資料夾。

#### <a name="uninstall-from-control-panel"></a>從控制台解除安裝

1. 若要從機器解除安裝 Windows 代理程式，請執行下列動作：

    a. 使用具有系統管理員權限的帳戶登入電腦。  
    b. 在 [控制台] 中，選取 [程式和功能]。  
    c. 在 [程式和功能] 中，依序選取 [Azure Connected Machine 代理程式]、[解除安裝] 和 [是]。  

    >[!NOTE]
    > 您也可以連按兩下 **AzureConnectedMachineAgent.msi** 安裝程式套件來執行代理程式安裝精靈。

#### <a name="uninstall-from-the-command-line"></a>從命令列解除安裝

若要從命令提示字元手動解除安裝代理程式，或使用自動化方法 (例如指令碼)，您可以使用下列範例。 首先，您必須從作業系統擷取產品代碼，這是應用程式套件主體識別碼的 GUID。 解除安裝是使用 Msiexec.exe 命令列 - `msiexec /x {Product Code}` 來執行。

1. 開啟登錄編輯程式。

2. 在登錄機碼 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 底下，尋找並複製產品代碼 GUID。

3. 然後，您可以如同下列範例，使用 Msiexec 來解除安裝代理程式：

   * 從命令列輸入：

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * 您可以使用 PowerShell 執行相同的步驟：

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 代理程式

> [!NOTE]
> 若要解除安裝代理程式，您必須擁有「根」存取權限，或具有使用 Sudo 提升權限的帳戶。

若要解除安裝 Linux 代理程式，要使用的命令取決於 Linux 作業系統。

- 針對 Ubuntu，執行下列命令：

    ```bash
    sudo apt purge azcmagent
    ```

- 針對 RHEL、CentOS 和 Amazon Linux，執行下列命令：

    ```bash
    sudo yum remove azcmagent
    ```

- 針對 SLES，執行下列命令：

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>取消註冊機器

如果您打算停止使用 Azure 中的支援服務來管理機器，請執行下列步驟，向適用於伺服器的 Arc (預覽) 取消註冊機器。 您可以在從機器移除 Connected Machine 代理程式之前或之後，執行這些步驟。

1. 移至 [Azure 入口網站](https://aka.ms/hybridmachineportal)來開啟適用於伺服器的 Azure Arc (預覽)。

2. 依序選取清單中的機器、省略號 ( **...** ) 和 [刪除]。

## <a name="update-or-remove-proxy-settings"></a>更新或移除 Proxy 設定

若要在部署之後將代理程式設定為透過 Proxy 伺服器來與服務通訊，或移除此設定，可使用下列其中一種方法來完成這項工作。

### <a name="windows"></a>Windows

若要設定 Proxy 伺服器環境變數，請執行下列命令：

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

若要設定代理程式停止透過 Proxy 伺服器進行通訊，請執行下列命令來移除 Proxy 伺服器環境變數，然後重新啟動代理程式服務：

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

若要設定 Proxy 伺服器，請從您將代理程式安裝套件下載至其中的目錄執行下列命令：

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

若要設定代理程式停止透過 Proxy 伺服器進行通訊，請執行下列命令來移除 Proxy 設定：

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>後續步驟

- 了解如何使用 [Azure 原則](../../governance/policy/overview.md)，針對例如 VM [來賓設定](../../governance/policy/concepts/guest-configuration.md)、確認機器回報至預期的 Log Analytics 工作區、使用 [Azure 監視器與 VM](../../azure-monitor/insights/vminsights-enable-policy.md) 啟用監視等等項目，管理您的機器。

- 深入了解 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或功能 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。