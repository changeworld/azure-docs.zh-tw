---
title: Azure 安全控制 - 日誌記錄和監視
description: 安全控制日誌記錄和監視
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545495"
---
# <a name="security-control-logging-and-monitoring"></a>安全控制：日誌記錄和監視

安全性記錄檔記錄和監視側重于與為 Azure 服務啟用、獲取和存儲稽核記錄相關的活動。

## <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的時間同步源

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft 維護 Azure 資源的時間源，但是，您可以選擇管理計算資源的時間同步設置。

如何為 Azure 計算資源配置時間同步：

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全性記錄檔管理

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.2 | 6.5, 6.6 | 客戶 |

通過 Azure 監視器引入日誌以聚合終結點設備、網路資源和其他安全系統生成的安全資料。 在 Azure 監視器中，使用日誌分析工作區來查詢和執行分析，並使用 Azure 存儲帳戶進行長期/存檔存儲。

或者，您可以將資料啟用到 Azure Sentinel 或協力廠商 SIEM。 如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

如何使用 Azure 監視器收集平臺日誌和指標：

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

如何使用 Azure 監視器收集 Azure 虛擬機器內部主機日誌：

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

如何開始使用 Azure 監視器和協力廠商 SIEM 集成：

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：為 Azure 資源啟用稽核記錄記錄

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.3 | 6.2, 6.3 | 客戶 |

在 Azure 資源上啟用診斷設置，以訪問審核、安全和診斷日誌。 活動日誌自動可用，包括事件源、日期、使用者、時間戳記、源位址、目標位址和其他有用元素。

如何使用 Azure 監視器收集平臺日誌和指標：

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

瞭解 Azure 中的日誌記錄和不同的日誌類型：

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 從作業系統收集安全性記錄檔

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.4 | 6.2, 6.3 | 客戶 |

如果計算資源歸 Microsoft 所有，則 Microsoft 負責監視它。 如果計算資源歸組織所有，則您有責任對其進行監視。 可以使用 Azure 安全中心監視作業系統。 安全中心從作業系統收集的資料包括作業系統類型和版本、作業系統日誌（Windows 事件日誌）、正在運行的進程、電腦名稱稱、IP 位址和登錄使用者。 日誌分析代理還會收集崩潰轉儲檔。

如何使用 Azure 監視器收集 Azure 虛擬機器內部主機日誌：

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

瞭解 Azure 安全中心資料收集：

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全性記錄檔存儲保留

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.5 | 6.4 | 客戶 |

在 Azure 監視器中，根據組織的合規性法規設置日誌分析工作區保留期。 使用 Azure 存儲帳戶進行長期/存檔存儲。

如何為日誌分析工作區設置日誌保留參數：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6： 監視和查看日誌

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.6 | 6.7 | 客戶 |

分析和監視日誌是否存在異常行為，並定期查看結果。 使用 Azure 監視器的日誌分析工作區查看日誌並執行日誌資料的查詢。

或者，您可以將資料啟用並車載到 Azure Sentinel 或協力廠商 SIEM。 

如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

瞭解日誌分析工作區：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢：

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 啟用異常活動的警報

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.7 | 6.8 | 客戶 |

使用 Azure 安全中心與日誌分析工作區監視和警報安全性記錄檔和事件中的異常活動。

或者，您可以將資料啟用和車載到 Azure Sentinel。

如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

如何管理 Azure 安全中心的警報：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

如何對日誌分析日誌資料發出警報：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反惡意軟體日誌記錄

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.8 | 8.6 | 客戶 |

為 Azure 虛擬機器和雲服務啟用反惡意軟體事件集合。

如何為虛擬機器配置 Microsoft 反惡意軟體：

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

如何為雲服務配置 Microsoft 反惡意軟體：

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

瞭解微軟反惡意軟體：

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9： 啟用 DNS 查詢日誌記錄

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.9 | 8.7 | 客戶 |

為 DNS 日誌記錄實施協力廠商解決方案。

## <a name="210-enable-command-line-audit-logging"></a>2.10： 啟用命令列稽核記錄記錄

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 2.1 | 8.8 | 客戶 |

根據每個節點手動設定主控台日誌記錄和 PowerShell 轉錄。

## <a name="next-steps"></a>後續步驟

請參閱下一個安全控制：[標識和存取控制](security-control-identity-access-control.md)
