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
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658366"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure 監視器活頁簿資料來源

活頁簿與大量資料來源相容。 本文將逐步引導您瞭解目前可用於 Azure 監視器活頁簿的資料來源。

## <a name="logs"></a>記錄檔

活頁簿允許從下列來源查詢記錄：

* Azure 監視器記錄（Application Insights 資源和 Log Analytics 工作區）。
* 以資源為中心的資料（活動記錄）

活頁簿作者可以使用轉換基礎資源資料的 KQL 查詢，以選取可哥視化為文字、圖表或格線的結果集。

![活頁簿記錄報表介面的螢幕擷取畫面](./media/workbooks-overview/logs.png)

活頁簿作者可以輕鬆地跨多個資源查詢，建立真正統一的豐富報告體驗。

## <a name="metrics"></a>度量

Azure 資源會發出可透過活頁簿存取的[計量](data-platform-metrics.md)。 您可以透過特殊控制項存取活頁簿中的計量，讓您指定目標資源、所需的計量和匯總。 然後，您可以在圖表或方格中繪製這項資料。

![Cpu 使用率之活頁簿計量圖表的螢幕擷取畫面](./media/workbooks-overview/metrics-graph.png)

![活頁簿計量介面的螢幕擷取畫面](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

活頁簿支援使用 Azure Resource Graph （ARG）來查詢資源及其中繼資料。 此功能主要是用來建立報表的自訂查詢範圍。 資源範圍是透過 ARG 支援的 KQL 子集來表示–這通常足以應付一般使用案例。

若要讓查詢控制項使用此資料來源，請使用 [查詢類型] 下拉式選來選擇 Azure Resource Graph 並選取要設為目標的訂閱。 使用查詢控制項，加入可選取有趣資源子集的 ARG KQL 子集。


![Azure Resource Graph KQL 查詢的螢幕擷取畫面](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>警示（預覽）

活頁簿可讓使用者以視覺化方式呈現與其資源相關的作用中警示。 這項功能可讓您建立將通知資料（警示）和診斷資訊（計量、記錄）結合在一份報表中的報表。 這項資訊也可以聯結在一起，以建立豐富的報表，將深入解析結合到這些資料來源。

若要讓查詢控制項使用此資料來源，請使用 [查詢類型] 下拉式選來選擇 [警示]，然後選取要做為目標的訂用帳戶、資源群組或資源。 使用 [警示篩選器] 下拉式清單，為您的分析需求選取一個有趣的警示子集。

![警示查詢的螢幕擷取畫面](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>工作負載健全狀況（預覽）

Azure 監視器具有可主動監視 Windows 或 Linux 客體作業系統之可用性和效能的功能。 Azure 監視器會建立主要元件及其關聯性的模型、如何測量這些元件之健康情況的準則，以及在偵測到狀況不良的狀況時，哪些元件會對您發出警示。 活頁簿可讓使用者使用此資訊來建立豐富的互動式報表。

若要讓查詢控制項使用此資料來源，請使用 [**查詢類型**] 下拉式選選擇 [工作負載健全狀況]，然後選取訂用帳戶、資源群組或要設為目標的 VM 資源。 使用 [健全狀況篩選器] 下拉式清單，為您的分析需求選取一個有趣的健全狀況事件子集。

![警示查詢的螢幕擷取畫面](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 資源健康狀態 

活頁簿支援取得 Azure 資源健康狀態，並將其與其他資料來源結合，以建立豐富的互動式健全狀況報告

若要讓查詢控制項使用此資料來源，請使用 [**查詢類型**] 下拉式選來選擇 [Azure 健康情況]，然後選取要設為目標的資源。 使用 [健全狀況篩選器] 下拉式清單，為您的分析需求選取有趣的資源問題子集。

![警示查詢的螢幕擷取畫面](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure 資料總管（預覽）

活頁簿現在支援使用強大的[Kusto](https://docs.microsoft.com/azure/kusto/query/index)查詢語言，從[Azure 資料總管](https://docs.microsoft.com/azure/data-explorer/)叢集進行查詢。   

![Kusto 查詢視窗的螢幕擷取畫面](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
