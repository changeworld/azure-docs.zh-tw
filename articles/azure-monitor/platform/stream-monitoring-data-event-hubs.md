---
title: 將 Azure 監視資料串流至事件中樞和外部合作夥伴
description: 瞭解如何將 Azure 監視資料串流至事件中樞，以將資料放入合作夥伴 SIEM 或分析工具。
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.subservice: ''
ms.openlocfilehash: f6272e3d976c7c3b04d5b1332e2d7b3410c3045c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318873"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>將 Azure 監視資料串流至事件中樞或外部合作夥伴

Azure 監視器針對 Azure、其他雲端和內部部署中的應用程式和服務，提供完整的完整堆疊監視解決方案。 除了使用 Azure 監視器來分析該資料並將其運用於不同的監視案例中，您可能需要將它傳送至您環境中的其他監視工具。 在大部分的情況下，將監視資料串流至外部工具最有效的方法是使用 [Azure 事件中樞](../../event-hubs/index.yml)。 本文提供如何進行這項操作的簡短描述，並列出您可以傳送資料的部分夥伴。 有些與 Azure 監視器有特殊的整合，而且可能裝載在 Azure 上。  

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間

設定任何資料來源的串流之前，您必須先 [建立事件中樞命名空間和事件中樞](../../event-hubs/event-hubs-create.md)。 此命名空間和事件中樞是所有監視資料的目的地。 「事件中樞」命名空間是共用相同存取原則之事件中樞的邏輯群組，非常類似於儲存體帳戶在該儲存體帳戶內有個別的 Blob。 請考慮下列有關事件中樞命名空間的詳細資料，以及用於串流處理監視資料的事件中樞：

* 輸送量單位數可讓您擴大事件中樞的輸送量規模。 通常只需要一個輸送量單位。 如果您需要在記錄使用量增加時擴大，您可以手動增加命名空間的輸送量單位數目，或啟用自動擴大。
* 分割區數可讓您跨眾多客戶平行處理取用量。 單一分割區最多可支援每秒20MBps 或大約20000的訊息。 視取用資料的工具而定，可能支援也可能不知支援從多個分割區取用。 如果您不確定要設定的資料分割數目，就可以開始使用四個數據分割。
* 您將事件中樞的訊息保留期設定為至少7天。 如果您的取用工具停機時間超過一天，這可確保此工具可以從最長7天的事件中斷的地方繼續。
* 您應使用事件中樞的預設取用者群組。 您不需要建立其他取用者群組或使用個別的取用者群組，除非您打算讓兩個不同的工具從相同的事件中樞取用相同的資料。
* 針對 Azure 活動記錄，您可以挑選事件中樞命名空間，Azure 監視器會在該命名空間內建立名為「 _深入解析-記錄-作業記錄_」的事件中樞。 針對其他記錄類型，您可以選擇現有的事件中樞，或 Azure 監視器建立每個記錄類別的事件中樞。
* 輸出埠5671和5672通常必須在電腦或從事件中樞取用資料的 VNET 上開啟。

## <a name="monitoring-data-available"></a>可用的監視資料
[Azure 監視器的監視資料來源](data-sources.md) 會針對 Azure 應用程式描述不同層的資料，以及適用于每個應用程式的監視資料類型。 下表列出每個層級，以及如何將該資料串流至事件中樞的說明。 請遵循提供的連結，以取得進一步的詳細資料。

| 服務層級 | 資料 | 方法 |
|:---|:---|:---|
| [Azure 租用戶](data-sources.md#azure-tenant) | Azure Active Directory 稽核記錄 | 在您的 AAD 租使用者上設定租使用者診斷設定。 請參閱  [教學課程：將 Azure Active Directory 記錄串流至 Azure 事件中樞](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) ，以取得詳細資料。 |
| [Azure 訂用帳戶](data-sources.md#azure-subscription) | Azure 活動記錄檔 | 建立記錄設定檔，以將活動記錄事件匯出至事件中樞。  如需詳細資料，請參閱 [將 Azure 平臺記錄串流至 Azure 事件中樞](./resource-logs.md#send-to-azure-event-hubs) 。 |
| [Azure 資源](data-sources.md#azure-resources) | 平台計量<br> 資源記錄 |這兩種資料都會使用資源診斷設定來傳送至事件中樞。 如需詳細資料，請參閱 [將 Azure 資源記錄串流至事件中樞](./resource-logs.md#send-to-azure-event-hubs) 。 |
| [作業系統 (來賓) ](data-sources.md#operating-system-guest) | Azure 虛擬機器 | 在 Azure 中的 Windows 和 Linux 虛擬機器上安裝 [Azure 診斷擴充](diagnostics-extension-overview.md) 功能。 如需有關 Windows Vm 的詳細資料，請參閱 [使用事件中樞串流處理最忙碌路徑中的 Azure 診斷資料](diagnostics-extension-stream-event-hubs.md) ，並 [使用 linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) ，以取得 Linux vm 的詳細資料。 |
| [應用程式程式碼](data-sources.md#application-code) | Application Insights | Application Insights 不會提供直接方法將資料串流至事件中樞。 您可以設定將 Application Insights 資料 [連續匯出](../app/export-telemetry.md) 至儲存體帳戶，然後使用邏輯應用程式將資料傳送至事件中樞，如 [手動使用邏輯應用程式進行串流](#manual-streaming-with-logic-app)所述。 |

## <a name="manual-streaming-with-logic-app"></a>使用邏輯應用程式進行手動串流
對於無法直接串流至事件中樞的資料，您可以寫入至 Azure 儲存體，然後使用 [從 blob 儲存體提取資料](../../connectors/connectors-create-api-azureblobstorage.md#add-action) ，並將 [其作為訊息推送至事件中樞](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)的時間觸發邏輯應用程式。 


## <a name="partner-tools-with-azure-monitor-integration"></a>具有 Azure 監視器整合的合作夥伴工具

使用 Azure 監視器將監視資料路由至事件中樞，可讓您輕鬆地與外部 SIEM 和監視工具整合。 具有 Azure 監視器整合的工具範例包括下列各項：

| 工具 | 託管于 Azure | 說明 |
|:---|:---| :---|
|  IBM QRadar | 否 | Microsoft Azure DSM 與 Microsoft Azure 事件中樞通訊協定均可從 [IBM 支援網站](https://www.ibm.com/support)下載。 您可以在 [QRADAR DSM](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)設定中深入瞭解與 Azure 的整合。 |
| Splunk | 否 | [Splunk 的 Azure 監視器 Add-On](https://splunkbase.splunk.com/app/3534/) 是可在從 splunkbase 取得中使用的開放原始碼專案。 您可以在 Splunk 的 [Azure 監視器增益集取得](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)檔。<br><br> 如果您無法在 Splunk 實例中安裝附加元件（例如您使用 proxy 或在 Splunk Cloud 上執行），您可以使用 [Azure Function For Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS)將這些事件轉送至 Splunk HTTP 事件收集器，這是由事件中樞中的新訊息所觸發。 |
| sumologic | 否 | 您可以 [從事件中樞的 Azure Audit 應用程式收集記錄](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)中，取得設定 SumoLogic 以取用來自事件中樞之資料的指示。 |
| ArcSight | 否 | ArcSight Azure 事件中樞智慧型連接器可作為 [ArcSight 智慧型連接器集合](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)的一部分。 |
| Syslog 伺服器 | 否 | 如果您想要將 Azure 監視器資料直接串流到 syslog 伺服器，您可以使用以 [Azure 函式為基礎的解決方案](https://github.com/miguelangelopereira/azuremonitor2syslog/)。
| LogRhythm | 否| [這裡](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)提供設定 LogRhythm 以從事件中樞收集記錄的指示。 
|Logz.io | 是 | 如需詳細資訊，請參閱針對 [在 Azure 上執行的 JAVA 應用程式使用 Logz.io 開始使用監視和記錄](/azure/developer/java/fundamentals/java-get-started-with-logzio)

也可以使用其他合作夥伴。 如需所有 Azure 監視器夥伴及其功能的完整清單，請參閱 [Azure 監視器夥伴](partners.md)整合。

## <a name="next-steps"></a>後續步驟
* [將活動記錄封存至儲存體帳戶](./activity-log.md#legacy-collection-methods)
* [閱讀 Azure 活動記錄的總覽](./platform-logs-overview.md)
* [根據活動記錄事件設定警示](./alerts-log-webhook.md)

