---
title: Azure Sentinel 資料來源架構參考
description: 本文列出 Azure Sentinel 所支援的 Azure 和協力廠商資料來源架構，以及其參考檔的連結。
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 32ec711309190d4bb7c6c98a05b6a75cf88a8f5c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920498"
---
# <a name="data-source-schema-reference"></a>資料來源架構參考

本文列出支援的 Azure 和協力廠商資料來源架構，以及其參考檔的連結。

## <a name="azure-data-sources"></a>Azure 資料來源

| 類型                             | 資料來源             | Log Analytics tablename | 結構描述參考 |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Azure AD 活動報告登入屬性](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure 監視器 AuditLogs 參考](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure 監視器 AzureActivity 參考](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Office 365 管理活動 API 架構： <br>- [一般架構 ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange 系統管理員架構 ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange 信箱架構](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [SharePoint 基底架構](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint 檔案作業](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure 金鑰保存庫         | AzureDiagnostics       | [Azure 監視器 AzureDiagnostics 參考](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **主控件**                             | Linux                  | syslog                 | [Azure 監視器 Syslog 參考](/azure/azure-monitor/reference/tables/syslog) |
| **Network**                          | IIS 記錄               | W3CIISLog              | [Azure 監視器 W3CIISLog 參考](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Network**                          | VMinsights             | VMConnection           | [Azure 監視器 VMConnection 參考](/azure/azure-monitor/reference/tables/vmconnection) |
| **Network**                          | 線路資料解決方案     | WireData               | [Azure 監視器 WireData 參考](/azure/azure-monitor/reference/tables/wiredata) |
| **Network**                          | NSG 流量記錄          | AzureNetworkAnalytics  | [流量分析中的架構和資料匯總](/azure/network-watcher/traffic-analytics-schema) |
| | | | |

## <a name="3rd-party-vendor-data-sources"></a>協力廠商資料來源

下表列出支援的協力廠商廠商及其 Syslog 或常見事件格式 (CEF 各種支援的記錄類型) 對應檔，其中包含每個類別目錄類型的 CEF 欄位對應和範例記錄。

| 類型 |    廠商 |    Product | Log Analytics tablename | CEF 欄位對應參考  |
| ----- | ----- | ----- | ----- |----- |
| **Network** | Palo Alto   | 平移 OS    | CommonSecurityLog |   [平移-作業系統9.0 常見事件格式整合指南](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (搜尋 *CEF 樣式的記錄格式*)  |
| **Network** | Check Point  |ALL   | CommonSecurityLog | [記錄欄位描述](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Network** | Fortigate   | ALL   | CommonSecurityLog | [記錄架構結構](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Network** | Barracuda | Web 應用程式防火牆 |  CommonSecurityLog   | [如何設定 Syslog 和其他記錄](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Network** | Cisco | ASA | CommonSecurityLog | [Cisco ASA 系列 Syslog 訊息](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Network** | Cisco | 火力   | CommonSecurityLog | [Cisco Firepower 威脅防護 Syslog 訊息](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Network** | Cisco   | 傘  | 自訂記錄資料表  | [記錄格式和版本控制](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Network**   | Cisco | Meraki    | CommonSecurityLog |   [Syslog 事件種類和記錄範例](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Network**   | Zscaler | Nano 串流服務 (NSS) |   CommonSecurityLog | 將[NSS 摘要格式化](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (Web、防火牆、DNS 和通道記錄檔)  |
| **Network**   |F5 | BigIP LTM|    CommonSecurityLog|  [事件訊息和攻擊類型](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Network** | F5  | BigIP ASM|    CommonSecurityLog|  [記錄應用程式安全性事件](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Network** | Citrix  |Web 應用程式防火牆   | CommonSecurityLog|    [ (在應用程式防火牆中 CEF) 記錄支援的常見事件格式](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12.0 Syslog 訊息參考](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**主控件** |Symantec | Symantec Endpoint Protection Manager (SEPM)  | CommonSecurityLog|[Endpoint Protection Manager 的外部記錄設定和記錄事件嚴重性層級](https://support.symantec.com/us/en/article.tech171741.html)|
|**主控件** |趨勢科技 |全部 |CommonSecurityLog | [Syslog 內容對應-CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>後續步驟

深入瞭解支援的 Azure Sentinel 連接器，例如 CEF、Syslog、direct、agent 和自訂連接器：

- [連線資料來源](connect-data-sources.md)

- [Azure Sentinel Syslog、CEF 和其他協力廠商連接器](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)