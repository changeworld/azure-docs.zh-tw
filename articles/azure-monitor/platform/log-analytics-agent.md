---
title: Log Analytics 代理程式概觀
description: 此主題可協助您了解如何使用 Log Analytics 來收集資料，並監視 Azure、內部部署或其他雲端環境中裝載的電腦。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 8a086830398555d962bb13d1d9b0fea3554f7924
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032515"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics 代理程式概觀
Azure Log Analytics 代理程式會從任何雲端中的 Windows 和 Linux 虛擬機器、內部部署電腦，以及受 [System Center Operations Manager](/system-center/scom/) 監視的遙測收集遙測資料，並將收集到的資料傳送到您的 Log Analytics 工作區 Azure 監視器。 Log Analytics 代理程式也支援 Azure 監視器中的深入解析和其他服務，例如[適用於 VM 的 Azure 監視器](../insights/vminsights-enable-overview.md)、[Azure 資訊安全中心](../../security-center/index.yml)和 [Azure 自動化](../../automation/automation-intro.md)。 本文提供代理程式、系統和網路需求，以及部署方法的詳細總覽。

> [!NOTE]
> 您也可能會看到稱為 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理程式的 Log Analytics 代理程式。

## <a name="comparison-to-azure-diagnostics-extension"></a>與 Azure 診斷擴充功能的比較
Azure 監視器中的 [Azure 診斷擴充功能](diagnostics-extension-overview.md)也可用來從 Azure 虛擬機器的客體作業系統收集監視資料。 視需求而定，可選擇使用其中一或兩種方式。 如需 Azure 監視器代理程式的詳細比較，請參閱 [Azure 監視器代理程式的概觀](agents-overview.md)。 

需要考慮的主要差異如下：

- Azure 診斷延伸模組只能與 Azure 虛擬機器搭配使用。 Log Analytics 代理程式可與 Azure、其他雲端和內部部署中的虛擬機器搭配使用。
- Azure 診斷延伸模組會將資料傳送至 Azure 儲存體、[Azure 監視器計量](data-platform-metrics.md) (僅限 Windows) 和事件中樞。 Log Analytics 代理程式會將資料傳送給 [Azure 監視器記錄](data-platform-logs.md)。
- [解決方案](../monitor-reference.md#insights-and-core-solutions)、[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)以及其他服務 (例如 [Azure 資訊安全中心](../../security-center/index.yml)) 都需要 Log Analytics 代理程式。

## <a name="costs"></a>費用
Log Analytics 代理程式沒有任何成本，但您可能會因為所擷取的資料而產生費用。 如需 Log Analytics 工作區中所收集資料的定價詳細資訊，請參閱[使用 Azure 監視器記錄來管理使用量和成本](manage-cost-storage.md)。

## <a name="supported-operating-systems"></a>支援的作業系統

 請參閱 [支援的作業系統](agents-overview.md#supported-operating-systems) ，以取得 Log Analytics 代理程式所支援的 Windows 和 Linux 作業系統版本清單。 


## <a name="data-collected"></a>收集的資料
下表列出您可以設定 Log Analytics 工作區以從所有連線代理程式收集的資料類型。 請參閱[Azure 監視器監視的項目為何？](../monitor-reference.md)，以取得深入解析、解決方案和其他會使用 Log Analytics 代理程式來收集其他類型資料的解決方案清單。

| 資料來源 | 描述 |
| --- | --- |
| [Windows 事件記錄](data-sources-windows-events.md) | 傳送至 Windows 事件記錄系統的資訊。 |
| [Syslog](data-sources-syslog.md)                     | 傳送至 Linux 事件記錄系統的資訊。 |
| [效能](data-sources-performance-counters.md)  | 測量作業系統和工作負載不同層面效能的數值。 |
| [IIS 記錄](data-sources-iis-logs.md)                 | 在客體作業系統上執行的 IIS 網站其使用量資訊。 |
| [自訂的記錄](data-sources-custom-logs.md)           | 來自 Windows 和 Linux 電腦上文字檔的事件。 |

## <a name="data-destinations"></a>資料目的地
Log Analytics 代理程式會將資料傳送至 Azure 監視器中的 Log Analytics 工作區。 Windows 代理程式可以有多重主目錄，以將資料傳送至多個工作區和 System Center Operations Manager 管理群組。 Linux 代理程式只能傳送至單一目的地，也就是工作區或管理群組。

## <a name="other-services"></a>其他服務
Linux 和 Windows 的代理程式不只用於連接到 Azure 監視器。 其他服務（例如 Azure 資訊安全中心和 Azure Sentinel 會依賴代理程式和其連線的 Log Analytics 工作區。 代理程式也支援 Azure 自動化來裝載混合式 Runbook 背景工作角色和其他服務，例如 [變更追蹤](../../automation/change-tracking.md)、 [更新管理](../../automation/update-management/update-mgmt-overview.md)和 [Azure 資訊安全中心](../../security-center/security-center-intro.md)。 如需有關「混合式 Runbook」背景工作角色的詳細資訊，請參閱 [Azure 自動化混合式 Runbook 背景工作](../../automation/automation-hybrid-runbook-worker.md)。  

## <a name="workspace-and-management-group-limitations"></a>工作區和管理群組限制

如需將代理程式連接到 Operations Manager 管理群組的詳細資訊，請參閱 [設定代理程式向 Operations Manager 管理群組報告](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) 。

* Windows 代理程式可以連接到最多四個工作區，即使它們已連線到 System Center Operations Manager 管理群組也一樣。
* Linux 代理程式不支援多路連接，而且只能連線到單一工作區或管理群組。
  

## <a name="security-limitations"></a>安全性限制

* Windows 和 Linux 代理程式支援 [FIPS 140 標準](/windows/security/threat-protection/fips-140-validation)，但 [可能不支援其他類型的強化](agent-linux.md#supported-linux-hardening)。


## <a name="installation-options"></a>安裝選項

根據需求而定，有多種方法可以安裝 Log Analytics 代理程式並將您的機器連線至 Azure 監視器。 下列各節列出不同類型的虛擬機器可能的方法。

### <a name="azure-virtual-machine"></a>Azure 虛擬機器

- [適用於 VM 的 Azure 監視器](../insights/vminsights-enable-overview.md) 提供多個可大規模啟用代理程式的方法。 這包括安裝 Log Analytics 代理程式和 Dependency agent。 
- Azure 資訊安全中心可以在所有支援的 Azure Vm 和任何新建立的 Azure Vm 上布建[Log Analytics 代理程式](../../security-center/security-center-enable-data-collection.md)，以監視是否有安全性弱點和威脅。
- 適用于 [Windows](../../virtual-machines/extensions/oms-windows.md) 或 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 Log Analytics VM 擴充功能可以隨 Azure 入口網站、Azure CLI、Azure PowerShell 或 Azure Resource Manager 範本一起安裝。
- [從 Azure 入口網站手動](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)安裝個別的 Azure 虛擬機器。


### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>內部部署或其他雲端中的 Windows 虛擬機器 

- 從命令列[手動安裝](agent-windows.md)代理程式。
- 使用 [AZURE 自動化 DSC](agent-windows.md#install-agent-using-dsc-in-azure-automation)自動化安裝。
- 使用 [Resource Manager 範本搭配 Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>內部部署或其他雲端中的 Linux 虛擬機器

- [手動安裝](../learn/quick-collect-linux-computer.md) 代理程式，呼叫裝載在 GitHub 上的包裝函式腳本。
- System Center Operations Manager |將[Operations Manager 與 Azure 監視器整合](./om-agents.md) ，將收集到的資料從回報的 Windows 電腦轉送到管理群組。

## <a name="workspace-id-and-key"></a>工作區識別碼和金鑰
無論使用的安裝方法為何，您都需要代理程式將連接之 Log Analytics 工作區的工作區識別碼和金鑰。 從 Azure 入口網站的 **Log Analytics 工作區** 功能表中選取工作區。 然後，在 [**設定**] 區段中選取 [**代理程式管理**]。 

[![工作區詳細資料](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1.2 通訊協定

為了確保資料傳送至 Azure 監視器記錄時的安全性，我們強烈建議您將代理程式設定為至少使用傳輸層安全性 (TLS) 1.2。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。  如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](data-security.md#sending-data-securely-using-tls-12)。 

## <a name="network-requirements"></a>網路需求
Linux 和 Windows 的代理程式會透過 TCP 通訊埠443，透過 TCP 埠向 Azure 監視器服務進行輸出通訊。 如果電腦透過防火牆或 proxy 伺服器連線到網際網路進行通訊，請參閱下方的需求，以瞭解所需的網路設定。 如果您的 IT 安全性原則不允許網路上的電腦連線到網際網路，您可以設定 [Log Analytics 閘道](gateway.md) ，然後將代理程式設定為透過閘道連接到 Azure 監視器。 然後，代理程式就可以接收設定資訊，並傳送收集到的資料。

![Log Analytics 代理程式通訊圖表](./media/log-analytics-agent/log-analytics-agent-01.png)

下表列出 Linux 和 Windows 代理程式用來與 Azure 監視器記錄進行通訊所需的 proxy 和防火牆設定資訊。

### <a name="firewall-requirements"></a>防火牆需求

|代理程式資源|連接埠 |方向 |略過 HTTPS 檢查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.oms.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.blob.core.windows.net |連接埠 443 |輸出|是 |
|*.azure-automation.net |連接埠 443 |輸出|是 |

如需 Azure Government 所需的防火牆資訊，請參閱 [Azure Government 管理](../../azure-government/compare-azure-government-global-azure.md#azure-monitor)。 

如果您打算使用 Azure 自動化混合式 Runbook 背景工作角色連線到自動化服務並向其註冊，以便在您的環境中使用 Runbook 或管理解決方案，其必須具有[設定適用於混合式 Runbook 背景工作角色的網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)中所述的連接埠號碼和 URL 存取權。 

### <a name="proxy-configuration"></a>Proxy 組態

Windows 和 Linux 代理程式支援使用 HTTPS 通訊協定，透過 Proxy 伺服器或 Log Analytics 閘道來與 Azure 監視器通訊。  不支援匿名和基本驗證 (使用者名稱/密碼)。  若是直接連線到服務的 Windows 代理程式，請在安裝期間或[部署後](agent-manage.md#update-proxy-settings)從控制台或使用 PowerShell 來指定 Proxy 設定。  

若是 Linux 代理程式，請在安裝期間或[安裝後](agent-manage.md#update-proxy-settings)透過修改 proxy.conf 設定檔來指定 Proxy 伺服器。  Linux 代理程式 Proxy 設定值的語法如下：

`[protocol://][user:password@]proxyhost[:port]`

|屬性| 描述 |
|--------|-------------|
|通訊協定 | https |
|user | 用於驗證 Proxy 的選擇性使用者名稱 |
|密碼 | 用於驗證 Proxy 的選擇性密碼 |
|proxyhost | Proxy 伺服器/Log Analytics 閘道的位址或 FQDN |
|連接埠 | Proxy 伺服器/Log Analytics 閘道的選擇性連接埠號碼 |

例如： `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 若您在您的密碼中使用特殊字元 (例如 "\@")，您會收到 Proxy 連線錯誤訊息，因為系統無法正確剖析該值。  若要暫時解決此問題，請使用 [URLDecode](https://www.urldecoder.org/) 之類的工具將 URL 中的密碼編碼。  



## <a name="next-steps"></a>後續步驟

* 檢閱[資料來源](agent-data-sources.md)以了解可用於從您的 Windows 或 Linux 系統收集資料的資料來源。 
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。 
* 了解可將功能新增至 Azure 監視器，並會將資料收集到 Log Analytics 工作區的[監視解決方案](../insights/solutions.md)。

