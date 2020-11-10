---
title: Azure 安全性基準測試 V2-記錄和威脅偵測
description: Azure 安全性基準測試 V2 記錄和威脅偵測
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 99e7a8fcb34c963cfab366f1913508a7ff3f4a51
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408697"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>安全性控制 V2：記錄和威脅偵測

記錄和威脅偵測涵蓋了在 Azure 上偵測威脅，以及啟用、收集和儲存 Azure 服務之審核記錄的控制項。 這包括使用控制項來啟用偵測、調查和補救程式，以在 Azure 服務中產生具有原生威脅偵測的高品質警示;它也包括使用 Azure 監視器收集記錄，並使用 Azure Sentinel、時間同步處理和記錄保留來集中進行安全性分析。 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：啟用 Azure 資源的威脅偵測

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| LT-1 | 6.7 | AU-3，AU-6，AU-12，SI-4 |

確定您正在監視不同類型的 Azure 資產，以找出潛在的威脅和異常。 專注于取得高品質的警示，以減少因分析師排序的誤報。 警示可能源自于記錄資料、代理程式或其他資料。

使用 Azure 資訊安全中心內建的威脅偵測功能，這項功能是以監視 Azure 服務遙測和分析服務記錄為基礎。 您可以使用 Log Analytics 代理程式來收集資料，該代理程式會從系統讀取各種安全性相關設定和事件記錄檔，並將資料複製到您的工作區以進行分析。 

此外，您也可以使用 Azure Sentinel 來建立分析規則，以搜尋符合您環境內特定準則的威脅。 規則會在符合準則時產生事件，讓您可以調查每個事件。 Azure Sentinel 也可以匯入協力廠商威脅情報，以增強其威脅偵測功能。 

- [Azure 資訊安全中心內的威脅防護](../../security-center/azure-defender.md)

- [Azure 資訊安全中心安全性警示參考指南](../../security-center/alerts-reference.md)

- [建立自訂分析規則來偵測威脅](../../sentinel/tutorial-detect-threats-custom.md)

- [使用 Azure Sentinel 的網路威脅情報](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：啟用 Azure 身分識別與存取管理的威脅偵測

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| LT-2 | 6.8 | AU-3，AU-6，AU-12，SI-4 |

Azure AD 提供下列使用者記錄檔，可在 Azure AD 報告中查看或與 Azure 監視器、Azure Sentinel 或其他 SIEM/監視工具整合，以取得更精密的監視和分析使用案例： 
-   登入 – 登入報告會提供受控應用程式和使用者登入活動的使用情況相關資訊。

-   稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

-   有風險的登入 - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

-   標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

Azure 資訊安全中心也可能會對某些可疑活動發出警示，例如驗證嘗試失敗的次數過多，以及訂用帳戶中已淘汰的帳戶。 除了基本的安全性防護監視之外，Azure 資訊安全中心的威脅防護模組也可以從個別的 Azure 計算資源收集更深入的安全性警示 (例如虛擬機器、容器、app service) 、資料資源 (例如 SQL DB 和儲存體) ，以及 Azure 服務層。 這項功能可讓您查看個別資源內的帳戶異常。

- [Azure AD 中的審核活動報告](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [啟用 Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 資訊安全中心內的威脅防護](../../security-center/azure-defender.md)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：啟用 Azure 網路活動的記錄功能

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| LT-3 | 9.3、12.2、12.5、12。8 | AU-3，AU-6，AU-12，SI-4 |

啟用和收集網路安全性群組 (NSG) 資源記錄、NSG 流量記錄、Azure 防火牆記錄和 Web 應用程式防火牆 (WAF) 記錄以進行安全性分析，以支援事件調查、威脅搜尋和安全性警示產生。 您可以將流量記錄傳送至 Azure 監視器 Log Analytics 工作區，然後使用流量分析來提供見解。 確定您正在收集 DNS 查詢記錄，以協助將其他網路資料相互關聯。

- [如何啟用網路安全性群組流量記錄](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure 防火牆記錄和計量](../../firewall/logs-and-metrics.md)

- [如何啟用和使用流量分析](../../network-watcher/traffic-analytics.md)

- [使用網路監看員進行監視](../../network-watcher/network-watcher-monitoring-overview.md)

- [Azure 監視器中的 Azure 網路監視解決方案](../../azure-monitor/insights/azure-networking-analytics.md)

- [使用 DNS 分析解決方案收集您的 DNS 基礎結構的深入解析](../../azure-monitor/insights/dns-analytics.md)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| LT-4 | 6.2、6.3、8。8 | AU-3，AU-12 |

啟用 Azure 資源的記錄，以符合合規性、威脅偵測、搜尋和事件調查的需求。 

您可以使用 Azure 資訊安全中心和 Azure 原則來啟用在 Azure 資源上收集的資源記錄和記錄資料，以存取審核、安全性和資源記錄。 活動記錄 (自動提供) 包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址，以及其他有用的項目。 

- [瞭解 Azure 中的記錄和不同的記錄類型](../../azure-monitor/platform/platform-logs-overview.md)

- [瞭解 Azure 資訊安全中心資料收集](../../security-center/security-center-enable-data-collection.md)

**責任** ：共用

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

基礎結構和端點安全性 

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中安全性記錄檔管理和分析

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| LT-5 | 6.5、6。6 | AU-3、SI-4 |

集中記錄儲存體和分析以啟用相互關聯。 針對每個記錄來源，請確定您已指派資料擁有者、存取指引、儲存位置、使用哪些工具來處理和存取資料，以及資料保留需求。

確定您要將 Azure 活動記錄整合到您的集中記錄。 透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。

此外，請啟用資料並將其上架至 Azure Sentinel 或協力廠商 SIEM。

許多組織選擇使用 Azure Sentinel 「經常性存取」資料，而這些資料經常使用，並 Azure 儲存體使用較不常使用的「冷」資料。 

- [如何使用 Azure 監視器收集平臺記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6：設定記錄儲存體保留期

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| LT-6 | 6.4 | AU-3，AU-11 |

根據您的合規性、法規和商務需求來設定記錄保留期。 

在 Azure 監視器中，您可以根據組織的合規性法規來設定 Log Analytics 工作區保留期限。 使用 Azure 儲存體、Data Lake 或 Log Analytics 工作區帳戶來取得長期和封存儲存體。

- [變更 Log Analytics 中的資料保留期限](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure 資訊安全中心警示和建議匯出](../../security-center/continuous-export.md)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7：使用已核准的時間同步處理來源

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Microsoft 會維護大部分 Azure PaaS 和 SaaS 服務的時間來源。 針對您的虛擬機器，除非您有特定需求，否則請使用 Microsoft 預設 NTP 伺服器進行時間同步處理。  如果您需要建立自己的網路時間通訊協定 (NTP) server，請確定您已保護 UDP 服務埠123的安全。

由 Azure 中的資源所產生的所有記錄都會提供時間戳記與預設指定的時區。

- [如何設定 Azure Windows 計算資源的時間同步處理](../../virtual-machines/windows/time-sync.md)

- [如何設定 Azure Linux 計算資源的時間同步處理](../../virtual-machines/linux/time-sync.md)

- [如何停用 Azure 服務的輸入 UDP](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**責任** ：共用

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [原則和標準](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)