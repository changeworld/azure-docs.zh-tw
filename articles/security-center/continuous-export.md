---
title: 將 Azure 安全中心警報和建議匯出到 SIEM |微軟文檔
description: 本文介紹如何設置安全警報和向 SIEM 提供的安全警報和建議的連續匯出
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158959"
---
# <a name="export-security-alerts-and-recommendations"></a>匯出安全性警訊和建議

Azure 安全中心生成詳細的安全警報和建議。 您可以在門戶中或通過程式設計工具查看它們。 您可能還需要匯出此資訊或將其發送到環境中的其他監視工具。 

本文介紹了允許您手動或持續匯出警報和建議的工具集。

使用這些工具，您可以：

* 持續匯出到日誌分析工作區
* 持續匯出到 Azure 事件中心（用於與協力廠商 SIEM 的集成）
* 匯出到 CSV（一次）


## <a name="setting-up-a-continuous-export"></a>設置連續匯出

無論是設置日誌分析工作區還是 Azure 事件中心，都有必要執行以下步驟。

1. 從安全中心的側邊欄中，選擇**定價&設置**。

1. 選擇要為其配置資料匯出的特定訂閱。
    
1. 從該訂閱的設置頁的邊欄中，選擇 **"連續匯出**"。

    [Azure 安全中心的匯出選項![ ](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox)在這裡，您可以看到匯出選項。 每個可用的匯出目標都有一個選項卡。 

1. 選擇要匯出的資料類型，並從每種類型的篩選器中進行選擇（例如，僅匯出高嚴重性警報）。

1. 從"匯出目標"區域中，選擇希望將資料保存的位置。 資料可以保存在目標上不同的訂閱（例如，在中央事件中心實例或中央日誌分析工作區）。

1. 按一下 [儲存]****。



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>通過 Azure 事件中心配置 SIEM 集成

Azure 事件中心是程式化使用任何流資料的絕佳解決方案。 對於 Azure 安全中心警報和建議，它是與協力廠商 SIEM 集成的首選方式。

> [!NOTE]
> 在大多數情況下，將監視資料流程式傳輸到外部工具的最有效方法是使用 Azure 事件中心。 [本文](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)簡要介紹了如何將監視資料從不同源資料流到事件中心，以及指向詳細指南的連結。

> [!NOTE]
> 如果以前使用 Azure 活動日誌將安全中心警報匯出到 SIEM，則下面的過程將替換該方法。

要查看匯出資料類型的事件架構，請訪問[事件中心事件架構](https://aka.ms/ASCAutomationSchemas)。


### <a name="to-integrate-with-a-siem"></a>與 SIEM 集成 

將所選安全中心資料連續匯出到 Azure 事件中心後，可以為 SIEM 設置適當的連接器：

* **Azure 哨兵**- 使用本地 Azure 安全中心警報資料[連接器](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)。
* **Splunk** - 使用[Azure 監視器載入項進行斯普倫克](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - 使用[手動設定的日誌源](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **弧線**視覺 – 使用[智慧連接器](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

此外，如果要將連續匯出的資料自動從配置的事件中心移動到 Azure 資料資源管理器，請使用[將資料從事件中心引入到 Azure 資料資源管理器](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)中的說明。



## <a name="continuous-export-to-a-log-analytics-workspace"></a>持續匯出到日誌分析工作區

如果要分析日誌分析工作區內的 Azure 安全中心資料或與安全中心一起使用 Azure 警報，請設置連續匯出到日誌分析工作區。

要匯出到日誌分析工作區，必須在工作區上啟用安全中心的日誌分析解決方案。 如果使用 Azure 門戶，則啟用連續匯出時，安全中心的免費層解決方案將自動啟用。 但是，如果要以程式設計方式配置連續匯出設置，則必須在**定價&設置**中手動選擇所需工作區的免費或標準定價層。  

### <a name="log-analytics-tables-and-schemas"></a>日誌分析表和架構

安全警報和建議分別存儲在*安全警報**和安全建議*表中。 包含這些表的日誌分析解決方案的名稱取決於您是位於免費層還是標準層（請參閱[定價](security-center-pricing.md)）：安全性（"安全和審核"）還是安全中心免費。

![日誌分析中的 [安全警報] 表](./media/continuous-export/log-analytics-securityalert-solution.png)

要查看匯出資料類型的事件架構，請訪問[日誌分析表架構](https://aka.ms/ASCAutomationSchemas)。

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>在 Azure 監視器中查看匯出的安全警報和建議

在某些情況下，您可以選擇在[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)中查看匯出的安全警報和/或建議。 

Azure 監視器為各種 Azure 警報提供了統一的警報體驗，包括診斷日誌、指標警報和基於日誌分析工作區查詢的自訂警報。

要查看 Azure 監視器中安全中心的警報和建議，請根據日誌分析查詢（日誌警報）配置警報規則：

1. 在 Azure 監視器的**警報頁中**，按一下 **"新警報規則**"。

    ![Azure 監視器的警報頁](./media/continuous-export/azure-monitor-alerts.png)

1. 在創建規則頁中，配置新規則（與[在 Azure 監視器中配置日誌警報規則](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)的方式相同）：

    * 對於**資源**，選擇匯出安全警報和建議的日誌分析工作區。

    * 對於**條件**，選擇**自訂日誌搜索**。 在顯示的頁面中，配置查詢、回望週期和頻率週期。 在搜索查詢中，可以鍵入*安全警報*或*安全建議*，以查詢安全中心在啟用"連續匯出到日誌分析"功能時連續匯出到的資料類型。 
    
    * 或者，配置要觸發[的操作組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。 操作組可以觸發電子郵件發送、ITSM 票證、WebHook 等。
    ![Azure 監視器警報規則](./media/continuous-export/azure-monitor-alert-rule.png)

現在，您將在 Azure 監視器警報中看到新的 Azure 安全中心警報或建議（取決於您的配置），並自動觸發操作組（如果提供）。

## <a name="manual-one-time-export-of-security-alerts"></a>手動一次性匯出安全警報

要下載 CSV 報告以查找警報或建議，請打開**安全警報**或**建議**頁面，然後按一下 **"下載 CSV 報告**"按鈕。

[![將警報資料下載為 CSV 檔](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 這些報告包含當前所選訂閱中的資源的警報和建議。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何配置建議和警報的連續匯出。 您還學習了如何將警報資料下載為 CSV 檔。 

有關相關材料，請參閱以下文檔： 

- [Azure 事件中心文檔](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel 文件](https://docs.microsoft.com/azure/sentinel/)
- [Azure 監視器文件](https://docs.microsoft.com/azure/azure-monitor/)
- [工作流自動化和連續匯出資料類型架構](https://aka.ms/ASCAutomationSchemas)
