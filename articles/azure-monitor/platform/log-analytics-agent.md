---
title: Log Analytics 代理程式概觀
description: 此主題可協助您了解如何使用 Log Analytics 來收集資料，並監視 Azure、內部部署或其他雲端環境中裝載的電腦。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: cbefe2e2b25db7ce16a7a1bde423f60fda412590
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773350"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics 代理程式概觀
開發 Azure Log Analytics 代理程式是為了能夠全面管理任何雲端中的虛擬機器、內部部署機器，以及 [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) 所監視的機器。 Windows 和 Linux 代理程式會將從不同來源收集而來的資料傳送至 Azure 監視器中的 Log Analytics 工作區，以及監視解決方案中所定義的任何唯一記錄或計量。 Log Analytics 代理程式也支援 Azure 監視器中的深入解析和其他服務，例如[適用於 VM 的 Azure 監視器](../insights/vminsights-enable-overview.md)、[Azure 資訊安全中心](/azure/security-center/)和 [Azure 自動化](../../automation/automation-intro.md)。

本文將詳細說明代理程式的概觀、系統和網路需求以及不同的部署方法。

> [!NOTE]
> 您也可能會看到稱為 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理程式的 Log Analytics 代理程式。

> [!NOTE]
> Azure 診斷延伸模組是可從計算資源的客體作業系統收集監視資料的其中一個代理程式。 如需不同代理程式的描述，以及針對需求選取適當代理程式的相關指引，請參閱 [Azure 監視器代理程式的概觀](agents-overview.md)。

## <a name="comparison-to-azure-diagnostics-extension"></a>與 Azure 診斷擴充功能的比較
Azure 監視器中的 [Azure 診斷擴充功能](diagnostics-extension-overview.md)也可用來從 Azure 虛擬機器的客體作業系統收集監視資料。 視需求而定，可選擇使用其中一或兩種方式。 如需 Azure 監視器代理程式的詳細比較，請參閱 [Azure 監視器代理程式的概觀](agents-overview.md)。 

需要考慮的主要差異如下：

- Azure 診斷延伸模組只能與 Azure 虛擬機器搭配使用。 Log Analytics 代理程式可與 Azure、其他雲端和內部部署中的虛擬機器搭配使用。
- Azure 診斷延伸模組會將資料傳送至 Azure 儲存體、[Azure 監視器計量](data-platform-metrics.md) (僅限 Windows) 和事件中樞。 Log Analytics 代理程式則會將資料收集到 [Azure 監視器記錄](data-platform-logs.md)。
- [解決方案](../monitor-reference.md#insights-and-core-solutions)、[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)以及其他服務 (例如 [Azure 資訊安全中心](/azure/security-center/)) 都需要 Log Analytics 代理程式。

## <a name="costs"></a>費用
Log Analytics 代理程式沒有任何成本，但您可能會因為所擷取的資料而產生費用。 如需 Log Analytics 工作區中所收集資料的定價詳細資訊，請參閱[使用 Azure 監視器記錄來管理使用量和成本](manage-cost-storage.md)。

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
Log Analytics 代理程式會將資料傳送至 Azure 監視器中的 Log Analytics 工作區。 Windows 代理程式可以有多重主目錄，以將資料傳送至多個工作區和 System Center Operations Manager 管理群組。 Linux 代理程式則只能傳送至單一目的地。

## <a name="other-services"></a>其他服務
適用於 Linux 與 Windows 的代理程式不僅可用於連線到 Azure 監視器，也支援「Azure 自動化」以裝載「混合式 Runbook」背景工作角色與其他服務，例如[變更追蹤](../../automation/change-tracking.md)、[更新管理](../../automation/automation-update-management.md)與 [Azure 資訊安全中心](../../security-center/security-center-intro.md)。 如需有關「混合式 Runbook」背景工作角色的詳細資訊，請參閱 [Azure 自動化混合式 Runbook 背景工作](../../automation/automation-hybrid-runbook-worker.md)。  

## <a name="installation-and-configuration"></a>安裝和組態

使用 Log Analytics 代理程式來收集資料時，您必須了解下列事項才能規劃代理程式的部署：

* 若要從 Windows 代理程式收集資料，您可以[將每個代理程式設定為向一或多個工作區報告](agent-windows.md)，即使其同時會向 System Center Operations Manager 管理群組報告也沒關係。 Windows 代理程式最多可以向四個工作區報告。
* Linux 代理程式不支援多重主目錄，只能向單一工作區報告。
* Windows 代理程式支援 [FIPS 140 標準](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)，Linux 代理程式則不支援此標準。  

如果您使用 System Center Operations Manager 2012 R2 或更新版本：

* 每個 Operations Manager 管理群組[只能連線到一個工作區](om-agents.md)。
* 向管理群組報告的 Linux 電腦必須設定為直接向 Log Analytics 工作區報告。 如果您的 Linux 電腦已直接向工作區報告，而您想要使用 Operations Manager 來監視這些電腦，請遵循這些步驟來[向 Operations Manager 管理群組報告](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)。
* 您可以在 Windows 電腦上安裝 Log Analytics Windows 代理程式，並讓其同時向已與工作區整合的 Operations Manager 以及不同工作區報告。


根據需求而定，有多種方法可以安裝 Log Analytics 代理程式並將您的機器連線至 Azure 監視器。 下表說明每個方法，您可以判斷哪個方法最適合您的組織。

|來源 | 方法 | 描述|
|-------|-------------|-------------|
|Azure VM| [從 Azure 入口網站手動執行](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | 指定要從 Log Analytics 工作區部署的 VM。 |
| | 適用於 [Windows](../../virtual-machines/extensions/oms-windows.md) 或 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 Log Analytics VM 擴充功能，使用 Azure CLI 或 Azure Resource Manager 範本 | 擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並且在現有的 Azure 監視器工作區中註冊這些機器。 |
| | [適用於 VM 的 Azure 監視器](../insights/vminsights-enable-overview.md) | 當您為適用於 VM 的 Azure 監視器啟用監視時，其會安裝 Log Analytics 擴充功能和代理程式。 |
| | [Azure 資訊安全中心的自動佈建](../../security-center/security-center-enable-data-collection.md) | Azure 資訊安全中心可以將 Log Analytics 代理程式佈建在所有受支援的 Azure VM 上，以及任何新建立的 Azure VM 上 (如果您為其啟用監視安全性弱點和威脅的功能)。 若已啟用，則會佈建任何未安裝代理程式的全新或現有 VM。 |
| 混合式 Windows 電腦| [手動安裝](agent-windows.md) | 從命令列安裝 Microsoft Monitoring Agent。 |
| | [Azure 自動化 DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | 使用 Azure 自動化 DSC 來自動安裝。 |
| | [Resource Manager 範本搭配 Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | 使用 Azure Resource Manager 範本 (如果您已在資料中心內部署 Microsoft Azure Stack)。| 
| 混合式 Linux 電腦| [手動安裝](../../azure-monitor/learn/quick-collect-linux-computer.md)|安裝適用於 Linux 的代理程式需要 GitHub 上裝載的包裝函式指令碼。 | 
| System Center Operations Manager|[將 Operations Manager 與 Log Analytics 整合](../../azure-monitor/platform/om-agents.md) | 設定 Operations Manager 與 Azure 監視器記錄之間的整合，以將從 Windows 電腦報告收集而來的資料轉送至管理群組。|  


## <a name="supported-windows-operating-systems"></a>支援的 Windows 作業系統

Windows 代理程式正式支援下列 Windows 作業系統版本：

* Windows Server 2019
* Windows Server 2016 1709 版和 1803 版
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 企業版 (包括多工作階段) 和專業版
* Windows 8 企業版和專業版 
* Windows 7 SP1

>[!NOTE]
>雖然適用於 Windows 的 Log Analytics 代理程式的設計目的是為了支援伺服器監視案例，但我們了解您可能會執行 Windows 用戶端來支援針對伺服器作業系統所設定並最佳化的工作負載。 此代理程式確實支援 Windows 用戶端，但除非有明確指出，否則我們的監視解決方案不會聚焦在用戶端監視案例。

## <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統

本節提供有關所支援 Linux 散發套件的詳細資料。

從 2018 年 8 月之後所發行的版本開始，我們會對支援模型進行下列變更：  

* 只支援伺服器版本，不支援用戶端版本。  
* 請將焦點放在任何 [Azure Linux 背書散發版本](../../virtual-machines/linux/endorsed-distros.md)的支援上。 請注意，從新的散發版本/版本受到 Azure Linux 背書到開始支援 Log Analytics Linux 代理程式，時間上可能會有一些延遲。
* 列出的每個主要版本都支援所有次要版本。
* 不支援製造商結束支援日期已過的版本。  
* 不支援新版 AMI。  
* 只支援預設執行 SSL 1.x 的版本。

>[!NOTE]
>如果您使用的散發版本或版本目前不受支援，且不符合支援模型，建議您為此存放庫建立分支，以認可 Microsoft 支援服務將不會對分支代理程式版本提供協助。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x64) 和 7 (x64)  
* Oracle Linux 6 和 7 (x64) 
* Red Hat Enterprise Linux Server 6 (x64)、7 (x64) 和 8 (x64)
* Debian GNU/Linux 8 和 9 (x64)
* Ubuntu 14.04 LTS (x86/x64)、16.04 LTS (x64) 和 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) 和 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 只支援用於 x86_x64 平台 (64 位元)，1.x 之前的 OpenSSL 則不支援用於任何平台。
>

### <a name="agent-prerequisites"></a>代理程式必要條件

下表點出將會安裝代理程式的受支援 Linux 散發版本所需的套件。

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

為了確保資料傳送至 Azure 監視器記錄時的安全性，我們強烈建議您將代理程式設定為至少使用傳輸層安全性 (TLS) 1.2。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。  如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](data-security.md#sending-data-securely-using-tls-12)。 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Windows 的 SHA-2 程式碼簽署支援需求
Windows 代理程式將在 2020 年 8 月 17 日開始獨佔使用 SHA-2 簽署。 這項變更會影響在舊版 OS 上以任何 Azure 服務 (Azure 監視器、Azure 自動化、Azure 更新管理、Azure 變更追蹤、Azure 資訊安全中心、Azure Sentinel、Windows Defender ATP) 的形式來使用 Log Analytics 代理程式的客戶。 除非客戶在舊版 OS (Windows 7、Windows Server 2008 R2 和 Windows Server 2008) 上執行代理程式，否則此變更不需要其採取任何動作。 在舊版 OS 上執行的客戶必須於 2020 年 8 月 17 日之前在其電腦上採取下列動作，否則其代理程式將會停止將資料傳送到其 Log Analytics 工作區：

1. 安裝適用於 OS 的最新 Service Pack。 所需的 Service Pack 版本如下：
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. 安裝適用於 OS 的 SHA-2 簽署 Windows 更新，如 [Windows 和 WSUS 的 2019 SHA-2 程式碼簽署支援需求](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)所述
3. 更新至最新版的 Windows 代理程式 (10.20.18029 版)。
4. 建議將代理程式設定為[使用 TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12)。 


## <a name="network-requirements"></a>網路需求
適用於 Linux 與 Windows 的代理程式會透過 TCP 連接埠 443 對外與 Azure 監視器服務通訊，且如果機器連線至防火牆或 Proxy 伺服器以透過網際網路通訊，請檢閱下面的需求，以了解所需的網路設定。 如果您的 IT 安全性原則不允許網路上的電腦連線到網際網路，您可以設定 [Log Analytics 閘道](gateway.md)，然後將代理程式設定為透過閘道連線至 Azure 監視器記錄。 接著，代理程式就可以接收設定資訊，並傳送根據您在工作區中啟用的資料收集規則和監視解決方案而收集的資料。

![Log Analytics 代理程式通訊圖表](./media/log-analytics-agent/log-analytics-agent-01.png)

下表列出 Linux 和 Windows 代理程式要與 Azure 監視器記錄通訊所需的 Proxy 和防火牆設定資訊。

### <a name="firewall-requirements"></a>防火牆需求

|代理程式資源|連接埠 |方向 |略過 HTTPS 檢查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.oms.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.blob.core.windows.net |連接埠 443 |輸出|是 |
|*.azure-automation.net |連接埠 443 |輸出|是 |

如需 Azure Government 所需的防火牆資訊，請參閱 [Azure Government 管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

如果您打算使用 Azure 自動化混合式 Runbook 背景工作角色連線到自動化服務並向其註冊，以便在您的環境中使用 Runbook 或管理解決方案，其必須具有[設定適用於混合式 Runbook 背景工作角色的網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)中所述的連接埠號碼和 URL 存取權。 

### <a name="proxy-configuration"></a>Proxy 組態

Windows 和 Linux 代理程式支援使用 HTTPS 通訊協定，透過 Proxy 伺服器或 Log Analytics 閘道來與 Azure 監視器通訊。  不支援匿名和基本驗證 (使用者名稱/密碼)。  若是直接連線到服務的 Windows 代理程式，請在安裝期間或[部署後](agent-manage.md#update-proxy-settings)從控制台或使用 PowerShell 來指定 Proxy 設定。  

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
> 若您在您的密碼中使用特殊字元 (例如 "\@")，您會收到 Proxy 連線錯誤訊息，因為系統無法正確剖析該值。  若要暫時解決此問題，請使用 [URLDecode](https://www.urldecoder.org/) 之類的工具將 URL 中的密碼編碼。  



## <a name="next-steps"></a>後續步驟

* 檢閱[資料來源](agent-data-sources.md)以了解可用於從您的 Windows 或 Linux 系統收集資料的資料來源。 
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。 
* 了解可將功能新增至 Azure 監視器，並會將資料收集到 Log Analytics 工作區的[監視解決方案](../insights/solutions.md)。

