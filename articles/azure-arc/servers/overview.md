---
title: 適用於伺服器的 Azure Arc (預覽) 概觀
description: 了解如何使用適用於伺服器的 Azure Arc 來管理裝載於 Azure 外部的機器，就如同管理 Azure 資源一樣。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure 自動化, DSC, powershell, Desired State Configuration, 更新管理, 變更追蹤, 清查, Runbook, python, 圖形, 混合式
ms.date: 02/24/2020
ms.topic: overview
ms.openlocfilehash: 57b44db9c1bb9a607ad8478b7208df40441020c2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79290126"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>什麼是適用於伺服器的 Azure Arc (預覽)

適用於伺服器的 Azure Arc (預覽) 可讓您在公司網路或其他雲端提供者上管理裝載於 Azure 外部的 Windows 和 Linux 機器，就如同您管理原生 Azure 虛擬機器一樣。 混合式機器連線到 Azure 時就會變成已連線的機器，並且視為 Azure 中的資源。 每個已連線的機器都有資源識別碼，可在訂用帳戶內作為資源群組的一部分來管理，並可從標準的 Azure 結構中 (例如 Azure 原則和套用標籤) 獲益。

若要對裝載於 Azure 外部的混合式機器提供這項體驗，您必須在計畫連線到 Azure 的每部機器上安裝 Azure Connected Machine 代理程式。 此代理程式不會提供任何其他功能，也不會取代 Azure [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。

>[!NOTE]
>此預覽版本主要用於評估，不建議用來管理重要的生產機器。
>

## <a name="supported-scenarios"></a>支援的案例

適用於伺服器的 Azure Arc (預覽) 支援對已連線的機器執行下列案例：

- 使用與 Azure 虛擬機器原則指派相同的體驗來指派 [Azure 原則客體設定](../../governance/policy/concepts/guest-configuration.md)。
- 如果記錄資料由 Log Analytics 代理程式所收集，並且儲存在機器註冊的 Log Analytics 工作區中，則現在會包含機器專屬的屬性 (例如資源識別碼)，以用來支援[資源內容](../../azure-monitor/platform/design-logs-deployment.md#access-mode)記錄存取。

## <a name="supported-regions"></a>支援區域

使用適用於伺服器的 Azure Arc (預覽) 時，只有特定區域可受到支援：

- WestUS2
- WestEurope
- WestAsia

在大部分情況下，您在建立安裝指令碼時所選取的位置，應該是地理位置最接近機器位置的 Azure 區域。 待用資料會儲存在包含您所指定區域的 Azure 地理位置中，如果您有資料落地需求，這可能也會影響選擇的區域。 如果您的機器所連線的 Azure 區域受到中斷影響，連線的機器不會受到影響，但使用 Azure 的管理作業可能無法完成。 在發生區域性中斷的情況下，如果您有多個位置可提供異地備援服務，最好將每個位置中的機器連線到不同的 Azure 區域。

## <a name="prerequisites"></a>Prerequisites

### <a name="supported-operating-systems"></a>支援的作業系統

Azure Connected Machine 代理程式可正式支援下列 Windows 和 Linux 作業系統版本： 

- Windows Server 2012 R2 和更新版本 (包括 Windows Server Core)
- Ubuntu 16.04 和 18.04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 7

>[!NOTE]
>此適用於 Windows 的 Connected Machine 代理程式預覽版本僅支援設定為使用英文語言的 Windows Server。
>

### <a name="required-permissions"></a>所需的權限

- 若要使電腦上線，您必須是 **Azure Connected Machine 上線**角色的成員。

- 若要讀取、修改、重新上線和刪除機器，您必須是 **Azure Connected Machine 資源管理員**角色的成員。 

### <a name="azure-subscription-and-service-limits"></a>Azure 訂用帳戶與服務限制

使用適用於伺服器的 Azure Arc (預覽) 設定您的電腦之前，您應先檢查 Azure Resource Manager 的[訂用帳戶限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)和[資源群組限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)，以規劃要連線的機器數目。

## <a name="tls-12-protocol"></a>TLS 1.2 通訊協定

為了確保資料傳送至 Azure 時的安全性，我們強烈建議您將機器設定為使用傳輸層安全性 (TLS) 1.2。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。 

|平台/語言 | 支援 | 相關資訊 |
| --- | --- | --- |
|Linux | Linux 發行版本通常會依賴 [OpenSSL](https://www.openssl.org) 來取得 TLS 1.2 支援。 | 請檢查 [OpenSSL 變更記錄](https://www.openssl.org/news/changelog.html)來確認支援的 OpenSSL 版本。|
| Windows Server 2012 R2 及更高版本 | 支援，而且已預設為啟用。 | 請確認您仍在使用[預設設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)。|

### <a name="networking-configuration"></a>網路設定

適用於 Linux 和 Windows 的 Connected Machine 代理程式會透過 TCP 連接埠 443，安全地將訊息輸出到 Azure Arc。 如果機器連線至防火牆或 Proxy 伺服器以透過網際網路通訊，請檢閱下面的需求，以了解網路設定需求。

如果您的防火牆或 Proxy 伺服器已限制輸出連線，請確定下面所列 URL 並未遭到封鎖。 如果您只允許代理程式用來與服務通訊所需的 IP 範圍或網域名稱，您也必須允許存取下列服務標籤和 URL。

服務標籤：

- AzureActiveDirectory
- AzureTrafficManager

URL：

| 代理程式資源 | 描述 |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|來賓組態|
|*-agentservice-prod-1.azure-automation.net|來賓組態|
|*.his.hybridcompute.azure-automation.net|混合式識別服務|

如需每個服務標籤/區域的 IP 位址清單，請參閱 JSON 檔案 - [Azure IP 範圍和服務標籤 – 公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。 Microsoft 會發佈每週更新，其中包含每個 Azure 服務和其使用的 IP 範圍。 如需詳細資訊，請參閱[服務標籤](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。

除了服務標籤 IP 位址範圍資訊之外，上述表格中的 URL 也是必要的，因為大部分的服務目前都沒有服務標籤註冊。 因此，IP 位址可能會變更。 如果您的防火牆設定需要 IP 位址範圍，則應該使用 **AzureCloud** 服務標籤來允許存取所有 Azure 服務。 請勿停用這些 URL 的安全性監視或檢查，但允許這些 URL，如同其他網際網路流量。

### <a name="register-azure-resource-providers"></a>註冊 Azure 資源提供者

適用於伺服器的 Azure Arc (預覽) 需依賴您訂用帳戶中的下列 Azure 資源提供者來使用此服務：

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

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

## <a name="connected-machine-agent"></a>Connected Machine 代理程式

您可以從下列位置下載適用於 Windows 和 Linux 的 Azure Connected Machine 代理程式套件。

- 您可以從 Microsoft 下載中心取得 [Windows 代理程式的 Windows Installer 套件](https://aka.ms/AzureConnectedMachineAgent)。
- Linux 代理程式套件會使用散發所慣用的套件格式 (.RPM 或 .DEB)，透過 Microsoft 的[套件存放庫](https://packages.microsoft.com/)來散發代理程式。

>[!NOTE]
>在此預覽期間，只發行了一個套件，其適用於 Ubuntu 16.04 或 18.04。

您可以根據需求，以手動或自動方式將適用於 Windows 和 Linux 的 Azure Connected Machine 代理程式升級為最新版本。 若使用 Windows，可以使用 Windows Update 自動完成代理程式更新；若使用 Ubuntu，則請使用 [apt](https://help.ubuntu.com/lts/serverguide/apt.html) 命令列工具。

### <a name="agent-status"></a>代理程式狀態

Connected Machine 代理程式每隔 5 分鐘會定期將活動訊號訊息傳送至服務。 如果 15 分鐘未收到訊息，則系統會將機器視為離線，且入口網站中的狀態會自動變更為**中斷連線**。 從 Connected Machine 代理程式收到後續的活動訊號訊息時，其狀態會自動變更為**連線**。

## <a name="install-and-configure-agent"></a>安裝及設定代理程式

您可以視需求使用不同的方法，將您混合式環境中的機器直接與 Azure 連線。 下表說明每個方法，您可以判斷哪個方法最適合您的組織。

| 方法 | 描述 |
|--------|-------------|
| 以互動方式 | 若要在一部或少數機器上手動安裝代理程式，請遵循[從 Azure 入口網站連線機器](onboard-portal.md)中的步驟。<br> 您可以在 Azure 入口網站中產生指令碼並在機器上執行該指令碼，以自動化代理程式的安裝和設定步驟。|
| 大規模 | 若要為多部機器安裝及設定代理程式，請遵循[使用服務主體連線機器](onboard-service-principal.md)。<br> 此方法會建立服務主體，以透過非互動的方式與機器連線。|

## <a name="next-steps"></a>後續步驟

- 若要開始評估適用於伺服器的 Azure Arc (預覽)，請遵循[從 Azure 入口網站將混合式機器連線到 Azure](onboard-portal.md) 一文。 