---
title: 將 Azure 資訊安全中心警示和建議匯出至 Siem |Microsoft Docs
description: 本文說明如何設定連續匯出安全性警示和建議至 Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 7b0fbb7c4f680f9d732a63fff7b0b317c6cf1511
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519692"
---
# <a name="export-security-alerts-and-recommendations"></a>匯出安全性警訊和建議

Azure 資訊安全中心會產生詳細的安全性警示和建議。 您可以在入口網站中或透過程式設計工具來查看它們。 您可能也需要匯出這項資訊，或將它傳送至環境中的其他監視工具。 

本文說明一組工具，可讓您以手動或持續的方式來匯出警示和建議。

使用這些工具，您可以：

* 持續匯出至 Log Analytics 工作區
* 持續匯出至 Azure 事件中樞（以與協力廠商 Siem 整合）
* 匯出至 CSV （一次）



## <a name="availability"></a>可用性

- 發行階段：**正式推出**
- 必要的角色和許可權：
    - 包含匯出設定之訂用帳戶上的**讀取器**
    - 資源群組（或**擁有**者）上的**安全性系統管理員角色**
    - 也必須具有目標資源的寫入權限
- 雲端：✔的商用雲端✔ US Gov ✘中國 Gov，其他 Gov


## <a name="setting-up-a-continuous-export"></a>設定連續匯出

無論您是設定連續匯出至 Log Analytics 工作區或 Azure 事件中樞，都需要下列步驟。

1. 從資訊安全中心的提要欄位中，選取 [**定價] & 設定**]。

1. 選取您要設定資料匯出的特定訂用帳戶。
    
1. 從該訂用帳戶的 [設定] 頁面的提要欄位中，選取 [**連續匯出**]。

    [ ![ 匯出選項在 Azure 資訊安全中心](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox)這裡您會看到匯出選項。 每個可用的匯出目標都有一個索引標籤。 

1. 選取您想要匯出的資料類型，並從每個類型的篩選中選擇（例如，[僅匯出高嚴重性警示]）。

1. 從 [匯出目標] 區域中，選擇您想要儲存資料的位置。 資料可以儲存在不同訂用帳戶上的目標中（例如，在中央事件中樞實例或中央 Log Analytics 工作區）。

1. 按一下 [檔案] 。



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>透過 Azure 事件中樞設定 SIEM 整合

Azure 事件中樞是用來以程式設計方式取用任何串流資料的絕佳解決方案。 針對 Azure 資訊安全中心警示和建議，這是整合協力廠商 SIEM 的最佳方式。

> [!NOTE]
> 在大部分情況下，將監視資料串流至外部工具的最有效方法是使用 Azure 事件中樞。 [本文](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)提供如何將來自不同來源的監視資料串流至事件中樞的簡短描述，以及詳細指引的連結。

> [!NOTE]
> 如果您先前已使用 Azure 活動記錄將資訊安全中心警示匯出至 SIEM，下列程式會取代該方法。

若要查看匯出資料類型的事件架構，請造訪[事件中樞事件架構](https://aka.ms/ASCAutomationSchemas)。


### <a name="to-integrate-with-a-siem"></a>與 SIEM 整合 

在您將所選資訊安全中心資料的連續匯出設定為 [Azure 事件中樞] 之後，您可以為 SIEM 設定適當的連接器：

* **Azure Sentinel** -使用此處提供的原生 Azure 資訊安全中心警示[資料連線器](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)。
* **Splunk** -使用[適用于 Splunk 的 Azure 監視器附加](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)元件
* **IBM QRadar** -使用[手動設定的記錄來源](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** –使用[SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

此外，如果您想要從已設定的事件中樞自動將連續匯出的資料移至 Azure 資料總管，請使用將[資料從事件中樞內嵌至 azure 資料總管](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)中的指示。



## <a name="continuous-export-to-a-log-analytics-workspace"></a>連續匯出至 Log Analytics 工作區

如果您想要分析 Log Analytics 工作區中的 Azure 資訊安全中心資料，或搭配資訊安全中心使用 Azure 警示，請設定連續匯出至您的 Log Analytics 工作區。

若要匯出至 Log Analytics 工作區，您必須在工作區上啟用資訊安全中心的 Log Analytics 解決方案。 如果您使用 Azure 入口網站，則當您啟用連續匯出時，資訊安全中心的免費層解決方案會自動啟用。 不過，如果您要以程式設計方式設定連續匯出設定，您必須從**定價 & 設定**中，手動選取所需工作區的免費或標準定價層。  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics 資料表和架構

安全性警示和建議會分別儲存在*SecurityAlert*和*SecurityRecommendations*資料表中。 包含這些資料表的 Log Analytics 解決方案名稱，取決於您是在免費或標準層（請參閱[定價](security-center-pricing.md)）：安全性（' 安全性與稽核 '）或 SecurityCenterFree。

![Log Analytics 中的 * SecurityAlert * 資料表](./media/continuous-export/log-analytics-securityalert-solution.png)

若要查看匯出資料類型的事件架構，請造訪[Log Analytics 資料表架構](https://aka.ms/ASCAutomationSchemas)。

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>在 Azure 監視器中查看匯出的安全性警示和建議

在某些情況下，您可以選擇在[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)中，查看匯出的安全性警示和（或）建議。 

Azure 監視器針對各種 Azure 警示（包括診斷記錄、計量警示，以及根據 Log Analytics 工作區查詢的自訂警示）提供統一的警示體驗。

若要從 Azure 監視器中的資訊安全中心查看警示和建議，請根據 Log Analytics 查詢來設定警示規則（記錄警示）：

1. 在 Azure 監視器的 [**警示**] 頁面上，按一下 [**新增警示規則**]。

    ![Azure 監視器的 [警示] 頁面](./media/continuous-export/azure-monitor-alerts.png)

1. 在 [建立規則] 頁面中，設定您的新規則（使用您在[Azure 監視器中設定記錄警示規則](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)的相同方式）：

    * 針對 [**資源**]，選取您匯出安全性警示和建議的 Log Analytics 工作區。

    * 針對 [**條件**]，選取 [**自訂記錄搜尋**]。 在出現的頁面中，設定 [查詢]、[回顧期間] 和 [頻率週期]。 在搜尋查詢中，您可以輸入*SecurityAlert*或*SecurityRecommendation* ，以查詢當您啟用連續匯出至 Log Analytics 功能時，資訊安全中心連續匯出的資料類型。 
    
    * （選擇性）設定您想要觸發的[動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。 動作群組可以觸發電子郵件傳送、ITSM 票證、Webhook 等等。
    ![Azure 監視器警示規則](./media/continuous-export/azure-monitor-alert-rule.png)

您現在會在 Azure 監視器警示中看到新的 Azure 資訊安全中心警示或建議（視您的設定而定），並自動觸發動作群組（如果有提供的話）。

## <a name="manual-one-time-export-of-security-alerts"></a>手動一次匯出安全性警示

若要下載警示或建議的 CSV 報告，請開啟 [**安全性警示**或**建議**] 頁面，然後按一下 [**下載 CSV 報表**] 按鈕。

[![將警示資料下載為 CSV 檔案](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 這些報告包含來自目前所選訂用帳戶之資源的警示和建議。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何設定您的建議和警示的連續匯出。 您也已瞭解如何將警示資料下載為 CSV 檔案。 

如需相關內容，請參閱下列檔： 

- [Azure 事件中樞文件](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel 文件](https://docs.microsoft.com/azure/sentinel/)
- [Azure 監視器文件](https://docs.microsoft.com/azure/azure-monitor/)
- [工作流程自動化和連續匯出資料類型架構](https://aka.ms/ASCAutomationSchemas)
