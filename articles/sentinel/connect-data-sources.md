---
title: 將資料來源連線至 Azure Sentinel | Microsoft Docs
description: 瞭解如何將資料來源（像是 Microsoft 365 Defender (之前的 Microsoft 威脅防護) 、Microsoft 365 和 Office 365、Azure AD、ATP 以及 Cloud App Security）連接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: e294423e3f078253984dc4fcd8695c2676abb236
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541252"
---
# <a name="connect-data-sources"></a>連線資料來源

啟用 Azure Sentinel 之後，您需要做的第一件事就是連接您的資料來源。 Azure Sentinel 隨附許多適用于 Microsoft 解決方案的連接器，並提供即時整合，包括 Microsoft 365 Defender (之前 Microsoft 威脅防護) 解決方案、Microsoft 365 來源 (，包括 Office 365) 、Azure AD、適用于身分識別 (的 Microsoft Defender Azure ATP) 、Microsoft Cloud App Security 等等。 此外，還有適用於非 Microsoft 解決方案的內建連接器，用於連線至更廣泛的安全性生態系統。 您也可以使用常見事件格式 (CEF) (Syslog 或 REST API) 來連線資料來源與 Azure Sentinel。

1. 在功能表上，選取 [資料連接器]。 此頁面可讓您查看 Azure Sentinel 提供的連接器完整清單及其狀態。 選取您想要連線的連接器，然後選取 [開啟連接器頁面]。 

   ![資料連線器資源庫](./media/collect-data/collect-data-page.png)

1. 在特定連接器頁面上，確定您已符合所有必要條件，並遵循指示將資料連線到 Azure Sentinel。 可能需要一些時間，記錄才能開始與 Azure Sentinel 同步處理。 連線之後，您會在 [接收的資料] 圖表中看見資料摘要，以及資料類型的連線狀態。

   ![設定資料連線器](./media/collect-data/opened-connector-page.png)
  
1. 按一下 [後續步驟] 索引標籤，以取得 Azure Sentinel 針對特定資料類型提供的立即可用內容清單。

   ![連接器的後續步驟](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>資料連線方法

Azure Sentinel 支援下列資料連線方法：

- **服務對服務整合**：<br> 某些服務會以原生方式連線 (例如 AWS 和 Microsoft 服務)，這些服務會運用 Azure 基礎進行立即整合，只需按幾下就可以與下列解決方案連線：
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - [Azure Active Directory](connect-azure-active-directory.md) -audit 記錄和登入記錄
    - [Azure 活動](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure DDoS 保護](connect-azure-ddos-protection.md)
    - [適用于 IoT 的 Azure Defender](connect-asc-iot.md) (先前適用於 IoT 的 Azure 資訊安全中心) 
    - [Azure 資訊保護](connect-azure-information-protection.md)
    - [Azure 防火牆](connect-azure-firewall.md)
    - [Azure 資訊安全中心](connect-azure-security-center.md) -Azure Defender 解決方案的警示
    - [Azure Web 應用程式防火牆 (WAF) ](connect-azure-waf.md) (前身為 Microsoft WAF) 
    - [Cloud App Security](connect-cloud-app-security.md)
    - [網域名稱伺服器](connect-dns.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) -包含 MDATP 原始資料
    - [適用于端點 (的 Microsoft Defender](connect-microsoft-defender-advanced-threat-protection.md) 先前 Microsoft Defender 進階威脅防護) 
    - [適用于 Identity (的 Microsoft Defender](connect-azure-atp.md) 先前 Azure 進階威脅防護) 
    - [適用于 office 365 的 Microsoft Defender](connect-office-365-advanced-threat-protection.md) (先前的 Office 365 Advanced 威脅防護) 
    - 現在有團隊的[Office 365](connect-office-365.md) (！ ) 
    - [Windows 防火牆](connect-windows-firewall.md)
    - [Windows 安全性事件](connect-windows-security-events.md)

- **透過 API 連線的外部解決方案**：某些資料來源會使用自身提供的 API 進行連線。 一般而言，大部分的安全性技術都會提供一組 API，藉此來擷取事件記錄。API 可連線到 Azure Sentinel 並收集特定資料類型，然後將其傳送至 Azure Log Analytics。 透過 API 連線的設備包括：
    
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [更好的行動裝置威脅防護](connect-better-mtd.md)
    - [Beyond Security beSECURE](connect-besecure.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [周邊 81 個記錄](connect-perimeter-81-logs.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [VMware Carbon Black Cloud Endpoint Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- 透過 **代理程式的外部解決方案**： Azure Sentinel 可以透過代理程式連接到任何其他可使用 Syslog 通訊協定執行即時記錄資料流程的資料來源。

    大部分設備會使用 Syslog 通訊協定來傳送事件訊息，包含記錄本身和記錄的相關資料。 記錄檔的格式會有所不同，但大部分的設備也都支援記錄資料以 CEF 為基礎的格式。 

    Azure Sentinel 代理程式（實際上是 Log Analytics 代理程式）會將 CEF 格式的記錄轉換成 Log Analytics 可內嵌的格式。 視裝置類型而定，代理程式會直接安裝在設備上，或安裝在專用的 Linux 記錄轉寄站上。 適用於 Linux 的代理程式會透過 UDP 從 Syslog 精靈接收事件，但如果預期 Linux 機器會收集大量的 Syslog 事件，則會透過 TCP 將事件從 Syslog 精靈傳送到代理程式，然後從該處傳送至 Log Analytics。

    - **防火牆、proxy 和端點-CEF：**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Citrix WAF](connect-citrix-waf.md)
        - [CyberArk Enterprise Password Vault](connect-cyberark.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Forcepoint 產品](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [趨勢科技 Deep Security](connect-trend-micro.md)
        - [趨勢微 TippingPoint](connect-trend-micro-tippingpoint.md)
        - [WireX 網路辯論平臺](connect-wirex-systems.md)
        - [Zscaler](connect-zscaler.md)
        - [其他以 CEF 為基礎的設備](connect-common-event-format.md)
    - **防火牆、proxy 和端點-Syslog：**
        - [Infoblox NIOS](connect-infoblox.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos XG](connect-sophos-xg-firewall.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [其他 Syslog 型設備](connect-syslog.md)
    - DLP 解決方案
    - [威脅情報提供者](connect-threat-intelligence.md)
    - [DNS 機器](connect-dns.md) - 直接在 DNS 機器上安裝代理程式
    - [Azure Stack Vm](connect-azure-stack.md)
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
| AWSCloudTrail | [連線 AWS](connect-aws.md) | &#10003; | |
| AzureActivity | [連線 Azure 活動](connect-azure-activity.md)和[活動記錄概觀](../azure-monitor/platform/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [Connect Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Connect Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Azure 診斷](../firewall/firewall-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Azure 資訊保護報告](/azure/information-protection/reports-aip)<br>[連線 Azure 資訊保護](connect-azure-information-protection.md)  | &#10003; | 除了資料類型之外，這通常會使用 **InformationProtectionEvents** 函式。 如需詳細資訊，請參閱[如何修改報告及建立自訂查詢](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [流量分析結構描述](../network-watcher/traffic-analytics.md) [流量分析](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [連線 CEF](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [連線 Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [連線 Windows 安全性事件](connect-windows-security-events.md)  | &#10003; | 若為不安全的通訊協定活頁簿，請參閱[不安全的通訊協定活頁簿設定](./quickstart-get-visibility.md#use-built-in-workbooks)  |
| syslog | [連線 Syslog](connect-syslog.md) | &#10003; | |
| Microsoft Web 應用程式防火牆 (WAF) - (AzureDiagnostics) |[連線 Microsoft Web 應用程式防火牆](./connect-azure-waf.md) | &#10003; | |
| SymantecICDx_CL | [連線 Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [連線威脅情報](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure 監視服務對應](../azure-monitor/insights/service-map.md)<br>[Azure 監視器 VM 見解上線](../azure-monitor/insights/vminsights-enable-overview.md) <br> [啟用 Azure 監視器 VM 見解](../azure-monitor/insights/vminsights-enable-overview.md) <br> [使用單一 VM 上線](../azure-monitor/insights/vminsights-enable-portal.md)<br>  [使用透過原則上線](../azure-monitor/insights/vminsights-enable-policy.md)| &#10007; | VM 見解活頁簿  |
| DnsEvents | [連線 DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [連線 IIS 記錄](../azure-monitor/platform/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [連線 Wire Data](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [連線 Windows 防火牆](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [連線 Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [將適用于身分識別 (的 Microsoft Defender 連接](connect-azure-atp.md) 先前 Azure ATP)  | &#10003; | |
| ASC SecurityAlert  | 從 Azure 資訊安全中心連線[到 Azure Defender 警示](connect-azure-security-center.md)  | &#10003; | |
| MCAS SecurityAlert  | [連線 Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (事件) | [連線 Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [連線 Windows 事件](../azure-monitor/platform/data-sources-windows-events.md) <br> [取得 Sysmon 剖析器](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | 預設不會在虛擬機器上安裝 Sysmon 集合。 如需有關如何安裝 Sysmon 代理程式的詳細資訊，請參閱 [Sysmon](/sysinternals/downloads/sysmon)。 |
| ConfigurationData  | [自動執行 VM 清查](../automation/change-tracking/overview.md)| &#10007; | |
| ConfigurationChange  | [自動執行 VM 追蹤](../automation/change-tracking/overview.md) | &#10007; | |
| F5 BIG-IP | [連線 F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [連線 Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>後續步驟

- 若要開始使用 Azure Sentinel，您需要 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/free/)。
- 了解如何[將資料上架到 Azure Sentinel](quickstart-onboard.md)，並[掌握您的資料和潛在威脅](quickstart-get-visibility.md)。