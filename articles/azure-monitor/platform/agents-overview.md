---
title: Azure 監視代理程式概觀 | Microsoft Docs
description: 本文將詳細說明可支援對裝載於 Azure 或混合式環境中的虛擬機器進行監視的 Azure 代理程式。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2020
ms.openlocfilehash: 8b9a79ee32d7a81fee64e40b85272877b7d87589
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674193"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure 監視器代理程式的總覽

虛擬機器和其他計算資源需要代理程式來收集測量其虛擬作業系統和工作負載效能和可用性所需的監視資料。 本文說明 Azure 監視器所使用的代理程式，並協助您判斷需要符合特定環境的需求。

> [!NOTE]
> Azure 監視器目前有多個代理程式，因為 Azure 監視器與 Log Analytics 的最新匯總。 雖然它們的功能可能會重迭，但各有獨特的功能。 視您的需求而定，您可能需要在虛擬機器上有一或多個代理程式。 

針對特定虛擬機器，您可能會有一組特定的需求無法完全符合。 例如，您可能會想要使用需要 Azure 診斷擴充功能但也想要利用適用於 VM 的 Azure 監視器的功能，這需要 Log Analytics 代理程式和 Dependency agent。 在這種情況下，您可以使用多個代理程式，這是一種常見的案例，適用于需要各項功能的客戶。

## <a name="summary-of-agents"></a>代理程式摘要

下表提供 Windows 和 Linux Azure 監視器代理程式的快速比較。 下一節會提供每個詳細資料的詳細資料。

> [!NOTE]
> Azure 監視器代理程式目前處於預覽狀態，但功能有限。 此資料表將會更新 

### <a name="windows-agents"></a>Windows 代理程式

| | Azure 監視器代理程式 (預覽) | 診斷<br>延伸模組 (WAD)  | Log Analytics<br>代理程式 | 相依性<br>代理程式 |
|:---|:---|:---|:---|:---|
| **支援的環境** | Azure<br>其他雲端 (Azure Arc) <br>內部部署 (Azure Arc)   | Azure | Azure<br>其他雲端<br>內部部署 | Azure<br>其他雲端<br>內部部署 | 
| **代理程式需求**  | 無 | 無 | 無 | 需要 Log Analytics 代理程式 |
| **收集的資料** | 事件記錄<br>效能 | 事件記錄<br>ETW 事件<br>效能<br>檔案型記錄<br>IIS 記錄<br>.NET 應用程式記錄檔<br>損毀傾印<br>代理程式診斷記錄 | 事件記錄<br>效能<br>檔案型記錄<br>IIS 記錄<br>見解和解決方案<br>其他服務 | 程序相依性<br>網路連接計量 |
| **傳送至的資料** | Azure 監視器記錄<br>Azure 監視器計量 | Azure 儲存體<br>Azure 監視器計量<br>事件中樞 | Azure 監視器記錄 | Azure 監視器記錄<br>透過 Log Analytics 代理程式 ()  |
| **服務和**<br>**特徵**<br>**支援** | Log Analytics<br>計量瀏覽器 | 計量瀏覽器 | 適用於 VM 的 Azure 監視器<br>Log Analytics<br>Azure 自動化<br>Azure 資訊安全中心<br>Azure Sentinel | 適用於 VM 的 Azure 監視器<br>服務對應 |

### <a name="linux-agents"></a>Linux 代理程式

| | Azure 監視器代理程式 (預覽) | 診斷<br>延伸模組 (LAD)  | Telegraf<br>代理程式 | Log Analytics<br>代理程式 | 相依性<br>代理程式 |
|:---|:---|:---|:---|:---|:---|
| **支援的環境** | Azure<br>其他雲端 (Azure Arc) <br>內部部署 (Arc 弧線)  | Azure | Azure<br>其他雲端<br>內部部署 | Azure<br>其他雲端<br>內部部署 | Azure<br>其他雲端<br>內部部署 |
| **代理程式需求**  | 無 | 無 | 無 | 無 | 需要 Log Analytics 代理程式 |
| **收集的資料** | syslog<br>效能 | syslog<br>效能 | 效能 | syslog<br>效能| 程序相依性<br>網路連接計量 |
| **傳送至的資料** | Azure 監視器記錄<br>Azure 監視器計量 | Azure 儲存體<br>事件中樞 | Azure 監視器計量 | Azure 監視器記錄 | Azure 監視器記錄<br>透過 Log Analytics 代理程式 ()  |
| **服務和**<br>**特徵**<br>**支援** | Log Analytics<br>計量瀏覽器 | | 計量瀏覽器 | 適用於 VM 的 Azure 監視器<br>Log Analytics<br>Azure 自動化<br>Azure 資訊安全中心<br>Azure Sentinel | 適用於 VM 的 Azure 監視器<br>服務對應 |


## <a name="azure-monitor-agent-preview"></a>Azure 監視器代理程式 (預覽)
[Azure 監視器代理程式](azure-monitor-agent-overview.md)目前為預覽狀態，會取代 Windows 和 Linux 虛擬機器的 Log Analytics 代理程式和 Telegraf 代理程式。 它可以將資料傳送至 Azure 監視器記錄和 Azure 監視器計量，並使用 [資料收集規則 (DCR) ](data-collection-rule-overview.md) ，以提供更具擴充性的方法來設定每個代理程式的資料收集和目的地。

如果您需要執行下列動作，請使用 Azure 監視器代理程式：

- 從 Azure、其他雲端或內部部署中的任何虛擬機器收集來賓記錄和計量。  (Azure 外部虛擬機器所需的 Azure Arc。 )  
- 將資料傳送至 Azure 監視器記錄和 Azure 監視器計量，以 Azure 監視器進行分析。 
- 將資料傳送至 Azure 儲存體以便封存。
- 使用 [Azure 事件中樞](diagnostics-extension-stream-event-hubs.md)將資料傳送至協力廠商工具。
- 使用 [Azure 資訊安全中心](../../security-center/security-center-introduction.md)  或 [Azure Sentinel](../../sentinel/overview.md)管理虛擬機器的安全性。  (無法在預覽中使用。 ) 

Azure 監視器代理程式的限制包括：

- 目前處於公開預覽狀態。 查看公開預覽期間的限制清單 [目前的限制](azure-monitor-agent-overview.md#current-limitations) 。

## <a name="log-analytics-agent"></a>Log Analytics 代理程式

[Log Analytics 代理程式](log-analytics-agent.md)會收集來自客體作業系統的監視資料，以及 Azure、其他雲端提供者和內部部署中虛擬機器的工作負載。 它會將資料傳送到 Log Analytics 工作區。 Log Analytics 代理程式是 System Center Operations Manager 所使用的相同代理程式，您可以多路連接代理程式電腦與管理群組通訊，並同時 Azure 監視器。 Azure 監視器和 Azure 中的其他服務的特定見解也需要此代理程式。


> [!NOTE]
> 適用于 Windows 的 Log Analytics 代理程式通常稱為 Microsoft Monitoring Agent (MMA) 。 適用于 Linux 的 Log Analytics 代理程式通常稱為 OMS 代理程式。



如果您需要下列事項，請使用 Log Analytics 代理程式：

* 從 Azure 內部或外部的虛擬或實體機器收集記錄和效能資料。 
* 將資料傳送至 Log Analytics 工作區，以利用 [Azure 監視器記錄](data-platform-logs.md) 檔（例如 [記錄查詢](../log-query/log-query-overview.md)）所支援的功能。
* 使用 [適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md) 可讓您大規模監視虛擬機器，並監視其進程和其他資源和外部進程的相依性。  
* 使用 [Azure 資訊安全中心](../../security-center/security-center-introduction.md)  或 [Azure Sentinel](../../sentinel/overview.md)管理虛擬機器的安全性。
* 使用 [Azure 自動化的更新管理](../../automation/update-management/overview.md)、 [Azure 自動化狀態設定](../../automation/automation-dsc-overview.md)或 [Azure 自動化變更追蹤和清查](../../automation/change-tracking/overview.md) ，以提供 Azure vm 的全方位管理
* 使用不同的 [解決方案](../monitor-reference.md#insights-and-core-solutions) 來監視特定的服務或應用程式。

Log Analytics 代理程式的限制包括：

- 無法將資料傳送至 Azure 監視器計量、Azure 儲存體或 Azure 事件中樞。
- 難以為個別代理程式設定唯一的監視定義。
- 因為每部虛擬機器都有獨特的設定，所以難以大規模管理。

## <a name="azure-diagnostics-extension"></a>Azure 診斷延伸模組

[Azure 診斷擴充](diagnostics-extension-overview.md)功能會從客體作業系統和 Azure 虛擬機器的工作負載，以及其他計算資源收集監視資料。 它主要會將資料收集到 Azure 儲存體，但也可讓您定義資料接收，也可將資料傳送到其他目的地，例如 Azure 監視器計量和 Azure 事件中樞。

如果您需要執行下列動作，請使用 Azure 診斷擴充功能：

- 將資料傳送至 Azure 儲存體進行封存，或使用 [Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)之類的工具來分析資料。
- 將資料傳送至 [Azure 監視器計量](data-platform-metrics.md) ，以使用 [計量瀏覽器](metrics-getting-started.md) 來分析資料，並利用近乎即時計量 [警示](./alerts-metric-overview.md) 和 [自動](autoscale-overview.md) 調整 (僅限 Windows) 的功能。
- 使用 [Azure 事件中樞](diagnostics-extension-stream-event-hubs.md)將資料傳送至協力廠商工具。
- 收集 [開機診斷](../../virtual-machines/troubleshooting/boot-diagnostics.md) 來調查 VM 開機問題。

Azure 診斷擴充功能的限制包括：

- 只能與 Azure 資源搭配使用。
- 將資料傳送至 Azure 監視器記錄的功能受限。

## <a name="telegraf-agent"></a>Telegraf 代理程式

[InfluxData Telegraf 代理程式](collect-custom-metrics-linux-telegraf.md)可用來收集 Linux 電腦的效能資料，以 Azure 監視器計量。

如果您需要執行下列動作，請使用 Telegraf 代理程式：

* 將資料傳送至 [Azure 監視器計量](data-platform-metrics.md) ，以使用 [計量瀏覽器](metrics-getting-started.md) 來分析資料，並利用接近即時計量 [警示](./alerts-metric-overview.md) 和 [自動](autoscale-overview.md) 調整 (Linux) 的功能。 



## <a name="dependency-agent"></a>相依性代理程式

相依性代理程式會收集在虛擬機器上執行之處理常式的探索資料，以及外部進程相依性的相關資訊。 

如果您需要執行下列動作，請使用 Dependency agent：

* 使用地圖功能 [適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md) 或 [服務對應](../insights/service-map.md) 方案。

使用相依性代理程式時，請考慮下列各項：

- 相依性代理程式需要在相同的虛擬機器上安裝 Log Analytics 代理程式。
- 在 Linux Vm 上，必須在 Azure 診斷擴充功能之前安裝 Log Analytics 代理程式。

## <a name="virtual-machine-extensions"></a>虛擬機器擴充功能

適用于 [Windows](../../virtual-machines/extensions/oms-windows.md) 和 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 log analytics 擴充功能會在 Azure 虛擬機器上安裝 log analytics 代理程式。 適用于 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 和 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 的 Azure 監視器相依性延伸模組會在 Azure 虛擬機器上安裝 dependency agent。 這些都是上述的相同代理程式，但可讓您透過 [虛擬機器擴充](../../virtual-machines/extensions/overview.md)功能來管理它們。 您應該盡可能使用延伸模組來安裝和管理代理程式。


## <a name="supported-operating-systems"></a>支援的作業系統
下表列出 Azure 監視器代理程式所支援的作業系統。 請參閱每個代理程式的檔，以瞭解獨特的考慮和安裝程式。 如需支援的作業系統，請參閱 Telegraf 檔。 所有作業系統都假設為 x64。 x86 不支援任何作業系統。

### <a name="windows"></a>Windows

| 作業系統 | Azure 監視器代理程式 | Log Analytics 代理程式 | 相依性代理程式 | 診斷擴充功能 | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br> (包括多會話) 和 Pro<br> (伺服器案例僅)   | X | X | X | X |
| Windows 8 企業版和專業版<br> (伺服器案例僅)   |   | X | X |   |
| Windows 7 SP1<br> (伺服器案例僅)                  |   | X | X |   |


### <a name="linux"></a>Linux

| 作業系統 | Azure 監視器代理程式 | Log Analytics 代理程式 | 相依性代理程式 | 診斷擴充功能 | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                     |   | X |   |   |
| CentOS Linux 8                                           |   | X |   |   |
| CentOS Linux 7                                           | X | X |   | X |
| CentOS Linux 7。8                                         | X | X | X | X |
| CentOS Linux 7。6                                         | X | X | X | X |
| CentOS Linux 6                                           |   | X |   |   |
| CentOS Linux 6.5 +                                        |   | X |   | X |
| Debian 10                                                | X |   |   |   |
| Debian 9                                                 | X | X | x | X |
| Debian 8                                                 |   | X | X | X |
| Debian 7                                                 |   |   |   | X |
| OpenSUSE 13.1+                                           |   |   |   | X |
| Oracle Linux 8                                           |   | X |   |   |
| Oracle Linux 7                                           | X | X |   | X |
| Oracle Linux 6                                           |   | X |   |   |
| Oracle Linux 6.4+                                        |   | X |   | X |
| Red Hat Enterprise Linux Server 8                        |   | X |   |   |
| Red Hat Enterprise Linux Server 7                        | X | X | X | X |
| Red Hat Enterprise Linux Server 6                        |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7 +                     |   | X | X | X |
| SUSE Linux Enterprise Server 15。1                        |   | X |   |   |
| SUSE Linux Enterprise Server 15                          | X | X |   |   |
| SUSE Linux Enterprise Server 12                          | X | X | X | X |
| Ubuntu 20.04 LTS                                         |   | X |   |   |
| Ubuntu 18.04 LTS                                         | X | X | X | X |
| Ubuntu 16.04 LTS                                         | X | X | X | X |
| Ubuntu 14.04 LTS                                         |   | X |   | X |


#### <a name="dependency-agent-linux-kernel-support"></a>Dependency agent Linux 核心支援
由於相依性代理程式是在核心層級運作，因此支援也會相依于核心版本。 下表列出相依性代理程式的主要和次要 Linux OS 版本，以及支援的核心版本。

| 散發 | OS 版本 | 核心版本 |
|:---|:---|:---|
|  Red Hat Linux 7   | 7.6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOSPlus         | 6.10    | 2.6.32-754.3。5<br>2.6.32-696.30。1 |
|                    | 6.9     | 2.6.32-696.30。1<br>2.6.32-696.18。7 |
| Ubuntu Server      | 18.04   | 5.3.0-1020<br>5.0 (包含 Azure 調整的核心) <br>4.18 *<br> 4.15* |
|                    | 16.04.3 | 4.15. * |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 12 SP4 | 4.12. * (包含 Azure 調整的核心)  |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4.9  | 


## <a name="next-steps"></a>後續步驟

在下列位置取得每個代理程式的詳細資料：

- [Log Analytics 代理程式總覽](log-analytics-agent.md)
- [Azure 診斷延伸模組概觀](diagnostics-extension-overview.md)
- [使用 InfluxData Telegraf 代理程式收集 Linux VM 的自訂計量](collect-custom-metrics-linux-telegraf.md)
