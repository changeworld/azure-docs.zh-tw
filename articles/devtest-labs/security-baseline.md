---
title: 適用于 Azure DevTest Labs 的 Azure 安全性基準
description: 適用于 Azure DevTest Labs 的 Azure 安全性基準
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096330"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>適用于 Azure DevTest Labs 的 Azure 安全性基準

適用于 Azure DevTest Labs 的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自[Azure 安全性基準測試版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供有關如何在 Azure 上保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)。

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源
**指導方針：** Microsoft 會維護 Azure 資源的時間來源。 不過，您可以管理計算資源的時間同步處理設定。

請參閱下列文章，以瞭解如何設定 Azure 計算資源的時間同步處理： [azure 中 Windows vm 的時間同步](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)。 

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理
**指導方針：** 啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 活動記錄可讓您深入瞭解在管理平面層級的 Azure DevTest Labs 實例上所進行的作業。 您可以使用 Azure 活動記錄資料，針對 DevTest Labs 實例的管理平面層級，判斷任何寫入作業（PUT、POST、DELETE）的「內容、物件和時間」。

如需詳細資訊，請參閱[建立診斷設定以將平臺記錄和計量傳送至不同的目的地](../azure-monitor/platform/diagnostic-settings.md)。

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 顧客

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄
**指導方針：** 啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 活動記錄可讓您深入瞭解在管理平面層級的 Azure DevTest Labs 實例上所進行的作業。 您可以使用 Azure 活動記錄資料，針對 DevTest Labs 實例的管理平面層級，判斷任何寫入作業（PUT、POST、DELETE）的「內容、物件和時間」。

如需詳細資訊，請參閱[建立診斷設定以將平臺記錄和計量傳送至不同的目的地](../azure-monitor/platform/diagnostic-settings.md)。

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 顧客

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄
**指導方針：** 客戶會建立並擁有 Azure DevTest Labs 的虛擬機器。 因此，組織必須負責監視它。 您可以使用 Azure 資訊安全中心來監視計算 OS。 資訊安全中心從作業系統收集的資料包括作業系統類型和版本、OS （Windows 事件記錄）、執行中的進程、電腦名稱稱、IP 位址，以及登入的使用者。 Log Analytics 代理程式也會收集損毀傾印檔案。

如需詳細資訊，請參閱下列文章： 

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](../azure-monitor/learn/quick-collect-azurevm.md)
- [瞭解 Azure 資訊安全中心資料收集](../security-center/security-center-enable-data-collection.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 顧客

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期
***指導方針：** 在 Azure 監視器中，根據貴組織的合規性規定，設定與您的 Azure DevTest Labs 實例相關聯之 Log Analytics 工作區的記錄保留週期。

如需詳細資訊，請參閱下列文章：[如何設定記錄檔保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視：** 不適用

**責任：** 顧客

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄
**指導方針：** 啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區。 在 Log Analytics 中執行查詢來搜尋字詞、識別趨勢、分析模式，以及根據可能已針對 Azure DevTest Labs 收集的活動記錄資料，提供許多其他深入解析。

如需詳細資訊，請參閱下列文章：

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/diagnostic-settings.md)
- [如何在 Azure 監視器中收集和分析 Log Analytics 工作區中的 Azure 活動記錄](../azure-monitor/platform/activity-log.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 顧客

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示
**指導方針：** 使用 Azure Log Analytics 工作區來監視和警示有關安全性記錄檔中的異常活動，以及與您的 Azure DevTest Labs 相關的事件。

如需詳細資訊，請參閱下列文章：[如何在 log analytics 記錄資料上發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 顧客

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄
**指導方針：** 不適用。 Azure DevTest Labs 不會處理或產生與反惡意程式碼相關的記錄檔。

**Azure 資訊安全中心監視：** 不適用

**責任：** 顧客

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄
**指導方針：** 不適用。 Azure DevTest Labs 不會處理或產生 DNS 相關的記錄檔。

**Azure 資訊安全中心監視：** 不適用

**責任：** 顧客

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄
**指導方針：** Azure DevTest Labs 會建立客戶所擁有和管理的 Azure 電腦器。 在所有支援的 Azure Windows 虛擬機器上使用 Microsoft Monitoring Agent，以記錄處理常式建立事件和命令列欄位。 針對支援的 Azure Linux 虛擬機器，您可以手動設定每個節點的主控台記錄，並使用 Syslog 來儲存資料。 此外，使用 Azure 監視器的 Log Analytics 工作區來審查記錄，並對 Azure 虛擬機器中的已記錄資料執行查詢。

- [Azure 資訊安全中心的資料收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)
- [Azure 監視器中的 Syslog 資料來源](../azure-monitor/platform/data-sources-syslog.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 顧客

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [Azure DevTest Labs 中環境的安全性警示](environment-security-alerts.md)