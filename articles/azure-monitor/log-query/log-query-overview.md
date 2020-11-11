---
title: Azure 監視器中的記錄查詢
description: Azure 監視器所使用的 Kusto 查詢語言參考資訊。 包含 Azure 監視器專用的其他元素和 Azure 監視器記錄查詢中不支援的元素。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 6174bcbe5a014cff8dbd8dff242880d7f0ef7aa0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491387"
---
# <a name="log-queries-in-azure-monitor"></a>Azure 監視器中的記錄查詢
Azure 監視器記錄是以 Azure 資料總管為基礎，而記錄查詢則是使用相同的 Kusto 查詢語言（ (KQL) ）來撰寫。 這是設計成易於閱讀和撰寫的豐富語言，因此您應該能夠使用一些基本的指引來開始撰寫查詢。

您將在 Azure 監視器中使用查詢的區域包括下列各項：

- [Log Analytics](../log-query/log-analytics-overview.md)。 Azure 入口網站中的主要工具，可用於編輯記錄查詢和以互動方式分析其結果。 即使您想要在 Azure 監視器的其他位置使用記錄查詢，您通常會在 Log Analytics 中撰寫和測試它，再將它複製到最終位置。
- [記錄警示規則](../platform/alerts-overview.md)。 主動識別您工作區中資料的問題。  每個警示規則都是以定期自動執行的記錄檔查詢為基礎。  會檢查結果來判斷是否應該建立警示。
- 活頁[簿](../platform/workbooks-overview.md)。 在 Azure 入口網站的互動式視覺效果報表中，使用不同的視覺效果來包含記錄查詢的結果。
- [Azure 儀表板](../learn/tutorial-logs-dashboards.md)。 將任何查詢的結果釘選到 Azure 儀表板，可讓您將記錄和度量資料視覺化，並選擇性地與其他 Azure 使用者共用。
- [Logic Apps](../platform/logicapp-flow-connector.md)。  使用 Logic Apps，在自動化工作流程中使用記錄查詢的結果。
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult)。 從命令列或使用 AzOperationalInsightsSearchResults 的 Azure 自動化 runbook，在 PowerShell 腳本中使用記錄查詢的結果。
- [Azure 監視器記錄 API](https://dev.loganalytics.io)。 從任何 REST API 用戶端取出工作區中的記錄資料。  API 要求包含針對 Azure 監視器執行的查詢，來判斷要擷取的資料。

## <a name="getting-started"></a>開始使用
開始學習使用 KQL 撰寫記錄查詢的最佳方式，就是運用可用的教學課程和範例。

- [Log analytics 教學](log-analytics-tutorial.md) 課程-使用 log analytics 功能的教學課程，這是您將在 Azure 入口網站中用來編輯和執行查詢的工具。 它也可讓您撰寫簡單的查詢，而不需要直接使用查詢語言。 如果您之前未曾使用過 Log Analytics，請從這裡開始，以便了解您將搭配其他教學課程和範例使用的工具。
- [KQL 教學](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) 課程-引導您逐步瞭解基本的 KQL 概念和一般運算子。 這是開始使用語言本身以及記錄查詢結構的最佳位置。 
- [範例查詢](example-queries.md) -Log Analytics 中可用之範例查詢的描述。 您可以在不修改的情況下使用這些查詢，也可以使用它們作為範例來瞭解 KQL。
- [查詢範例](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) -說明各種不同概念的範例查詢。



## <a name="reference-documentation"></a>參考文件
您可以在 Azure 資料總管檔中[取得 KQL 的檔](/azure/data-explorer/kusto/query/)，包括所有命令和運算子的參考。 即使您已熟悉 KQL，仍會定期使用參考來調查先前未使用的新命令和案例。


## <a name="language-differences"></a>語言差異
雖然 Azure 監視器使用與 Azure 資料總管相同的 KQL，但有一些差異。 KQL 檔將指定 Azure 監視器或具有不同功能的運算子不受支援。 Azure 監視器的特定運算子記載于 Azure 監視器內容中。 下列各節會提供語言版本之間的差異，以供快速參考。

### <a name="statements-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的陳述式

* [別名](/azure/kusto/query/aliasstatement)
* [查詢參數](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的函式

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current()、current_cursor()](/azure/kusto/query/cursorcurrent)
* [資料庫 ( # B1 ](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的運算子

* [跨叢集聯結](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的外掛程式

* [Python 外掛程式](/azure/kusto/query/pythonplugin)
* [sql_request 外掛程式](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Azure 監視器中的其他運算子
下列運算子支援特定 Azure 監視器功能，且無法在 Azure 監視器以外的地方使用。

* [應用程式 ( # B1 ](app-expression.md)
* [資源 ( # B1 ](resource-expression.md)
* [工作區 ( # B1 ](workspace-expression.md)

## <a name="next-steps"></a>後續步驟
- 逐步解說 [撰寫查詢的教學](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)課程。
- 存取完整的 [Kusto 查詢語言的參考文件](/azure/kusto/query/)。

