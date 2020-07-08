---
title: Azure 監視器活頁簿資料來源 |Microsoft 檔
description: 使用從多個資料來源建立的預建和自訂參數化 Azure 監視器活頁簿，簡化複雜的報表
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mbullwin
ms.openlocfilehash: 897e615234e17cfe36790778d00cd56371afd91f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85560146"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure 監視器活頁簿資料來源

活頁簿與大量資料來源相容。 本文將逐步引導您瞭解目前可用於 Azure 監視器活頁簿的資料來源。

## <a name="logs"></a>記錄

活頁簿允許從下列來源查詢記錄：

* Azure 監視器記錄（Application Insights 資源和 Log Analytics 工作區）。
* 以資源為中心的資料（活動記錄）

活頁簿作者可以使用轉換基礎資源資料的 KQL 查詢，以選取可哥視化為文字、圖表或格線的結果集。

![活頁簿記錄報表介面的螢幕擷取畫面](./media/workbooks-overview/logs.png)

活頁簿作者可以輕鬆地跨多個資源查詢，建立真正統一的豐富報告體驗。

## <a name="metrics"></a>計量

Azure 資源會發出可透過活頁簿存取的[計量](data-platform-metrics.md)。 您可以透過特殊控制項存取活頁簿中的計量，讓您指定目標資源、所需的計量和匯總。 然後，您可以在圖表或方格中繪製這項資料。

![Cpu 使用率之活頁簿計量圖表的螢幕擷取畫面](./media/workbooks-overview/metrics-graph.png)

![活頁簿計量介面的螢幕擷取畫面](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

活頁簿支援使用 Azure Resource Graph （ARG）來查詢資源及其中繼資料。 此功能主要是用來建立報表的自訂查詢範圍。 資源範圍是透過 ARG 支援的 KQL 子集來表示–這通常足以應付一般使用案例。

若要讓查詢控制項使用此資料來源，請使用 [查詢類型] 下拉式選來選擇 Azure Resource Graph 並選取要設為目標的訂閱。 使用查詢控制項，加入可選取有趣資源子集的 ARG KQL 子集。

![Azure Resource Graph KQL 查詢的螢幕擷取畫面](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

活頁簿支援 Azure Resource Manager REST 作業。 這可讓您在不需要提供自己的授權標頭權杖的情況下，查詢 management.azure.com 端點。

若要讓查詢控制項使用此資料來源，請使用 [資料來源] 下拉式選來選擇 Azure Resource Manager。 提供適當的參數，例如 Http 方法、url 路徑、標頭、url 參數和/或主體。

> [!NOTE]
> `GET`目前僅 `POST` 支援、和 `HEAD` 作業。

## <a name="azure-data-explorer"></a>Azure 資料總管

活頁簿現在支援使用強大的[Kusto](https://docs.microsoft.com/azure/kusto/query/index)查詢語言，從[Azure 資料總管](https://docs.microsoft.com/azure/data-explorer/)叢集進行查詢。   

![Kusto 查詢視窗的螢幕擷取畫面](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>工作負載健全狀況

Azure 監視器具有可主動監視 Windows 或 Linux 客體作業系統之可用性和效能的功能。 Azure 監視器會建立主要元件及其關聯性的模型、如何測量這些元件之健康情況的準則，以及在偵測到狀況不良的狀況時，哪些元件會對您發出警示。 活頁簿可讓使用者使用此資訊來建立豐富的互動式報表。

若要讓查詢控制項使用此資料來源，請使用 [**查詢類型**] 下拉式選選擇 [工作負載健全狀況]，然後選取訂用帳戶、資源群組或要設為目標的 VM 資源。 使用 [健全狀況篩選器] 下拉式清單，為您的分析需求選取一個有趣的健全狀況事件子集。

![警示查詢的螢幕擷取畫面](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 資源健康狀態

活頁簿支援取得 Azure 資源健康狀態，並將其與其他資料來源結合，以建立豐富的互動式健全狀況報告

若要讓查詢控制項使用此資料來源，請使用 [**查詢類型**] 下拉式選來選擇 [Azure 健康情況]，然後選取要設為目標的資源。 使用 [健全狀況篩選器] 下拉式清單，為您的分析需求選取有趣的資源問題子集。

![警示查詢的螢幕擷取畫面](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

JSON 提供者可讓您從靜態 JSON 內容建立查詢結果。 它最常用於參數中，以建立靜態值的下拉式參數。 簡單的 JSON 陣列或物件會自動轉換成方格的資料列和資料行。  如需更具體的行為，您可以使用 [結果] 索引標籤和 [JSONPath 設定] 來設定資料行。

## <a name="alerts-preview"></a>警示（預覽）

> [!NOTE]
> 若要查詢 Azure 警示資訊，建議的方式是藉由查詢資料表來使用[Azure Resource Graph](#azure-resource-graph)資料來源 `AlertsManagementResources` 。
>
> 如需範例，請參閱[Azure Resource Graph 資料表參考](https://docs.microsoft.com/azure/governance/resource-graph/reference/supported-tables-resources)或[警示範本](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook)。
>
> 當作者轉換成使用 ARG 時，警示資料來源會持續提供一段時間。 不鼓勵在範本中使用這個資料來源。 

活頁簿可讓使用者以視覺化方式呈現與其資源相關的作用中警示。 限制：警示資料來源需要訂用帳戶的讀取權限，才能查詢資源，而且可能不會顯示較新的警示類型。 

若要讓查詢控制項使用此資料來源，請使用 [_資料來源_] 下拉式選選擇 [_警示（預覽）_ ]，然後選取要設為目標的訂用帳戶、資源群組或資源。 使用 [警示篩選器] 下拉式清單，為您的分析需求選取一個有趣的警示子集。

## <a name="custom-endpoint"></a>自訂端點

活頁簿支援從任何外部來源取得資料。 如果您的資料位於 Azure 外部，您可以使用此資料來源類型將它帶入活頁簿。

若要讓查詢控制項使用此資料來源，請使用 [_資料來源_] 下拉式選來選擇 [_自訂端點_]。 提供適當的參數，例如 `Http method` 、 `url` 、 `headers` `url parameters` 和/或 `body` 。 請確定您的資料來源支援[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) ，否則要求將會失敗。

若要避免在使用範本時自動呼叫未受信任的主機，使用者必須將使用的主機標示為受信任。 若要這麼做，請按一下 [_新增為受信任_] 按鈕，或在 [活頁簿設定] 中將它新增為信任的主控制項。 這些設定會儲存在支援 web 背景工作 IndexDb 的瀏覽器中，詳細資訊請參閱[這裡](https://caniuse.com/#feat=indexeddb)。

> [!NOTE]
> 請勿在任何欄位（ `headers` 、、、）中寫入任何 `parameters` 秘密 `body` `url` ，因為所有的活頁簿使用者都可以看到它們。

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
* [Log Analytics 查詢優化秘訣](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization)
* 
