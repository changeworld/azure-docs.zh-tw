---
title: 將 Azure 資訊安全中心警示和建議匯出至 Siem |Microsoft Docs
description: 本文說明如何將安全性警示和建議的連續匯出設定為 Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: eb7f642e36bd72f963481cb392d7e3a6c2555816
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612379"
---
# <a name="export-security-alerts-and-recommendations"></a>匯出安全性警訊和建議

Azure 資訊安全中心會產生詳細的安全性警示和建議。 您可以在入口網站中或透過程式設計工具來加以查看。 您可能也需要匯出此資訊，或將其傳送至您環境中的其他監視工具。 

本文說明一組工具，可讓您以手動方式或持續持續的方式來匯出警示和建議。

您可以使用這些工具：

* 持續匯出至 Log Analytics 工作區
* 持續匯出至 Azure 事件中樞 (，以與協力廠商 Siem) 整合
* 匯出至 CSV (一次) 



## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式推出|
|定價：|免費層|
|必要的角色和許可權：|資源群組 (或**擁有**者) 的**安全性系統管理員角色**<br>也必須具有目標資源的寫入權限|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中國 Gov，其他 Gov|
|||



## <a name="setting-up-a-continuous-export"></a>設定連續匯出

無論您是設定連續匯出至 Log Analytics 工作區或 Azure 事件中樞，都必須執行下列步驟。

1. 從 [Security Center 的提要欄位] 中，選取 [ **定價 & 設定**]。

1. 選取您要設定資料匯出的特定訂用帳戶。
    
1. 從該訂用帳戶的 [設定] 頁面的側邊欄中，選取 [ **連續匯出**]。

    您可以[ ![ 在 Azure 資訊安全中心的 [匯出選項] 中](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox)看到 [匯出選項]。 每個可用的匯出目標都有一個索引標籤。 

1. 選取您要匯出的資料類型，然後從每種類型的篩選中選擇 (例如，[僅匯出高嚴重性警示]) 。

1. 從 [匯出目標] 區域中，選擇您想要儲存資料的位置。 資料可以儲存在不同訂用帳戶的目標中 (例如中央事件中樞實例或中央 Log Analytics 工作區) 。

1. 選取 [儲存]。


## <a name="setting-up-continuous-export-via-the-rest-api"></a>透過 REST API 設定連續匯出

您可以透過 Azure 資訊安全中心 [自動化 API](https://docs.microsoft.com/rest/api/securitycenter/automations)來設定和管理連續匯出功能。 您可以使用此 API 來建立或更新自動化，以匯出到下列任何可能的目的地：

- Azure 事件中樞
- Log Analytics 工作區
- Azure Logic Apps 

API 提供 Azure 入口網站中無法使用的額外功能，例如：

* **更大的磁片** 區-API 可讓您在單一訂用帳戶上建立多個匯出設定。 安全中心的入口網站 UI 中的 [ **連續匯出** ] 頁面，每個訂用帳戶只支援一個匯出設定。

* **其他功能** -API 提供未顯示在 UI 中的其他參數。 例如，您可以將標籤新增至您的自動化資源，也可以根據在資訊安全中心的入口網站 UI 中 [ **連續匯出** ] 頁面所提供的更多警示和建議屬性來定義匯出。

* **更專注的範圍** -API 可為您的匯出設定範圍提供更細微的層級。 使用 API 定義匯出時，您可以在資源群組層級進行。 如果您是在安全中心的入口網站 UI 中使用 [ **連續匯出** ] 頁面，您必須在訂用帳戶層級定義它。

    > [!TIP]
    > 如果您已使用 API 來設定多個匯出設定，或您已使用僅限 API 的參數，這些額外的功能將不會顯示在 [安全性中心] UI 中。 相反地，會有橫幅通知您有其他設定存在。

在 [REST API 檔](https://docs.microsoft.com/rest/api/securitycenter/automations)中深入瞭解自動化 API。



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>透過 Azure 事件中樞設定 SIEM 整合

Azure 事件中樞是以程式設計方式使用任何串流資料的絕佳解決方案。 針對 Azure 資訊安全中心警示和建議，這是與協力廠商 SIEM 整合的最佳方式。

> [!NOTE]
> 在大多數情況下，將監視資料串流至外部工具最有效的方法是使用 Azure 事件中樞。 [本文提供如何](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) 將來自不同來源的監視資料串流至事件中樞的簡短描述，以及詳細指引的連結。

> [!NOTE]
> 如果您先前使用 Azure 活動記錄檔將「安全性中心」警示匯出到 SIEM，以下程式會取代該方法。

若要查看已匯出資料類型的事件架構，請造訪 [事件中樞事件架構](https://aka.ms/ASCAutomationSchemas)。


### <a name="to-integrate-with-a-siem"></a>若要與 SIEM 整合 

將所選的安全性中心資料的連續匯出設定為 Azure 事件中樞之後，您可以為您的 SIEM 設定適當的連接器：

* **Azure Sentinel** -使用此處提供的原生 Azure 資訊安全中心警示 [資料連線器](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) 。
* **Splunk** -使用[適用于 Splunk 的 Azure 監視器附加](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)元件
* **IBM QRadar** -使用 [手動設定的記錄來源](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** –使用 [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

此外，如果您想要從已設定的事件中樞自動將連續匯出的資料移至 Azure 資料總管，請使用將 [資料從事件中樞內嵌至 azure 資料總管](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)中的指示。



## <a name="continuous-export-to-a-log-analytics-workspace"></a>連續匯出至 Log Analytics 工作區

如果您想要分析 Log Analytics 工作區中的 Azure 資訊安全中心資料，或將 Azure 警示與「安全性中心」搭配使用，請將「連續匯出」設定至 Log Analytics 工作區。

若要匯出至 Log Analytics 工作區，您必須在您的工作區上啟用資訊安全中心的 Log Analytics 解決方案。 如果您使用 Azure 入口網站，當您啟用「連續匯出」時，會自動啟用安全中心的「免費層」解決方案。 不過，如果您要以程式設計方式設定連續匯出設定，則必須從 **定價 & 設定**中手動選取所需工作區的免費或標準定價層。  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics 資料表和架構

安全性警示和建議分別儲存在 *SecurityAlert* 和 *SecurityRecommendations* 資料表中。 包含這些資料表的 Log Analytics 解決方案名稱，取決於您是否位於免費或標準層 (查看 [定價](security-center-pricing.md)) ：安全性 ( ' 安全性與稽核 ' ) 或 SecurityCenterFree。

![Log Analytics 中的 * SecurityAlert * 資料表](./media/continuous-export/log-analytics-securityalert-solution.png)

若要查看已匯出資料類型的事件架構，請造訪 [Log Analytics 資料表架構](https://aka.ms/ASCAutomationSchemas)。

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>在 Azure 監視器中查看匯出的安全性警示和建議

在某些情況下，您可以選擇在 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)中查看匯出的安全性警示及/或建議。 

Azure 監視器針對各種不同的 Azure 警示（包括診斷記錄、計量警示，以及以 Log Analytics 工作區查詢為基礎的自訂警示）提供統一的警示體驗。

若要在 Azure 監視器中從資訊安全中心查看警示和建議，請根據 Log Analytics 查詢 (記錄警示) 來設定警示規則：

1. 從 Azure 監視器的 [ **警示** ] 頁面上，選取 [ **新增警示規則**]。

    ![Azure 監視器的警示頁面](./media/continuous-export/azure-monitor-alerts.png)

1. 在 [建立規則] 頁面中，以您在 [Azure 監視器) 中設定記錄警示規則](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) 的相同方式，設定新的規則 (：

    * 針對 [ **資源**]，選取您匯出安全性警示和建議的 Log Analytics 工作區。

    * 針對 [ **條件**]，選取 [ **自訂記錄搜尋**]。 在出現的頁面中，設定查詢、回顧期限和頻率週期。 在搜尋查詢中，您可以輸入 *SecurityAlert* 或 *SecurityRecommendation* ，以查詢當您啟用「連續匯出至 Log Analytics」功能時，「安全性中心」持續匯出的資料類型。 
    
    * （選擇性）設定您想要觸發的 [動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 。 動作群組可以觸發電子郵件傳送、ITSM 票證、Webhook 等等。
    ![Azure 監視器警示規則](./media/continuous-export/azure-monitor-alert-rule.png)

您現在會看到新的 Azure 資訊安全中心警示或建議 (根據您在 Azure 監視器警示中的設定) ，並自動觸發動作群組 (（如果有提供) ）。

## <a name="manual-one-time-export-of-security-alerts"></a>手動一次匯出安全性警示

若要下載警示或建議的 CSV 報告，請開啟 [ **安全性警示** ] 或 [ **建議** ] 頁面，然後選取 [ **下載 csv 報表** ] 按鈕。

[![將警示資料下載為 CSV 檔案](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 這些報表包含目前所選訂用帳戶中的資源警示和建議。



## <a name="faq---continuous-export"></a>常見問題-連續匯出

### <a name="what-are-the-costs-involved-in-exporting-data"></a>匯出資料涉及哪些成本？

啟用連續匯出不會產生任何費用。 視您在 Log Analytics 工作區中的設定而定，可能會產生成本，以在您的 Log Analytics 工作區中內嵌和保留資料。 

深入瞭解 [Log Analytics 工作區定價](https://azure.microsoft.com/pricing/details/monitor/)。

深入瞭解 [Azure 事件中樞定價](https://azure.microsoft.com/pricing/details/event-hubs/)。


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何設定建議和警示的連續匯出。 您也已瞭解如何以 CSV 檔案的形式下載您的警示資料。 

如需相關材質，請參閱下列檔： 

- [Azure 事件中樞文件](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel 檔](https://docs.microsoft.com/azure/sentinel/)
- [Azure 監視器文件](https://docs.microsoft.com/azure/azure-monitor/)
- [工作流程自動化和連續匯出資料類型架構](https://aka.ms/ASCAutomationSchemas)
