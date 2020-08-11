---
title: Azure 監視器代理程式總覽
description: Azure 監視器代理程式 (AMA) 的總覽，它會從虛擬機器的客體作業系統收集監視資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: e38d59ff1eb31dd5fc3ecf6b7df6b12504141d5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082970"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure 監視器代理程式總覽 (預覽) 
Azure 監視器代理程式 (AMA) 會從虛擬機器的客體作業系統收集監視資料，並將它傳遞給 Azure 監視器。 這篇文章提供 Azure 監視器代理程式的總覽，包括如何安裝它，以及如何設定資料收集。

## <a name="relationship-to-other-agents"></a>與其他代理程式的關聯性
Azure 監視器代理程式會取代 Azure 監視器目前使用的下列代理程式，以收集來自虛擬機器的來賓資料：

- [Log analytics 代理](log-analytics-agent.md)程式-將資料傳送至 Log analytics 工作區，並支援適用於 VM 的 Azure 監視器和監視解決方案。
- [診斷擴充](diagnostics-extension-overview.md)功能-僅 (Windows) 、Azure 事件中樞和 Azure 儲存體，將資料傳送至 Azure 監視器計量。
- [Telegraf 代理](collect-custom-metrics-linux-telegraf.md)程式-僅 (Linux) ，將資料傳送至 Azure 監視器計量。

除了將此功能合併到單一代理程式之外，Azure 監視器代理程式還能為現有的代理程式提供下列優點：

- 監視的範圍。 集中設定不同 Vm 集的不同資料集集合。  
- Linux 多路連接：將資料從 Linux Vm 傳送至多個工作區。
- Windows 事件篩選：使用 XPATH 查詢來篩選要收集的 Windows 事件。
- 改良的延伸模組管理： Azure 監視器代理程式會使用新的方法來處理擴充性，這是比目前 Log Analytics 代理程式中的管理元件和 Linux 外掛程式更透明且可控制的延伸。

### <a name="changes-in-data-collection"></a>資料收集中的變更
定義現有代理程式之資料收集的方法與彼此之間的差異截然不同，而且每個方法都有 Azure 監視器代理程式所解決的挑戰。

- Log Analytics 代理程式會從 Log Analytics 工作區取得其設定。 這很容易集中設定，但很難定義不同虛擬機器的獨立定義。 它只能將資料傳送至 Log Analytics 工作區。

- 診斷擴充功能具有每部虛擬機器的設定。 這很容易定義不同虛擬機器的獨立定義，但很難集中管理。 它只能將資料傳送至 Azure 監視器計量、Azure 事件中樞或 Azure 儲存體。 針對 Linux 代理程式，必須要有開放原始碼 Telegraf 代理程式，才能將資料傳送至 Azure 監視器計量。

Azure 監視器代理程式會使用[資料收集規則 (DCR) ](data-collection-rule-overview.md)來設定要從每個代理程式收集的資料。 資料收集規則可進行大規模的集合設定管理，同時仍能針對電腦的子集啟用唯一的範圍設定。 它們與工作區無關，而且不受虛擬機器影響，可讓它們定義一次並在機器和環境中重複使用。 請參閱[設定 Azure 監視器代理程式 (預覽) 的資料收集](data-collection-rule-azure-monitor-agent.md)。



## <a name="current-limitations"></a>目前的限制
下列限制適用于 Azure 監視器代理程式的公開預覽期間：

- Azure 監視器代理程式不支援解決方案和深入解析，例如適用於 VM 的 Azure 監視器和 Azure 資訊安全中心。 目前唯一支援的案例是使用您所設定的資料集合規則來收集資料。 
- 資料收集規則必須在與做為目的地使用的任何 Log Analytics 工作區相同的區域中建立。
- 目前僅支援 Azure 虛擬機器。 目前不支援內部部署虛擬機器、虛擬機器擴展集、伺服器的 Arc、Azure Kubernetes Service 和其他計算資源類型。
- 虛擬機器必須具有下列 HTTPS 端點的存取權：
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>與其他代理程式共存
Azure 監視器代理程式可以與現有的代理程式並存，讓您在評估或遷移期間可以繼續使用其現有的功能。 這點特別重要，因為支援現有解決方案的公開預覽限制。 您應該小心收集重複的資料，因為這可能會扭曲查詢結果，並導致資料內嵌和保留的額外費用。

例如，適用於 VM 的 Azure 監視器使用 Log Analytics 代理程式，將效能資料傳送至 Log Analytics 工作區。 您也可以將工作區設定為從代理程式收集 Windows 事件和 Syslog 事件。 如果您安裝 Azure 監視器代理程式，並為這些相同的事件和效能資料建立資料集合規則，將會產生重複的資料。


## <a name="costs"></a>成本
Azure 監視器代理程式沒有成本，但您可能會產生資料內嵌的費用。 如需 Log Analytics 資料收集和保留以及客戶計量的詳細資訊，請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="data-sources-and-destinations"></a>資料來源和目的地
下表列出您目前可以使用資料收集規則與 Azure 監視器代理程式一起收集的資料類型，以及您可以傳送該資料的位置。 如需深入解析、解決方案和其他使用 Azure 監視器代理程式來收集其他資料類型的解決方案清單，請參閱[Azure 監視器監視哪些專案？](../monitor-reference.md) 。


Azure 監視器代理程式會將資料傳送至 Azure 監視器計量，或支援 Azure 監視器記錄的 Log Analytics 工作區。

| 資料來源 | Destinations | 描述 |
|:---|:---|:---|
| 效能        | Azure 監視器計量<br>Log Analytics 工作區 | 測量作業系統和工作負載不同層面效能的數值。 |
| Windows 事件記錄檔 | Log Analytics 工作區 | 傳送至 Windows 事件記錄系統的資訊。 |
| syslog             | Log Analytics 工作區 | 傳送至 Linux 事件記錄系統的資訊。 |


## <a name="supported-operating-systems"></a>支援的作業系統
Azure 監視器代理程式目前支援下列作業系統。

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>、7
  - Debian 9、10
  - Oracle Linux 6<sup>1</sup>、7
  - RHEL 6<sup>1</sup>、7、8
  - SLES 11、12、15
  - Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS

> [!IMPORTANT]
> <sup>1</sup>若要讓這些發行版本傳送 Syslog 資料，您必須移除 rsyslog 並安裝 syslog-ng。


## <a name="security"></a>安全性
Azure 監視器代理程式不需要任何金鑰，而是需要[系統指派的受控識別](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。 在部署代理程式之前，您必須先在每部虛擬機器上啟用系統指派的受控識別。


## <a name="install-the-azure-monitor-agent"></a>安裝 Azure 監視器代理程式
Azure 監視器代理程式會實作為[AZURE VM 擴充](../../virtual-machines/extensions/overview.md)功能，並具有下表中的詳細資料。 

| 屬性 | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure 監視器  | Microsoft. Azure 監視器 |
| 類型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 0.9 |

使用任何方法安裝 Azure 監視器代理程式，以使用 PowerShell 或 CLI 安裝包含下列的虛擬機器代理程式。 或者，您可以使用入口網站來安裝代理程式，並在 Azure 訂用帳戶中的虛擬機器上設定資料收集，以及[設定 Azure 監視器代理程式 (preview) 的資料收集](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal)中所述的步驟。

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --version 1.0 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -Version 1.0 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --version 0.9 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -Version 0.9 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>後續步驟

- [建立資料收集規則](data-collection-rule-azure-monitor-agent.md)，以從代理程式收集資料，並將它傳送至 Azure 監視器。
