---
title: Azure 監視器活頁簿資料來源 |微軟文檔
description: 使用從多個資料來源構建的預構建和自訂參數化 Azure 監視器活頁簿簡化複雜報告
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248576"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure 監視活頁簿資料來源

活頁簿與大量資料來源相容。 本文將引導您流覽當前可用於 Azure 監視器活頁簿的資料來源。

## <a name="logs"></a>記錄

活頁簿允許查詢來自以下來源的日誌：

* Azure 監視器日誌（應用程式見解資源和日誌分析工作區）。
* 以資源為中心的資料（活動日誌）

活頁簿作者可以使用轉換基礎資源資料的 KQL 查詢來選擇可以視覺化為文本、圖表或網格的結果集。

![活頁簿日誌報告介面的螢幕截圖](./media/workbooks-overview/logs.png)

活頁簿作者可以輕鬆地跨多個資源查詢，從而創建真正統一的豐富報告體驗。

## <a name="metrics"></a>計量

Azure 資源發出可通過活頁簿訪問的[指標](data-platform-metrics.md)。 指標可以通過專用控制項在活頁簿中訪問，該控制項允許您指定目標資源、所需指標及其聚合。 然後，可以在圖表或網格中繪製此資料。

![活頁簿 cpu 利用率指標圖表的螢幕截圖](./media/workbooks-overview/metrics-graph.png)

![活頁簿指標介面的螢幕截圖](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

活頁簿支援使用 Azure 資源圖 （ARG） 查詢資源及其中繼資料。 此功能主要用於為報表生成自訂查詢作用域。 資源範圍通過 ARG 支援的 KQL 子集表示 - 通常對於常見用例來說，該子集就足夠了。

要使查詢控制項使用此資料來源，請使用"查詢類型下拉清單"選擇 Azure 資源圖並選擇要目標的訂閱。 使用查詢控制項添加選擇有趣資源子集的 ARG KQL 子集。


![Azure 資源圖 KQL 查詢的螢幕截圖](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>警報（預覽）

活頁簿允許使用者視覺化與其資源相關的活動警報。 此功能允許創建將通知資料（警報）和診斷資訊（指標、日誌）合併到一個報表中的報表。 還可以將此資訊合併在一起，以創建豐富的報告，這些報表將見解合併到這些資料來源中。

要使查詢控制項使用此資料來源，請使用"查詢類型下拉清單"選擇"警報"並選擇要定位的訂閱、資源組或資源。 使用警報篩選器下拉清單可根據您的分析需求選擇一個有趣的警報子集。

![警報查詢的螢幕截圖](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>工作負載運行狀況（預覽）

Azure 監視器具有主動監視 Windows 或 Linux 客體作業系統的可用性和性能的功能。 Azure 監視器可建模關鍵元件及其關係、如何測量這些元件運行狀況的條件，以及檢測到不正常情況時提醒您哪些元件。 活頁簿允許使用者使用此資訊創建豐富的互動式報表。

要使查詢控制項使用此資料來源，請使用 **"查詢類型**下拉清單"選擇工作負載運行狀況，並選擇要定位的訂閱、資源組或 VM 資源。 使用運行狀況篩選器下拉清單可根據您的分析需求選擇一個有趣的運行狀況事件子集。

![警報查詢的螢幕截圖](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure 資源運行狀況 

活頁簿支援獲取 Azure 資源運行狀況並將其與其他資料來源結合，以創建豐富的互動式運行狀況報告

要使查詢控制項使用此資料來源，請使用 **"查詢類型**下拉清單"選擇 Azure 運行狀況並選擇要定位的資源。 使用運行狀況篩選器下拉清單為分析需求選擇有趣的資源問題子集。

![警報查詢的螢幕截圖](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure 資料資源管理器（預覽）

活頁簿現在支援使用強大的[Kusto](https://docs.microsoft.com/azure/kusto/query/index)查詢語言從[Azure 資料資源管理器](https://docs.microsoft.com/azure/data-explorer/)群集進行查詢。   

![庫斯托查詢視窗的螢幕截圖](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
