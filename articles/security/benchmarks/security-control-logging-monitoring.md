---
title: Azure 安全性控制-記錄和監視
description: Azure 安全性控制記錄和監視
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e58df0997c62a131a3c9987369f8e076a62d6654
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408349"
---
# <a name="security-control-logging-and-monitoring"></a>安全性控制：記錄和監視

安全性記錄和監視著重于與啟用、取得及儲存 Azure 服務的審核記錄相關的活動。

## <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

不過，Microsoft 會維護 Azure 資源的時間來源，您可以選擇管理計算資源的時間同步處理設定。

- [如何設定 Azure Windows 計算資源的時間同步處理](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [如何設定 Azure Linux 計算資源的時間同步處理](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.2 | 6.5、6。6 | 客戶 |

透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶進行長期/封存儲存。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何使用 Azure 監視器收集平臺記錄和計量](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.3 | 6.2、6。3 | 客戶 |

啟用 Azure 資源的診斷設定，以存取 audit、security 和診斷記錄。 活動記錄 (自動提供) 包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址，以及其他有用的項目。

- [如何使用 Azure 監視器收集平臺記錄和計量](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [瞭解 Azure 中的記錄和不同的記錄類型](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.4 | 6.2、6。3 | 客戶 |

如果計算資源是由 Microsoft 所擁有，則 Microsoft 會負責監視它。 如果計算資源由您的組織所擁有，則您必須負責監視它。 您可以使用 Azure 資訊安全中心來監視 OS。 資訊安全中心從作業系統收集的資料包括作業系統類型和版本、OS （Windows 事件記錄）、執行中的進程、電腦名稱稱、IP 位址，以及登入的使用者。 Log Analytics 代理程式也會收集損毀傾印檔案。

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [瞭解 Azure 資訊安全中心資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.5 | 6.4 | 客戶 |

在 Azure 監視器中，根據貴組織的合規性法規來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

- [變更 Log Analytics 中的資料保留期限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.6 | 6.7 | 客戶 |

分析和監視記錄中的異常行為，並定期查看結果。 使用 Azure 監視器的 Log Analytics 工作區來審查記錄，並對記錄資料執行查詢。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [了解 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [如何在 Azure 監視器中執行自訂查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.7 | 6.8 | 客戶 |

使用 Azure 資訊安全中心搭配 Log Analytics 工作區，以在安全性記錄和事件中發現的異常活動上進行監視和警示。

或者，您可以啟用和麵板上的資料來 Azure Sentinel。

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何管理 Azure 資訊安全中心中的警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [如何對 log analytics 記錄資料發出警示](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.8 | 8.6 | 客戶 |

針對 Azure 虛擬機器和雲端服務啟用反惡意程式碼事件收集。

- [如何設定虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [如何設定雲端服務的 Microsoft Antimalware](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [瞭解 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.9 | 8.7 | 客戶 |

根據您的組織需求，從 Azure Marketplace 針對 DNS 記錄解決方案來執行協力廠商解決方案。  

## <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 2.10 | 8.8 | 客戶 |

在所有支援的 Azure Windows 虛擬機器上使用 Microsoft Monitoring Agent，以記錄處理常式建立事件和命令列欄位。   針對支援的 Azure Linux 虛擬機器，您可以手動設定每個節點的主控台記錄，並使用 Syslog 來儲存資料。  此外，使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並對 Azure 虛擬機器中的記錄資料執行查詢。 

- [Azure 資訊安全中心的資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [如何在 Azure 監視器中執行自訂查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Azure 監視器中的 Syslog 資料來源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：身分[識別和存取控制](security-control-identity-access-control.md)