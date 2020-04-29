---
title: 將資料來源連線至 Azure Sentinel | Microsoft Docs
description: 了解如何將資料來源連線至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: e59359d791729d2136003596d80dd1f31a9a2aac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025147"
---
# <a name="connect-data-sources"></a>連線資料來源

若要將 Azure Sentinel 上架，必須先連線到資料來源。 Azure Sentinel 隨附多種適用於 Microsoft 解決方案的連接器，現成可用且提供即時整合，包括 Microsoft 威脅防護解決方案與 Microsoft 365 來源，例如 Office 365、Azure AD、Azure ATP 和 Microsoft Cloud App Security 等等。 此外，還有適用於非 Microsoft 解決方案的內建連接器，用於連線至更廣泛的安全性生態系統。 您也可以使用常見事件格式 (Syslog 或 REST API) 來連線資料來源與 Azure Sentinel。  

1. 在功能表上，選取 [資料連接器]  。 此頁面可讓您查看 Azure Sentinel 提供的連接器完整清單及其狀態。 選取您想要連線的連接器，然後選取 [開啟連接器頁面]  。 

   ![資料收集器](./media/collect-data/collect-data-page.png)

1. 在特定連接器頁面上，確定您已符合所有必要條件，並遵循指示將資料連線到 Azure Sentinel。 可能需要一些時間，記錄才能開始與 Azure Sentinel 同步處理。 連線之後，您會在 [接收的資料]  圖表中看見資料摘要，以及資料類型的連線狀態。

   ![連接收集器](./media/collect-data/opened-connector-page.png)
  
1. 按一下 [後續步驟]  索引標籤，以取得 Azure Sentinel 針對特定資料類型提供的立即可用內容清單。

   ![資料收集器](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>資料連線方法

Azure Sentinel 支援下列資料連線方法：

- **服務對服務整合**：<br> 某些服務會以原生方式連線 (例如 AWS 和 Microsoft 服務)，這些服務會運用 Azure 基礎進行立即整合，只需按幾下就可以與下列解決方案連線：
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - [Azure 活動](connect-azure-activity.md)
    - [Azure AD 稽核記錄和登入](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure 進階威脅防護](connect-azure-atp.md)
    - [Azure 資訊保護](connect-azure-information-protection.md)
    - [Azure 資訊安全中心](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [網域名稱伺服器](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Microsoft Web 應用程式防火牆](connect-microsoft-waf.md)
    - [Windows 防火牆](connect-windows-firewall.md)
    - [Windows 安全性事件](connect-windows-security-events.md)

- **透過 API 連線的外部解決方案**：某些資料來源會使用自身提供的 API 進行連線。 一般而言，大部分的安全性技術都會提供一組 API，藉此來擷取事件記錄。API 可連線到 Azure Sentinel 並收集特定資料類型，然後將其傳送至 Azure Log Analytics。 透過 API 連線的設備包括：
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **透過代理程式連線的外部解決方案**：透過代理程式，Azure Sentinel 可以連線到所有其他使用 Syslog 通訊協定執行即時記錄串流的資料來源。 <br>大部分設備會使用 Syslog 通訊協定來傳送事件訊息，包含記錄本身和記錄的相關資料。 記錄的格式各不相同，但大部分的設備都會針對記錄資料支援以常見事件格式 (CEF) 為基礎的格式設定。 <br>Azure Sentinel 代理程式以 Log Analytics 代理程式作為基礎，可將 CEF 格式的記錄轉換成 Log Analytics 可擷取的格式。 根據不同設備類型，代理程式可能會直接安裝在設備上，或安裝在專用的 Linux 伺服器上。 適用於 Linux 的代理程式會透過 UDP 從 Syslog 精靈接收事件，但如果預期 Linux 機器會收集大量的 Syslog 事件，則會透過 TCP 將事件從 Syslog 精靈傳送到代理程式，然後從該處傳送至 Log Analytics。
    - 防火牆、Proxy 和端點：
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Forcepoint 產品](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [其他 CEF 設備](connect-common-event-format.md)
        - [其他 Syslog 設備](connect-syslog.md)
        - [趨勢科技 Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - DLP 解決方案
    - [威脅情報提供者](connect-threat-intelligence.md)
    - [DNS 機器](connect-dns.md) - 直接在 DNS 機器上安裝代理程式
    - Linux 伺服器
    - 其他雲端
    
## <a name="agent-connection-options"></a>代理程式連接選項<a name="agent-options"></a>

若要將外部設備連接至 Azure Sentinel，代理程式必須部署在專用的機器上 (VM 或內部部署裝置)，才能支援設備與 Azure Sentinel 之間的通訊。 您可以透過自動或手動來部署代理程式。 只有當您的專用機器是在 Azure 中建立的新 VM 時，才能使用自動部署。 


![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，您可以透過手動方式在現有的 Azure VM、在另一個雲端中的 VM 或在內部部署機器上部署代理程式。

![內部部署的 CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>使用 Azure Sentinel 連線選項對應資料類型


| **Data type** | **如何連線** | **資料連接器？** | **註解** |
|------|---------|-------------|------|
| AWSCloudTrail | [連線 AWS](connect-aws.md) | V | |
| AzureActivity | [連線 Azure 活動](connect-azure-activity.md)和[活動記錄概觀](../azure-monitor/platform/platform-logs-overview.md)| V | |
| AuditLogs | [Connect Azure AD](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Connect Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Azure 診斷](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Azure 資訊保護報告](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[連線 Azure 資訊保護](connect-azure-information-protection.md)  | V | 除了資料類型之外，這通常會使用 **InformationProtectionEvents**函式。 如需詳細資訊，請參閱[如何修改報告及建立自訂查詢](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [流量分析結構描述](../network-watcher/traffic-analytics.md) [流量分析](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [連線 CEF](connect-common-event-format.md)  | V | |
| OfficeActivity | [連線 Office 365](connect-office-365.md) | V | |
| SecurityEvents | [連線 Windows 安全性事件](connect-windows-security-events.md)  | V | 若為不安全的通訊協定活頁簿，請參閱[不安全的通訊協定活頁簿設定](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| syslog | [連線 Syslog](connect-syslog.md) | V | |
| Microsoft Web 應用程式防火牆 (WAF) - (AzureDiagnostics) |[連線 Microsoft Web 應用程式防火牆](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [連線 Symantec](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [連線威脅情報](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure 監視服務對應](../azure-monitor/insights/service-map.md)<br>[Azure 監視器 VM 見解上線](../azure-monitor/insights/vminsights-onboard.md) <br> [啟用 Azure 監視器 VM 見解](../azure-monitor/insights/vminsights-enable-overview.md) <br> [使用單一 VM 上線](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [使用透過原則上線](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | VM 見解活頁簿  |
| DnsEvents | [連線 DNS](connect-dns.md) | V | |
| W3CIISLog | [連線 IIS 記錄](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData | [連線 Wire Data](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [連線 Windows 防火牆](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [連線 Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [連線 Azure ATP](connect-azure-atp.md) | V | |
| ASC SecurityAlert  | [連線 Azure 資訊安全中心](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [連線 Microsoft Cloud App Security](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (事件) | [連線 Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [連線 Windows 事件](../azure-monitor/platform/data-sources-windows-events.md) <br> [取得 Sysmon 剖析器](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | 預設不會在虛擬機器上安裝 Sysmon 集合。 如需有關如何安裝 Sysmon 代理程式的詳細資訊，請參閱 [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon)。 |
| ConfigurationData  | [自動執行 VM 清查](../automation/automation-vm-inventory.md)| X | |
| ConfigurationChange  | [自動執行 VM 追蹤](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [連線 F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [連線 Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>後續步驟

- 若要開始使用 Azure Sentinel，您需要 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/free/)。
- 了解如何[將資料上架到 Azure Sentinel](quickstart-onboard.md)，並[掌握您的資料和潛在威脅](quickstart-get-visibility.md)。
