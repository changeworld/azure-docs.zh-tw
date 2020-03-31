---
title: 將 Azure 監視資料串流至事件中樞
description: 瞭解如何將 Azure 監視資料流程式傳輸到事件中心，以便將資料輸入合作夥伴 SIEM 或分析工具。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 08177165439ff7d3205e31757e5d1e28759a9836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274186"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>將 Azure 監視資料串流至事件中樞
Azure 監視器為 Azure、其他雲和本地中的應用程式和服務提供了完整的完整堆疊監視解決方案。 除了使用 Azure 監視器分析該資料並將其用於不同的監視方案外，您可能需要將其發送到環境中的其他監視工具。 在大多數情況下，將監視資料流程式傳輸到外部工具的最有效方法是使用[Azure 事件中心](/azure/event-hubs/)。 本文簡要介紹了如何將監視資料從不同源資料流到事件中心，以及指向詳細指南的連結。


## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間

在為任何資料來源配置流式處理之前，需要[創建事件中心命名空間和事件中心](../../event-hubs/event-hubs-create.md)。 此命名空間和事件中樞是所有監視資料的目的地。 「事件中樞」命名空間是共用相同存取原則之事件中樞的邏輯群組，非常類似於儲存體帳戶在該儲存體帳戶內有個別的 Blob。 請考慮有關用於流式監視資料的事件中心命名空間和事件中心的詳細資訊：

* 輸送量單位數可讓您擴大事件中樞的輸送量規模。 通常只需要一個輸送量單元。 如果需要隨著日誌使用量的增加而向上擴展，可以手動增加命名空間的輸送量單位數或啟用自動膨脹。
* 分割區數可讓您跨眾多客戶平行處理取用量。 單個分區每秒最多支援 20MBps 或大約 20，000 條消息。 視取用資料的工具而定，可能支援也可能不知支援從多個分割區取用。 如果您不確定是否不確定要設置的分區數，則啟動四個分區是合理的。
* 在事件中心將郵件保留設置為至少 7 天。 如果您的消費工具出現故障超過一天，這可確保該工具可以拾起它離開的地方，以便發生長達 7 天的事件。
* 應為事件中心使用預設消費者組。 您不需要建立其他取用者群組或使用個別的取用者群組，除非您打算讓兩個不同的工具從相同的事件中樞取用相同的資料。
* 對於 Azure 活動日誌，選擇事件中心命名空間，Azure 監視器在該命名空間中創建一個事件中心，稱為_見解-日誌-動作記錄_。 對於其他日誌類型，您可以選擇現有事件中心，也可以讓 Azure 監視器為每個日誌類別創建事件中心。
* 出站埠 5671 和 5672 通常必須在電腦或 VNET 上打開，使用事件中心的資料。

## <a name="monitoring-data-available"></a>可用的監控資料
[Azure 監視器的監視資料來源](data-sources.md)描述了 Azure 應用程式的不同資料層以及每個應用程式可用的監視資料類型。 下表列出了每個層以及如何將資料流程式傳輸到事件中心的說明。 請按照提供的連結進行進一步說明。

| 層 | 資料 | 方法 |
|:---|:---|:---|
| [Azure 租用戶](data-sources.md#azure-tenant) | Azure 活動目錄稽核記錄 | 在 AAD 租戶上配置租戶診斷設置。 有關詳細資訊[，請參閱教程：將 Azure 活動目錄日誌資料流到 Azure 事件中心](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。 |
| [Azure 訂閱](data-sources.md#azure-subscription) | Azure 活動記錄檔 | 創建日誌設定檔以將活動日誌事件匯出到事件中心。  有關詳細資訊，請參閱[將 Azure 平臺日誌資料流到 Azure 事件中心](resource-logs-stream-event-hubs.md)。 |
| [Azure 資源](data-sources.md#azure-resources) | 平臺指標<br> 資源記錄 |這兩種資料都會使用資源診斷設定來傳送至事件中樞。 有關詳細資訊[，請參閱將 Azure 資源日誌資料流到事件中心](resource-logs-stream-event-hubs.md)。 |
| [作業系統（訪客）](data-sources.md#operating-system-guest) | Azure 虛擬機器 | 在 Azure 中的 Windows 和 Linux 虛擬機器上安裝[Azure 診斷擴展](diagnostics-extension-overview.md)。 有關 Windows VM 的詳細資訊[，請參閱熱路徑中的流式處理 Azure 診斷資料](diagnostics-extension-stream-event-hubs.md)，並使用[Linux 診斷擴展來監視指標和日誌](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings)以瞭解 Linux VM 的詳細資訊。 |
| [應用程式程式碼](data-sources.md#application-code) | Application Insights | 應用程式見解不提供將資料流程式傳輸到事件中心的直接方法。 您可以[設置應用程式見解資料的連續匯出](../../azure-monitor/app/export-telemetry.md)到存儲帳戶，然後使用邏輯應用將資料發送到事件中心，如[使用邏輯應用的手動流式處理](#manual-streaming-with-logic-app)中所述。 |

## <a name="manual-streaming-with-logic-app"></a>手動資料流邏輯應用程式
對於無法直接資料流到事件中心的資料，可以寫入 Azure 存儲，然後使用時間觸發的邏輯應用[從 Blob 存儲中提取資料](../../connectors/connectors-create-api-azureblobstorage.md#add-action)[並將其作為消息推送到事件中心](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)。 


## <a name="partner-tools-with-azure-monitor-integration"></a>使用 Azure 監視器集成的合作夥伴工具

使用 Azure 監視器將監視資料路由到事件中心，使您能夠輕鬆與外部 SIEM 和監視工具集成。 具有 Azure 監視器集成的工具示例包括：

| 工具 | 託管在 Azure 中 | 描述 |
|:---|:---| :---|
|  IBM QRadar | 否 | Microsoft Azure DSM 與 Microsoft Azure 事件中樞通訊協定均可從 [IBM 支援網站](https://www.ibm.com/support)下載。 您可以在[QRadar DSM 配置](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)中瞭解有關與 Azure 集成的更多資訊。 |
| Splunk | 否 | [Splunk 的 Azure 監視器載入項](https://splunkbase.splunk.com/app/3534/)是 Splunkbase 中可用的開源專案。 該文檔可在[Azure 監視器為 Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)的載入項中提供。<br><br> 如果無法在 Splunk 實例中安裝載入項，例如，如果您正在使用代理或在 Splunk 雲上運行，則可以使用["Splunk"功能為 Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS)（由事件中心中的新消息觸發）將這些事件轉發到 Splunk HTTP 事件收集器。 |
| sumologic | 否 | 設置相撲邏輯以使用事件中心的資料的說明，可在[事件中心 Azure 審核應用的收集日誌中](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)提供。 |
| ArcSight | 否 | ArcSight Azure 事件集線器智慧連接器作為[ArcSight 智慧連接器集合的一](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)部分提供。 |
| Syslog 伺服器 | 否 | 如果要將 Azure 監視器資料直接資料流到 syslog 伺服器，則可以使用基於[Azure 函數的解決方案](https://github.com/miguelangelopereira/azuremonitor2syslog/)。
| 日誌節奏 | 否| 此處[提供了設置](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)LogRhythm 以從事件中心收集日誌的說明。 
|Logz.io | 是 | 有關詳細資訊，請參閱[使用在 Azure 上運行的 JAVA 應用使用Logz.io開始監視和日誌記錄](https://docs.microsoft.com/azure/java/java-get-started-with-logzio)


## <a name="next-steps"></a>後續步驟
* [將活動日誌存檔到存儲帳戶](../../azure-monitor/platform/archive-activity-log.md)
* [閱讀 Azure 活動日誌的概述](../../azure-monitor/platform/platform-logs-overview.md)
* [基於活動日誌事件設置警報](../../azure-monitor/platform/alerts-log-webhook.md)


