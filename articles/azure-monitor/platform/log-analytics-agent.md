---
title: 日誌分析代理概述
description: 此主題可協助您了解如何使用 Log Analytics 來收集資料，並監視 Azure、內部部署或其他雲端環境中裝載的電腦。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 1ca03cde57a9496054d0860fbb70bd286caabe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533244"
---
# <a name="log-analytics-agent-overview"></a>日誌分析代理概述
Azure 日誌分析代理是為在任何雲、本地電腦以及[系統中心操作管理器](https://docs.microsoft.com/system-center/scom/)監視的虛擬機器中進行全面管理而開發的。 Windows 和 Linux 代理將收集的資料從不同來源發送到 Azure 監視器中的日誌分析工作區，以及監視解決方案中定義的任何唯一日誌或指標。 日誌分析代理還支援 Azure 監視器中的見解和其他服務，如[VM 的 Azure 監視器](../insights/vminsights-enable-overview.md)[、Azure 安全中心和](/azure/security-center/)Azure[自動化](../../automation/automation-intro.md)。

本文將詳細說明代理程式的概觀、系統和網路需求以及不同的部署方法。

> [!NOTE]
> 您可能還會看到日誌分析代理稱為 Microsoft 監視代理 （MMA） 或 OMS Linux 代理。

> [!NOTE]
> Azure 診斷擴展是可用於從計算資源的客體作業系統收集監視資料的代理之一。 有關不同代理的說明以及選擇適合需求的代理的指導，請參閱[Azure 監視器代理概述](agents-overview.md)。

## <a name="comparison-to-azure-diagnostics-extension"></a>與 Azure 診斷擴展的比較
Azure 監視器中的[Azure 診斷擴展](diagnostics-extension-overview.md)還可用於從 Azure 虛擬機器的客體作業系統收集監視資料。 根據您的要求，您可以選擇使用任一或兩者。 有關 Azure 監視器代理的詳細比較，請參閱[Azure 監視器代理的概述](agents-overview.md)。 

需要考慮的主要區別是：

- Azure 診斷擴展只能與 Azure 虛擬機器一起使用。 日誌分析代理可用於 Azure 中的虛擬機器、其他雲和本地。
- Azure 診斷擴展將資料發送到 Azure 存儲[、Azure 監視器指標](data-platform-metrics.md)（僅限 Windows）和事件中心。 日誌分析代理將資料收集到 Azure[監視器日誌](data-platform-logs.md)。
- [解決方案](../monitor-reference.md#insights-and-core-solutions)[、VM 的 Azure 監視器](../insights/vminsights-overview.md)和其他服務（如[Azure 安全中心](/azure/security-center/)）需要日誌分析代理。

## <a name="costs"></a>費用
日誌分析代理不收取任何費用，但可能會對引入的資料產生費用。 [使用 Azure 監視器日誌檢查管理使用方式和成本](manage-cost-storage.md)，瞭解日誌分析工作區中收集的資料定價的詳細資訊。

## <a name="data-collected"></a>收集的資料
下表列出了可以配置日誌分析工作區以便從所有連接的代理收集的資料類型。 有關使用日誌分析代理收集其他類型的資料的見解、解決方案和其他解決方案的清單，請參閱[Azure 監視器監視的內容](../monitor-reference.md)。

| 資料來源 | 描述 |
| --- | --- |
| [視窗事件日誌](data-sources-windows-events.md) | 傳送至 Windows 事件記錄系統的資訊。 |
| [Syslog](data-sources-syslog.md)                     | 發送到 Linux 事件日誌記錄系統的資訊。 |
| [效能](data-sources-performance-counters.md)  | 測量作業系統和工作負載不同方面性能的數值。 |
| [IIS 日誌](data-sources-iis-logs.md)                 | 在客體作業系統上運行的 IIS 網站的使用方式資訊。 |
| [自訂的記錄](data-sources-custom-logs.md)           | 來自 Windows 和 Linux 電腦上的文字檔的事件。 |

## <a name="data-destinations"></a>資料目的地
日誌分析代理將資料發送到 Azure 監視器中的日誌分析工作區。 Windows 代理可以多居化，將資料發送到多個工作區和系統中心操作管理器管理組。 Linux 代理只能發送到單個目標。

## <a name="other-services"></a>其他服務
Linux 和 Windows 的代理不僅用於連接到 Azure 監視器，它還支援 Azure 自動化以承載混合 Runbook 輔助角色和其他服務，如[更改跟蹤](../../automation/change-tracking.md)、[更新管理和](../../automation/automation-update-management.md) [Azure 安全中心](../../security-center/security-center-intro.md)。 如需有關「混合式 Runbook」背景工作角色的詳細資訊，請參閱 [Azure 自動化混合式 Runbook 背景工作](../../automation/automation-hybrid-runbook-worker.md)。  

## <a name="installation-and-configuration"></a>安裝和組態

使用日誌分析代理收集資料時，您需要瞭解以下內容，以便規劃代理部署：

* 要從 Windows 代理收集資料，可以將[每個代理配置為向一個或多個工作區報告](agent-windows.md)，即使它向系統中心操作管理器管理組報告也是如此。 Windows 代理最多可以報告四個工作區。
* Linux 代理不支援多宿主，只能向單個工作區報告。
* Windows 代理支援[FIPS 140 標準](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)，而 Linux 代理不支援它。  

如果您使用的是系統中心操作管理器 2012 R2 或更高版本：

* 每個操作管理器管理組只能[連接到一個工作區](om-agents.md)。
* 向管理組報告 Linux 電腦必須配置為直接報告到日誌分析工作區。 如果您的 Linux 電腦已直接報告到工作區，並且您希望使用操作管理器監視它們，請按照以下步驟[向操作管理器管理組報告](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)。
* 您可以在 Windows 電腦上安裝日誌分析 Windows 代理，並使其向操作管理器報告與工作區和不同的工作區集成。


有多種方法可以安裝日誌分析代理，並根據要求將電腦連接到 Azure 監視器。 下表說明每個方法，您可以判斷哪個方法最適合您的組織。

|來源 | 方法 | 描述|
|-------|-------------|-------------|
|Azure VM| [手動從 Azure 門戶](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | 指定要從日誌分析工作區部署的 VM。 |
| | 使用 Azure CLI 或使用 Azure 資源管理器範本的[Windows](../../virtual-machines/extensions/oms-windows.md)或[Linux](../../virtual-machines/extensions/oms-linux.md)日誌分析 VM 擴展 | 擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並且在現有的 Azure 監視器工作區中註冊這些機器。 |
| | [虛擬機器的 Azure 監視器](../insights/vminsights-enable-overview.md) | 使用 Azure 監視器為 VM 啟用監視時，它將安裝日誌分析擴展和代理。 |
| | [Azure 安全中心自動預配](../../security-center/security-center-enable-data-collection.md) | Azure 安全中心可以在所有受支援的 Azure VM 上預配日誌分析代理，以及啟用它監視安全性漏洞和威脅時創建的任何新代理。 如果啟用，將預配任何沒有已安裝代理的新或現有 VM。 |
| 混合式 Windows 電腦| [手動安裝](agent-windows.md) | 從命令列安裝 Microsoft 監視代理。 |
| | [Azure 自動化 DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | 使用 Azure 自動化 DSC 自動安裝。 |
| | [搭配 Azure Stack 的資源管理員範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | 如果在資料中心中部署了 Microsoft Azure 堆疊，請使用 Azure 資源管理器範本。| 
| 混合式 Linux 電腦| [手動安裝](../../azure-monitor/learn/quick-collect-linux-computer.md)|安裝適用於 Linux 的代理程式需要 GitHub 上裝載的包裝函式指令碼。 | 
| System Center Operations Manager|[將 Operations Manager 與 Log Analytics 整合](../../azure-monitor/platform/om-agents.md) | 配置操作管理器和 Azure 監視器日誌之間的集成，以便將從報告到管理組的 Windows 電腦收集的資料轉發。|  


## <a name="supported-windows-operating-systems"></a>支援的 Windows 作業系統

Windows 代理程式正式支援下列 Windows 作業系統版本：

* Windows Server 2019
* Windows 伺服器 2008 SP2 （x64）， 2008 R2， 2012， 2012 R2， 2016， 版本 1709 和 1803
* Windows 7 SP1、Windows 8 企業版和 Pro 版以及 Windows 10 企業版和 Pro 版

>[!NOTE]
>雖然 Windows 的日誌分析代理旨在支援伺服器監視方案，但我們意識到您可以運行 Windows 用戶端以支援為伺服器作業系統配置和優化的工作負載。 代理確實支援 Windows 用戶端，但我們的監視解決方案不會專注于用戶端監視方案，除非明確說明。

## <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統

本節提供有關所支援 Linux 散發套件的詳細資料。

從 2018 年 8 月之後所發行的版本開始，我們會對支援模型進行下列變更：  

* 只支援伺服器版本，不支援用戶端版本。  
* 對任何[Azure Linux 認可的發行版本](../../virtual-machines/linux/endorsed-distros.md)進行重點支援。 請注意，在新的發行版本/版本是 Azure Linux 支援版本和日誌分析 Linux 代理支援它之間可能存在一些延遲。
* 列出的每個主要版本都支援所有次要版本。
* 不支援製造商結束支援日期已過的版本。  
* 不支援新版 AMI。  
* 只支援預設執行 SSL 1.x 的版本。

>[!NOTE]
>如果您使用的散發版本或版本目前不受支援，且不符合支援模型，建議您為此存放庫建立分支，以認可 Microsoft 支援服務將不會對分支代理程式版本提供協助。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) 和 7 (x64)  
* Oracle Linux 6 和 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) 和 7 (x64)
* Debian GNU/Linux 8 和 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64)、16.04 LTS (x86/x64) 和 18.04 LTS (x64)
* SUSE Linux 企業伺服器 12 （x64） 和 15 （x64）

>[!NOTE]
>OpenSSL 1.1.0 只支援用於 x86_x64 平台 (64 位元)，1.x 之前的 OpenSSL 則不支援用於任何平台。
>

### <a name="agent-prerequisites"></a>代理程式先決條件

下表突出顯示了將安裝代理支援的 Linux 發行版本所需的套裝軟體。

|必要的套件 |描述 |最小版本 |
|-----------------|------------|----------------|
|Glibc |    GNU C 程式庫 | 2.5-12 
|Openssl    | OpenSSL 程式庫 | 1.0.x 或 1.1.x |
|Curl | cURL Web 用戶端 | 7.15.5 |
|Python-ctypes | | 
|PAM | 插入式驗證模組 | | 

>[!NOTE]
>需要有 rsyslog 或 syslog-ng，才能收集 syslog 訊息。 Red Hat Enterprise Linux 第 5 版、CentOS 和 Oracle Linux 版本 (sysklog) 不支援預設 syslog 精靈，進行 syslog 事件收集。 若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應該安裝和設定為取代 sysklog。

## <a name="tls-12-protocol"></a>TLS 1.2 通訊協定

為了確保傳輸到 Azure 監視器日誌的資料的安全性，我們強烈建議您配置代理至少使用傳輸層安全 （TLS） 1.2。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。  如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](data-security.md#sending-data-securely-using-tls-12)。 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>SHA-2 Windows 的代碼簽名支援要求
Windows 代理將于 2020 年 5 月 18 日開始專門使用 SHA-2 簽名。 此更改將影響客戶使用舊版作業系統上的日誌分析代理作為任何 Azure 服務的一部分（Azure 監視器、Azure 自動化、Azure 更新管理、Azure 更改跟蹤、Azure 安全中心、Azure 哨兵、Windows Defender ATP）。 除非您在舊版作業系統版本（Windows 7、Windows Server 2008 R2 和 Windows Server 2008）上運行代理，否則更改不需要任何客戶操作。 在 2020 年 5 月 18 日之前，運行在舊版作業系統版本的客戶必須在其電腦上執行以下操作，否則其代理將停止將資料發送到其日誌分析工作區：

1. 為您的作業系統安裝最新的服務包。 所需的服務包版本包括：
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. 安裝 SHA-2 簽名 Windows 更新，如[2019 年 SHA-2 代碼簽名支援 Windows 和 WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)中所述
3. 更新到最新版本的 Windows 代理（版本 10.20.18029）。
4. 建議將代理配置為[使用 TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12)。 


## <a name="network-requirements"></a>網路需求
Linux 和 Windows 的代理通過 TCP 埠 443 將出站與 Azure 監視器服務通信，如果電腦通過防火牆或代理伺服器連接以通過 Internet 進行通信，請查看以下要求以瞭解網路設定必填。 如果您的 IT 安全性原則不允許網路上的電腦連接到 Internet，則可以設置[日誌分析閘道](gateway.md)，然後將代理配置為通過閘道連接到 Azure 監視器日誌。 然後，代理可以接收配置資訊併發送收集的資料，具體取決於您在工作區中啟用的資料收集規則和監視解決方案。

![Log Analytics 代理程式通訊圖表](./media/log-analytics-agent/log-analytics-agent-01.png)


## <a name="network-firewall-requirements"></a>網路防火牆需求
以下資訊列出了 Linux 和 Windows 代理與 Azure 監視器日誌通信所需的代理和防火牆配置資訊。  

|代理程式資源|連接埠 |方向 |略過 HTTPS 檢查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.oms.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.blob.core.windows.net |連接埠 443 |輸出|是 |  

有關 Azure 政府所需的防火牆資訊，請參閱[Azure 政府管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

如果計畫使用 Azure 自動化混合 Runbook 輔助角色連接到自動化服務並在自動化服務中註冊以在環境中使用 Runbook 或管理解決方案，則必須有權訪問埠號和在[為混合 Runbook 工作執行緒配置網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)中描述的 URL。 

Windows 和 Linux 代理支援使用 HTTPS 協定通過代理伺服器或日誌分析閘道與 Azure 監視器通信。  不支援匿名和基本驗證 (使用者名稱/密碼)。  若是直接連線到服務的 Windows 代理程式，請在安裝期間或[部署後](agent-manage.md#update-proxy-settings)從控制台或使用 PowerShell 來指定 Proxy 設定。  

若是 Linux 代理程式，請在安裝期間或[安裝後](agent-manage.md#update-proxy-settings)透過修改 proxy.conf 設定檔來指定 Proxy 伺服器。  Linux 代理程式 Proxy 設定值的語法如下：

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 若您的 Proxy 伺服器不要求您進行驗證，Linux 代理程式仍會要求提供虛擬使用者/密碼。 這可以是任何使用者名稱或密碼。

|屬性| 描述 |
|--------|-------------|
|通訊協定 | https |
|user | 用於驗證 Proxy 的選擇性使用者名稱 |
|密碼 | 用於驗證 Proxy 的選擇性密碼 |
|proxyhost | Proxy 伺服器/Log Analytics 閘道的位址或 FQDN |
|連接埠 | Proxy 伺服器/Log Analytics 閘道的選擇性連接埠號碼 |

例如： `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 如果在密碼中使用特殊字元（如""），\@則會收到代理連接錯誤，因為值解析不正確。  若要暫時解決此問題，請使用 [URLDecode](https://www.urldecoder.org/) 之類的工具將 URL 中的密碼編碼。  



## <a name="next-steps"></a>後續步驟

* 檢閱[資料來源](agent-data-sources.md)以了解可用於從您的 Windows 或 Linux 系統收集資料的資料來源。 
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。 
* 了解可將功能新增至 Azure 監視器，並會將資料收集到 Log Analytics 工作區的[監視解決方案](../insights/solutions.md)。

