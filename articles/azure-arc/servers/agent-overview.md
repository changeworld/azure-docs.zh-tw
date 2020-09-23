---
title: Connected Machine Windows 代理程式概觀
description: 本文提供 Azure Arc 啟用的伺服器代理程式的詳細總覽，可支援監視混合式環境中裝載的虛擬機器。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 990b5999a8483c6417049ac5ab965843c2b13659
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908183"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Azure Arc 已啟用伺服器代理程式的總覽

啟用 Azure Arc 的伺服器連線的機器代理程式，可讓您在公司網路或其他雲端提供者上，管理裝載于 Azure 外部的 Windows 和 Linux 機器。 本文將詳細說明代理程式的概觀、系統和網路需求以及不同的部署方法。

## <a name="agent-component-details"></a>代理程式元件詳細資料

Azure Connected Machine 代理程式套件包含數個邏輯元件，這些元件會一起配套在一起。

* 混合式實例中繼資料服務 (HIMDS) 管理與 Azure 的連線，以及連線電腦的 Azure 身分識別。

* 來賓設定代理程式提供來賓原則和來賓設定功能，例如評定電腦是否符合必要的原則。

    請注意下列與已中斷連線電腦的 Azure 原則 [來賓](../../governance/policy/concepts/guest-configuration.md) 設定的行為：

    * 以已中斷連線的電腦為目標的來賓設定原則指派不會受到影響。
    * 來賓指派會儲存在本機14天。 在14天的期間內，如果連接的機器代理程式重新連接到服務，則會重新套用原則指派。
    * 指派會在14天后刪除，在14天的期間內不會重新指派給電腦。

* 擴充代理程式會管理 VM 擴充功能，包括安裝、卸載和升級。 系統會從 Azure 下載擴充功能，並將其複製到 `%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads` Windows 上的資料夾，以及從 Linux 複製到 `/opt/GC_Ext/downloads` 。 在 Windows 上，延伸模組會安裝到下列路徑 `%SystemDrive%\Packages\Plugins\<extension>` ，而在 Linux 上則是安裝擴充功能 `/var/lib/waagent/<extension>` 。

## <a name="download-agents"></a>下載代理程式

您可以從下列位置下載適用於 Windows 和 Linux 的 Azure Connected Machine 代理程式套件。

* 您可以從 Microsoft 下載中心取得 [Windows 代理程式的 Windows Installer 套件](https://aka.ms/AzureConnectedMachineAgent)。

* Linux 代理程式套件會使用散發所慣用的套件格式 (.RPM 或 .DEB)，透過 Microsoft 的[套件存放庫](https://packages.microsoft.com/)來散發代理程式。

您可以根據需求，以手動或自動方式將適用於 Windows 和 Linux 的 Azure Connected Machine 代理程式升級為最新版本。 如需詳細資訊，請參閱[這裡](manage-agent.md)。

## <a name="prerequisites"></a>Prerequisites

### <a name="supported-operating-systems"></a>支援的作業系統

Azure Connected Machine 代理程式可正式支援下列 Windows 和 Linux 作業系統版本： 

- Windows Server 2012 R2 和更新版本 (包括 Windows Server Core)
- Ubuntu 16.04 和 18.04 LTS (x64) 
- CentOS Linux 7 (x64) 
- SUSE Linux Enterprise Server (SLES) 15 (x64) 
- Red Hat Enterprise Linux (RHEL) 7 (x64) 
- Amazon Linux 2 (x64) 

### <a name="required-permissions"></a>所需的權限

* 若要使電腦上線，您必須是 **Azure Connected Machine 上線**角色的成員。

* 若要讀取、修改、重新上架和刪除電腦，您是 **Azure Connected Machine 資源管理員** 角色的成員。 

### <a name="azure-subscription-and-service-limits"></a>Azure 訂用帳戶與服務限制

在使用已啟用 Azure Arc 的伺服器設定電腦之前，請先參閱 Azure Resource Manager 訂用帳戶 [限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 和 [資源群組限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) ，以規劃要連線的機器數目。

### <a name="transport-layer-security-12-protocol"></a>傳輸層安全性1.2 通訊協定

為了確保資料傳送至 Azure 時的安全性，我們強烈建議您將機器設定為使用傳輸層安全性 (TLS) 1.2。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。

|平台/語言 | 支援 | 相關資訊 |
| --- | --- | --- |
|Linux | Linux 發行版本通常會依賴 [OpenSSL](https://www.openssl.org) 來取得 TLS 1.2 支援。 | 請檢查 [OpenSSL 變更記錄](https://www.openssl.org/news/changelog.html)來確認支援的 OpenSSL 版本。|
| Windows Server 2012 R2 及更高版本 | 支援，而且已預設為啟用。 | 請確認您仍在使用[預設設定](/windows-server/security/tls/tls-registry-settings)。|

### <a name="networking-configuration"></a>網路設定

適用於 Linux 和 Windows 的 Connected Machine 代理程式會透過 TCP 連接埠 443，安全地將訊息輸出到 Azure Arc。 如果機器連線至防火牆或 Proxy 伺服器以透過網際網路通訊，請檢閱下面的需求，以了解網路設定需求。

如果您的防火牆或 Proxy 伺服器已限制輸出連線，請確定下面所列 URL 並未遭到封鎖。 如果您只允許代理程式用來與服務通訊所需的 IP 範圍或網域名稱，您也必須允許存取下列服務標籤和 URL。

服務標籤：

* AzureActiveDirectory
* AzureTrafficManager

URL：

| 代理程式資源 | 描述 |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`agentserviceapi.azure-automation.net`|來賓組態|
|`*-agentservice-prod-1.azure-automation.net`|來賓組態|
|`*.guestconfiguration.azure.com` |來賓組態|
|`*.his.arc.azure.com`|混合式識別服務|

如需每個服務標籤/區域的 IP 位址清單，請參閱 JSON 檔案 - [Azure IP 範圍和服務標籤 – 公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。 Microsoft 會發佈每週更新，其中包含每個 Azure 服務和其使用的 IP 範圍。 如需詳細資訊，請參閱[服務標籤](../../virtual-network/security-overview.md#service-tags)。

除了服務標籤 IP 位址範圍資訊之外，上述表格中的 URL 也是必要的，因為大部分的服務目前都沒有服務標籤註冊。 因此，IP 位址可能會變更。 如果您的防火牆設定需要 IP 位址範圍，則應該使用 **AzureCloud** 服務標籤來允許存取所有 Azure 服務。 請勿停用這些 URL 的安全性監視或檢查，但允許這些 URL，如同其他網際網路流量。

### <a name="register-azure-resource-providers"></a>註冊 Azure 資源提供者

Azure Arc 啟用的伺服器相依于您訂用帳戶中的下列 Azure 資源提供者，才能使用此服務：

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

如果未登錄這些資源，您可以使用下列命令來登錄：

Azure PowerShell：

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI：

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

您也可以遵循 [Azure 入口網站](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)底下的步驟，使用 Azure 入口網站來註冊資源提供者。

## <a name="installation-and-configuration"></a>安裝和組態

您可以視需求使用不同的方法，將您混合式環境中的機器直接與 Azure 連線。 下表說明每個方法，您可以判斷哪個方法最適合您的組織。

| 方法 | 描述 |
|--------|-------------|
| 以互動方式 | 若要在一部或少數機器上手動安裝代理程式，請遵循[從 Azure 入口網站連線機器](onboard-portal.md)中的步驟。<br> 您可以在 Azure 入口網站中產生指令碼並在機器上執行該指令碼，以自動化代理程式的安裝和設定步驟。|
| 大規模 | 若要為多部機器安裝及設定代理程式，請遵循[使用服務主體連線機器](onboard-service-principal.md)。<br> 此方法會建立服務主體，以透過非互動的方式與機器連線。|
| 大規模 | 依照[使用 Windows PowerShell DSC](onboard-dsc.md) 中的方法，為多部機器安裝及設定代理程式。<br> 此方法會透過 PowerShell DSC，以非互動方式使用服務主體來與機器連線。 |

## <a name="connected-machine-agent-technical-overview"></a>連線的機器代理程式技術總覽

### <a name="windows-agent-installation-details"></a>Windows 代理程式安裝詳細資料

您可以使用下列三種方法之一，來安裝適用於 Windows 的 Connected Machine 代理程式：

* 按兩下 `AzureConnectedMachineAgent.msi` 檔案。
* 從命令殼層執行 Windows Installer 套件 `AzureConnectedMachineAgent.msi` 來手動執行。
* 從 PowerShell 工作階段使用已編寫指令碼的方法。

安裝適用於 Windows 的 Connected Machine 代理程式之後，會套用下列額外的全系統組態變更。

* 在安裝期間會建立下列安裝資料夾。

    |資料夾 |描述 |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |包含代理程式支援檔案的預設安裝路徑。|
    |%ProgramData%\AzureConnectedMachineAgent |包含代理程式組態檔。|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |包含取得的權杖。|
    |%ProgramData%\AzureConnectedMachineAgent\Config |包含代理程式組態檔 `agentconfig.json` 將其註冊資訊記錄到服務中。|
    |%SystemDrive%\Program Files\ArcConnectedMachineAgent\ExtensionService\GC | 包含來賓設定代理程式檔案的安裝路徑。 |
    |%ProgramData%\GuestConfig |包含從 Azure 套用) 原則的 (。|
    |%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads | 延伸模組會從 Azure 下載並複製到此處。|

* 在安裝代理程式期間，會在目標電腦上建立下列 Windows 服務。

    |服務名稱 |顯示名稱 |程序名稱 |描述 |
    |-------------|-------------|-------------|------------|
    |himds |Azure 混合式 Instance Metadata Service |himds.exe |此服務會將 Azure Instance Metadata service (IMDS) ，以管理與 Azure 的連線，以及連線電腦的 Azure 身分識別。|
    |DscService |來賓設定服務 |dsc_service.exe |Desired State Configuration (DSC v2) 程式碼基底，可在 Azure 內部用來執行來賓原則。|

* 下列環境變數是在代理程式安裝期間所建立。

    |名稱 |預設值 |描述 |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 有數個記錄檔可供進行疑難排解。 如下表中所述。

    |Log |描述 |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |記錄代理程式 (HIMDS) 服務和與 Azure 互動的詳細資料。|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |使用 verbose (-v) 引數時，包含 azcmagent 工具命令的輸出。|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |記錄 DSC 服務活動的詳細資料，<br> 尤其是 HIMDS 服務與 Azure 原則之間的連線能力。|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |記錄有關 DSC 服務遙測和詳細資訊記錄的詳細資料。|
    |%SystemDrive%\ProgramData\GuestConfig\ ext_mgr_logs|記錄有關延伸模組代理程式元件的詳細資料。|
    |%SystemDrive%\ProgramData\GuestConfig\ extension_logs\<Extension>|記錄已安裝之延伸模組的詳細資料。|

* 會建立**混合式代理程式延伸模組應用程式**的本機安全性群組。

* 解除安裝代理程式期間，不會移除下列成品。

    * %ProgramFiles%\AzureConnectedMachineAgent\Logs
    * %ProgramData%\AzureConnectedMachineAgent 和子目錄
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux 代理程式安裝詳細資料

適用於 Linux 的 Connected Machine 代理程式會以發行版本慣用的套件格式來提供 (.RPM 或 .DEB)，其裝載位置在 Microsoft 的[套件存放庫](https://packages.microsoft.com/)。 代理程式已安裝並使用殼層指令碼套件組合 [Install_linux_azcmagent.sh](https://aka.ms/azcmagent)進行設定。

安裝適用於 Linux 的 Connected Machine 代理程式之後，會套用下列額外的全系統組態變更。

* 在安裝期間會建立下列安裝資料夾。

    |資料夾 |描述 |
    |-------|------------|
    |/var/opt/azcmagent/ |包含代理程式支援檔案的預設安裝路徑。|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | 包含來賓設定代理程式檔案的安裝路徑。|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |包含取得的權杖。|
    |/var/lib/GuestConfig |包含從 Azure 套用) 原則的 (。|
    |/opt/GC_Ext/downloads|延伸模組會從 Azure 下載並複製到此處。|

* 在安裝代理程式期間，會在目標電腦上建立下列精靈。

    |服務名稱 |顯示名稱 |程序名稱 |描述 |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure 混合式 Instance Metadata Service |/opt/azcmagent/bin/himds |此服務會將 Azure Instance Metadata service (IMDS) ，以管理與 Azure 的連線，以及連線電腦的 Azure 身分識別。|
    |dscd.service |來賓設定服務 |/opt/DSC/dsc_linux_service |這是在 Azure 內部用來實作客體內原則的 Desired State Configuration (DSC v2) 程式碼基底。|

* 有數個記錄檔可供進行疑難排解。 如下表中所述。

    |Log |描述 |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |記錄代理程式 (HIMDS) 服務和與 Azure 互動的詳細資料。|
    |/var/opt/azcmagent/log/azcmagent.log |使用 verbose (-v) 引數時，包含 azcmagent 工具命令的輸出。|
    |/opt/logs/dsc.log |記錄 DSC 服務活動的詳細資料，<br> 特別是 himds 服務和 Azure 原則之間的連線能。|
    |/opt/logs/dsc.telemetry.txt |記錄有關 DSC 服務遙測和詳細資訊記錄的詳細資料。|
    |/var/lib/GuestConfig/ext_mgr_logs |記錄有關延伸模組代理程式元件的詳細資料。|
    |/var/log/GuestConfig/extension_logs|記錄已安裝之延伸模組的詳細資料。|

* 下列環境變數是在代理程式安裝期間所建立。 這些變數是在 `/lib/systemd/system.conf.d/azcmagent.conf` 中進行設定。

    |名稱 |預設值 |描述 |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 解除安裝代理程式期間，不會移除下列成品。

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>下一步

若要開始評估已啟用 Azure Arc 的伺服器，請遵循 [從 Azure 入口網站將混合式機器連線到 Azure](onboard-portal.md)的文章。
