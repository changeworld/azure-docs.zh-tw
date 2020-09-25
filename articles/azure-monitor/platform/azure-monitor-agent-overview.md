---
title: Azure 監視器代理程式總覽
description: 概述 Azure 監視器代理程式 (AMA) ，它會從虛擬機器的客體作業系統收集監視資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: cc49bec71f6c591ca3036592b0949e3fc7cef48e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263771"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure 監視器代理程式總覽 (預覽) 
Azure 監視器代理程式 (AMA) 會從虛擬機器的客體作業系統收集監視資料，並將其傳遞至 Azure 監視器。 本文概述 Azure 監視器代理程式，包括如何安裝，以及如何設定資料收集。

## <a name="relationship-to-other-agents"></a>與其他代理程式的關聯性
Azure 監視器代理程式會取代 Azure 監視器目前用來從虛擬機器收集來賓資料的下列代理程式：

- [Log analytics 代理程式](log-analytics-agent.md) -將資料傳送至 log analytics 工作區，並支援適用於 VM 的 Azure 監視器與監視解決方案。
- [診斷擴充](diagnostics-extension-overview.md) 功能-僅將資料傳送至 (Windows) 、Azure 事件中樞和 Azure 儲存體的 Azure 監視器計量。
- [Telegraf 代理](collect-custom-metrics-linux-telegraf.md) 程式-僅將資料傳送至 Azure 監視器計量 (Linux) 。

除了將這項功能合併成單一代理程式之外，Azure 監視器代理程式還可針對現有的代理程式提供下列優點：

- 監視的範圍。 從不同的 Vm 集合集中設定集合的不同資料集。  
- Linux 多路連接：將資料從 Linux Vm 傳送至多個工作區。
- Windows 事件篩選：使用 XPATH 查詢來篩選要收集的 Windows 事件。
- 改良的延伸模組管理： Azure 監視器代理程式會使用新的方法來處理擴充性，此方法在目前的 Log Analytics 代理程式中會比管理元件和 Linux 外掛程式更透明且可控制。

### <a name="changes-in-data-collection"></a>資料收集中的變更
針對現有的代理程式定義資料收集的方法與彼此截然不同，而且每個方法都有 Azure 監視器代理程式所解決的挑戰。

- Log Analytics 代理程式會從 Log Analytics 工作區取得其設定。 這很容易集中設定，但很難為不同的虛擬機器定義獨立定義。 它只能將資料傳送至 Log Analytics 工作區。

- 診斷擴充功能具有每部虛擬機器的設定。 您可以輕鬆地為不同的虛擬機器定義獨立定義，但難以集中管理。 它只能傳送資料給 Azure 監視器計量、Azure 事件中樞或 Azure 儲存體。 針對 Linux 代理程式，必須要有開放原始碼 Telegraf 代理程式，才能將資料傳送至 Azure 監視器計量。

Azure 監視器代理程式會使用 [資料收集規則 (DCR) ](data-collection-rule-overview.md) 來設定要從每個代理程式收集的資料。 資料收集規則可大規模管理集合設定，同時仍能針對電腦子集啟用獨特的範圍設定。 它們獨立于工作區，而且不受虛擬機器影響，可讓它們定義一次，並在電腦和環境中重複使用。 請參閱 [設定 Azure 監視器代理程式 (預覽) 的資料收集 ](data-collection-rule-azure-monitor-agent.md)。

## <a name="should-i-switch-to-azure-monitor-agent"></a>我應該切換到 Azure 監視器代理程式嗎？
Azure 監視器 agent 共存與正式運作的 [代理程式進行 Azure 監視器](agents-overview.md)，但您可以考慮在 Azure 監視器代理程式公開預覽期間，將 vm 從目前的代理程式轉換。 進行這項決定時，請考慮下列因素。

- **環境需求。** Azure 監視器代理程式所擁有的一組支援的作業系統、環境和網路需求，會比目前的代理程式更有限。 未來的環境支援（例如新的作業系統版本和網路需求的類型）最可能只會在 Azure 監視器代理程式中提供。 您應該評估 Azure 監視器代理程式是否支援您的環境。 如果沒有，您就必須保持目前的代理程式。 如果 Azure 監視器代理程式支援您目前的環境，則您應該考慮轉換為它。
- **公開預覽風險容忍度。** 雖然 Azure 監視器代理程式已針對目前支援的案例進行徹底測試，但代理程式仍處於公開預覽狀態。 版本更新和功能改進會頻繁發生，而且可能會導致 bug。 您應在可能停止資料收集的 Vm 上，評估代理程式錯誤的風險。 如果資料收集的間隔不會對您的服務造成重大影響，請繼續 Azure 監視器代理程式。 如果您對於任何不穩定的承受度有低的承受度，則應該保持正式推出的代理程式，直到 Azure 監視器代理程式達到此狀態為止。
- **目前和新的功能需求。** Azure 監視器代理程式導入了數種新功能，例如篩選、範圍設定和多路連接，但目前的代理程式尚不會與其他功能（例如自訂記錄檔收集和解決方案整合）的同位檢查。 Azure 監視器中的最新功能只會提供給 Azure 監視器代理程式使用，因此，在新的代理程式中只會提供更多的功能。 您應該考慮 Azure 監視器代理程式是否具有您所需的功能，以及是否有一些功能可以暫時進行，而不需要在新的代理程式中取得其他重要功能。 如果 Azure 監視器代理程式具有您所需的所有核心功能，則請考慮轉換為它。 如果您需要重要的功能，請繼續進行目前的代理程式，直到 Azure 監視器代理程式達到同位。
- **改編的容錯。** 如果您要使用部署腳本和上線範本等資源來設定新的環境，您應該考慮當 Azure 監視器的代理程式正式推出時，您是否能夠加以修改。 如果這種重做的工作很短，請立即與目前的代理程式保持聯繫。 如果需要大量的工作，請考慮使用新的代理程式來設定新的環境。 Azure 監視器代理程式預計會正式推出，並為2021中的 Log Analytics 代理程式發佈淘汰日期。 當淘汰開始時，將支援目前的代理程式數年。



## <a name="current-limitations"></a>目前的限制
Azure 監視器代理程式的公開預覽期間，適用下列限制：

- Azure 監視器代理程式不支援解決方案和見解，例如適用於 VM 的 Azure 監視器和 Azure 資訊安全中心。 目前唯一支援的案例是使用您所設定的資料收集規則來收集資料。 
- 資料收集規則必須建立在與做為目的地的任何 Log Analytics 工作區相同的區域中。
- 目前僅支援 Azure 虛擬機器。 目前不支援內部部署虛擬機器、虛擬機器擴展集、伺服器的 Arc、Azure Kubernetes Service 和其他計算資源類型。
- 虛擬機器必須具有下列 HTTPS 端點的存取權：
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>與其他代理程式共存
Azure 監視器代理程式可以與現有的代理程式並存，讓您可以在評估或遷移期間繼續使用其現有的功能。 這一點特別重要，因為支援現有解決方案的公開預覽版有所限制。 在收集重複的資料時，您應該要特別小心，因為這可能會扭曲查詢結果，並導致資料內嵌和保留的額外費用。

例如，適用於 VM 的 Azure 監視器使用 Log Analytics 代理程式將效能資料傳送至 Log Analytics 工作區。 您也可以設定工作區，從代理程式收集 Windows 事件和 Syslog 事件。 如果您安裝 Azure 監視器代理程式，並為這些相同的事件和效能資料建立資料集合規則，則會產生重複的資料。


## <a name="costs"></a>成本
Azure 監視器代理程式不會產生任何費用，但您可能會產生資料內嵌的費用。 如需 Log Analytics 資料收集和保留和客戶計量的詳細資料，請參閱 [Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/) 。

## <a name="data-sources-and-destinations"></a>資料來源和目的地
下表列出您目前可以使用資料收集規則和您可以在其中傳送資料的 Azure 監視器代理程式所收集的資料類型。 查看 [Azure 監視器所監視的內容](../monitor-reference.md) ，以取得深入解析、解決方案和其他使用 Azure 監視器代理程式來收集其他資料類型的解決方案的清單。


Azure 監視器代理程式會將資料傳送至 Azure 監視器計量或支援 Azure 監視器記錄的 Log Analytics 工作區。

| 資料來源 | Destinations | 說明 |
|:---|:---|:---|
| 效能        | Azure 監視器計量<br>Log Analytics 工作區 | 測量作業系統和工作負載不同層面效能的數值。 |
| Windows 事件記錄檔 | Log Analytics 工作區 | 傳送至 Windows 事件記錄系統的資訊。 |
| syslog             | Log Analytics 工作區 | 傳送至 Linux 事件記錄系統的資訊。 |


## <a name="supported-operating-systems"></a>支援的作業系統
請參閱 [支援的作業系統](agents-overview.md#supported-operating-systems) ，以取得 Log Analytics 代理程式目前支援的 Windows 和 Linux 作業系統版本清單。



## <a name="security"></a>安全性
Azure 監視器代理程式不需要任何金鑰，而是需要 [系統指派的受控識別](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。 部署代理程式之前，您必須在每部虛擬機器上啟用系統指派的受控識別。

## <a name="networking"></a>網路功能
Azure 監視器代理程式支援 Azure 服務標籤 (需要 AzureMonitor 和 AzureResourceManager 標記，) 但尚未使用 Azure 監視器 Private Link 範圍或直接 proxy。

## <a name="install-the-azure-monitor-agent"></a>安裝 Azure 監視器代理程式
Azure 監視器代理程式會實作為 [AZURE VM 擴充](../../virtual-machines/extensions/overview.md) 功能，並包含下表中的詳細資料。 

| 屬性 | Windows | Linux |
|:---|:---|:---|
| 發行者 | Microsoft Azure 監視器  | Microsoft Azure 監視器 |
| 類型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

使用任何方法安裝 Azure 監視器代理程式，以使用 PowerShell 或 CLI 安裝虛擬機器代理程式，包括下列各項。 或者，您可以使用入口網站來安裝代理程式，並在 Azure 訂用帳戶中的虛擬機器上設定資料收集，方法是使用「 [設定 Azure 監視器代理程式的資料收集 (預覽版) ](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal)中所述的程式。

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>後續步驟

- [建立資料集合規則](data-collection-rule-azure-monitor-agent.md) ，以從代理程式收集資料，並將資料傳送至 Azure 監視器。
