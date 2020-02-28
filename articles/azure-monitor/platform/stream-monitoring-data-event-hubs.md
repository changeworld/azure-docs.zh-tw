---
title: 將 Azure 監視資料串流至事件中樞
description: 瞭解如何將 Azure 監視資料串流至事件中樞，以將資料帶入合作夥伴 SIEM 或分析工具。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: b22f779d616751ebaa3dad853d5aa23ec4969f23
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658858"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>將 Azure 監視資料串流至事件中樞
Azure 監視器為 Azure、其他雲端和內部部署中的應用程式和服務，提供完整的完整堆疊監視解決方案。 除了使用 Azure 監視器來分析該資料，並將它運用在不同的監視案例中，您可能需要將它傳送至環境中的其他監視工具。 在大部分情況下，將監視資料串流至外部工具的最有效方法是使用[Azure 事件中樞](/azure/event-hubs/)。 本文提供如何將來自不同來源的監視資料串流至事件中樞的簡短描述，以及詳細指引的連結。


## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間

針對任何資料來源設定串流之前，您必須先[建立事件中樞命名空間和事件中樞](../../event-hubs/event-hubs-create.md)。 此命名空間和事件中樞是所有監視資料的目的地。 「事件中樞」命名空間是共用相同存取原則之事件中樞的邏輯群組，非常類似於儲存體帳戶在該儲存體帳戶內有個別的 Blob。 請考慮下列有關用來串流處理監視資料的事件中樞命名空間和事件中樞的詳細資料：

* 輸送量單位數可讓您擴大事件中樞的輸送量規模。 通常只需要一個輸送量單位。 如果您需要在記錄使用量增加時相應增加，您可以手動增加命名空間的輸送量單位數目，或啟用自動擴大。
* 分割區數可讓您跨眾多客戶平行處理取用量。 單一資料分割最多可支援20MBps 或大約每秒20000個訊息。 視取用資料的工具而定，可能支援也可能不知支援從多個分割區取用。 如果您不確定要設定的分割區數目，則可合理啟動四個分割區。
* 您在事件中樞至少設定了7天的訊息保留期。 如果您的取用工具停機時間超過一天，這可確保此工具可以從停止的事件（最多7天）繼續進行。
* 您應該使用事件中樞的預設取用者群組。 您不需要建立其他取用者群組或使用個別的取用者群組，除非您打算讓兩個不同的工具從相同的事件中樞取用相同的資料。
* 針對 Azure 活動記錄，您可以挑選事件中樞命名空間，Azure 監視器會在該命名空間內建立名為 [深入解析-_記錄檔-作業記錄_] 的事件中樞。 針對其他記錄類型，您可以選擇現有的事件中樞，或讓 Azure 監視器為每個記錄類別建立事件中樞。
* 輸出埠5671和5672通常必須在電腦或從事件中樞取用資料的 VNET 上開啟。

## <a name="monitoring-data-available"></a>可用的監視資料
[適用于 Azure 監視器的監視資料來源](data-sources.md)會描述 Azure 應用程式的不同資料層，以及適用于各項的監視資料類型。 下表列出每個層級，以及如何將該資料串流處理至事件中樞的說明。 請遵循提供的連結以取得進一步的詳細資料。

| 層 | Data | 方法 |
|:---|:---|:---|
| [Azure 租使用者](data-sources.md#azure-tenant) | Azure Active Directory audit 記錄檔 | 在您的 AAD 租使用者上設定租使用者診斷設定。 如需詳細資訊，請參閱[教學課程：將 Azure Active Directory 記錄串流至 Azure 事件中樞](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。 |
| [Azure 訂用帳戶](data-sources.md#azure-subscription) | Azure 活動記錄檔 | 建立記錄設定檔，將活動記錄檔事件匯出至事件中樞。  如需詳細資訊，請參閱[將 Azure 活動記錄匯出至儲存體或 Azure 事件中樞](activity-log-export.md)。 |
| [Azure 資源](data-sources.md#azure-resources) | 平臺計量<br> 資源記錄 |這兩種資料都會使用資源診斷設定來傳送至事件中樞。 如需詳細資訊，請參閱[將 Azure 資源記錄串流至事件中樞](resource-logs-stream-event-hubs.md)。 |
| [作業系統（來賓）](data-sources.md#operating-system-guest) | Azure 虛擬機器 | 在 Azure 中的 Windows 和 Linux 虛擬機器上安裝[Azure 診斷延伸](diagnostics-extension-overview.md)模組。 如需有關 Windows Vm 的詳細資料，請參閱[使用事件中樞在最忙碌路徑中串流 Azure 診斷資料](diagnostics-extension-stream-event-hubs.md)，並[使用 linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings)檔，以取得 Linux vm 的詳細資訊。 |
| [應用程式代碼](data-sources.md#application-code) | Application Insights | Application Insights 不會提供將資料串流至事件中樞的直接方法。 您可以設定將 Application Insights 資料[連續匯出](../../azure-monitor/app/export-telemetry.md)至儲存體帳戶，然後使用邏輯應用程式將資料傳送至事件中樞，如[使用邏輯應用程式手動串流](#manual-streaming-with-logic-app)中所述。 |

## <a name="manual-streaming-with-logic-app"></a>使用邏輯應用程式手動串流
對於無法直接串流至事件中樞的資料，您可以寫入至 Azure 儲存體，然後使用時間觸發的邏輯應用程式，[從 blob 儲存體提取資料](../../connectors/connectors-create-api-azureblobstorage.md#add-action)，並將[它以訊息的形式推送至事件中樞](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)。 


## <a name="partner-tools-with-azure-monitor-integration"></a>具有 Azure 監視器整合的合作夥伴工具

使用 Azure 監視器將監視資料路由傳送至事件中樞，可讓您輕鬆地與外部 SIEM 和監視工具整合。 具有 Azure 監視器整合的工具範例包括下列各項：

| 工具 | 託管于 Azure | 描述 |
|:---|:---| :---|
|  IBM QRadar | 否 | Microsoft Azure DSM 與 Microsoft Azure 事件中樞通訊協定均可從 [IBM 支援網站](https://www.ibm.com/support)下載。 您可以在[QRADAR DSM](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)設定深入瞭解與 Azure 的整合。 |
| Splunk | 否 | [適用于 Splunk 的 Azure 監視器附加](https://splunkbase.splunk.com/app/3534/)元件是 splunkbase 取得中提供的開放原始碼專案。 檔可在 Splunk 的[Azure 監視器](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)附加元件中取得。<br><br> 如果您無法在 Splunk 實例中安裝附加元件（例如，您使用 proxy 或在 Splunk Cloud 上執行），您可以使用[適用于 Splunk 的 Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS)函式將這些事件轉送至 Splunk HTTP 事件收集器，此功能是由事件中樞的新訊息所觸發。 |
| sumologic | 否 | [從事件中樞收集 Azure Audit 應用程式的記錄](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)中有提供設定 SumoLogic 以取用來自事件中樞之資料的指示。 |
| ArcSight | 否 | ArcSight Azure 事件中樞智慧連接器可作為[ArcSight 智慧連接器集合](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)的一部分。 |
| Syslog 伺服器 | 否 | 如果您想要將 Azure 監視器資料直接串流到 syslog 伺服器，您可以使用以[Azure function 為基礎的解決方案](https://github.com/miguelangelopereira/azuremonitor2syslog/)。
| LogRhythm | 否| [這裡](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)提供設定 LogRhythm 以從事件中樞收集記錄的指示。 
|Logz.io | 是 | 如需詳細資訊，請參閱[針對在 Azure 上執行的 JAVA 應用程式使用 Logz.io 開始進行監視和記錄](https://docs.microsoft.com/azure/java/java-get-started-with-logzio)


## <a name="next-steps"></a>後續步驟
* [將活動記錄檔封存至儲存體帳戶](../../azure-monitor/platform/archive-activity-log.md)
* [閱讀 Azure 活動記錄的總覽](../../azure-monitor/platform/platform-logs-overview.md)
* [根據活動記錄事件設定警示](../../azure-monitor/platform/alerts-log-webhook.md)


