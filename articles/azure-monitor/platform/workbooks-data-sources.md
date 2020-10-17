---
title: Azure 監視器活頁簿資料來源 |Microsoft 檔
description: 使用從多個資料來源建立的預建和自訂參數化 Azure 監視器活頁簿，簡化複雜的報表
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: a0349a3fe21bdc0b73252bce1207c9f3b53dc15f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143730"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure 監視器活頁簿資料來源

活頁簿與大量資料來源相容。 本文將逐步引導您進行 Azure 監視器活頁簿目前可用的資料來源。

## <a name="logs"></a>記錄

活頁簿可讓您從下列來源查詢記錄：

* Azure 監視器記錄 (Application Insights 資源和 Log Analytics 工作區。 ) 
* 以資源為中心的資料 (活動記錄) 

活頁簿作者可以使用 KQL 查詢來轉換基礎資源資料，以選取可哥視化為文字、圖表或格線的結果集。

![活頁簿記錄報表介面的螢幕擷取畫面](./media/workbooks-overview/logs.png)

活頁簿作者可以輕鬆地跨多個資源進行查詢，以建立真正統一的豐富報告體驗。

## <a name="metrics"></a>計量

Azure 資源會發出可透過活頁簿存取的 [度量](data-platform-metrics.md) 。 您可以透過專門的控制項（可讓您指定目標資源、所需的計量及其匯總），在活頁簿中存取計量。 然後，您可以將此資料繪製在圖表或方格中。

![Cpu 使用率之活頁簿度量圖表的螢幕擷取畫面](./media/workbooks-overview/metrics-graph.png)

![活頁簿計量介面的螢幕擷取畫面](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

活頁簿支援使用 Azure Resource Graph (ARG) 來查詢資源和其中繼資料。 這項功能主要是用來建立報表的自訂查詢範圍。 資源範圍是透過 ARG 所支援的 KQL 子集來表示，這通常就足以應付常見的使用案例。

若要讓查詢控制項使用這個資料來源，請使用 [查詢類型] 下拉式清單選擇 Azure Resource Graph 並選取要設為目標的訂閱。 您可以使用查詢控制項，加入可選取有趣資源子集的 ARG KQL 子集。

![Azure Resource Graph KQL query 的螢幕擷取畫面](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

活頁簿支援 Azure Resource Manager REST 作業。 這可讓您在不需要提供自己的授權標頭權杖的情況下，查詢 management.azure.com 端點。

若要讓查詢控制項使用這個資料來源，請使用 [資料來源] 下拉式清單選擇 Azure Resource Manager。 提供適當的參數，例如 Http 方法、url 路徑、標頭、url 參數和/或主體。

> [!NOTE]
> `GET`目前只 `POST` 支援、和 `HEAD` 作業。

## <a name="azure-data-explorer"></a>Azure 資料總管

活頁簿現在支援使用強大的[Kusto](/azure/kusto/query/index)查詢語言從[Azure 資料總管](/azure/data-explorer/)叢集進行查詢。   

![Kusto 查詢視窗的螢幕擷取畫面](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>工作負載健全狀況

Azure 監視器具有可主動監視 Windows 或 Linux 客體作業系統可用性和效能的功能。 Azure 監視器模型主要元件及其關聯性、如何測量這些元件健康情況的準則，以及偵測到狀況不良的狀況時，哪些元件會發出警示。 活頁簿可讓使用者使用此資訊來建立豐富的互動式報表。

若要讓查詢控制項使用此資料來源，請使用 [ **查詢類型** ] 下拉式清單來選擇工作負載健全狀況，並選取要作為目標的訂用帳戶、資源群組或 VM 資源。 使用 [健康情況篩選器] 下拉式清單，為您的分析需求選取一個有趣的健康情況事件子集。

![警示查詢的螢幕擷取畫面](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 資源健康狀態

活頁簿支援取得 Azure 資源健康狀態，並將它與其他資料來源結合，以建立豐富的互動式健康情況報告

若要讓查詢控制項使用此資料來源，請使用 [ **查詢類型** ] 下拉式清單來選擇 [Azure health]，然後選取要設為目標的資源。 使用 [健康情況篩選器] 下拉式清單，為您的分析需求選取一個有趣的資源問題子集。

![警示查詢的螢幕擷取畫面](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

JSON 提供者可讓您從靜態 JSON 內容建立查詢結果。 它最常用於參數來建立靜態值的下拉式參數。 簡單的 JSON 陣列或物件會自動轉換成方格資料列和資料行。  如需更具體的行為，您可以使用 [結果] 索引標籤和 JSONPath 設定來設定資料行。

## <a name="alerts-preview"></a>警示 (預覽)

> [!NOTE]
> 查詢 Azure 警示資訊的建議方式是藉由查詢資料表來使用 [Azure Resource Graph](#azure-resource-graph) 資料來源 `AlertsManagementResources` 。
>
> 如需範例，請參閱 [Azure Resource Graph 資料表參考](../../governance/resource-graph/reference/supported-tables-resources.md)或 [警示範本](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) 。
>
> 警示資料來源會在一段時間內保持可用，而作者會轉換成使用 ARG。 建議您不要在範本中使用這個資料來源。 

活頁簿可讓使用者以視覺化方式呈現與其資源相關的作用中警示。 限制：警示資料來源需要訂用帳戶的讀取權限，才能查詢資源，而且可能不會顯示較新的警示類型。 

若要讓查詢控制項使用此資料來源，請使用 [ _資料來源_ ] 下拉式清單來選擇 _警示 (預覽) _ 並選取要設為目標的訂用帳戶、資源群組或資源。 使用 [警示篩選器] 下拉式清單，為您的分析需求選取一個有趣的警示子集。

## <a name="custom-endpoint"></a>自訂端點

活頁簿支援從任何外部來源取得資料。 如果您的資料位於 Azure 外部，您可以使用此資料來源類型將它帶入活頁簿。

若要讓查詢控制項使用這個資料來源，請使用 [ _資料來源_ ] 下拉式清單選擇 [ _自訂端點_]。 提供適當的參數，例如 `Http method` 、 `url` 、 `headers` `url parameters` 和/或 `body` 。 請確定您的資料來源支援 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) ，否則要求將會失敗。

若要避免在使用範本時自動呼叫不受信任的主機，使用者必須將使用的主機標示為受信任。 這可以藉由按一下 [ _新增為受信任_ ] 按鈕來完成，或在活頁簿設定中將它新增為信任的主機。 這些設定會儲存在支援 web 背景工作 IndexDb 的瀏覽器中，請參閱 [這裡](https://caniuse.com/#feat=indexeddb)的詳細資訊。

> [!NOTE]
> 請勿將任何欄位中的任何秘密寫入 (`headers` 、 `parameters` 、 `body` `url`) ，因為所有活頁簿使用者都可以看到這些秘密。

## <a name="next-steps"></a>後續步驟

* [開始深入](./workbooks-overview.md#visualizations) 瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。
* [Log Analytics 查詢優化秘訣](../log-query/query-optimization.md)