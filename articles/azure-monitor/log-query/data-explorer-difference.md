---
title: Azure 監視器記錄查詢語言差異 | Microsoft Docs
description: Azure 監視器所使用的 Kusto 查詢語言參考資訊。 包含 Azure 監視器專用的其他元素和 Azure 監視器記錄查詢中不支援的元素。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80585703"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure 監視器記錄查詢語言差異

雖然 [Azure 監視器中的記錄](log-query-overview.md)建置於 [Azure 資料總管](/azure/data-explorer)上，並使用相同的 [Kusto 查詢語言](/azure/kusto/query)，但該語言的版本確實存在一些差異。 這篇文章會識別用於資料總管的語言版本，與用於 Azure 監視器記錄查詢的版本之間不同的元素。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的 KQL 元素
下列各節描述 Azure 監視器不支援的 Kusto 查詢語言的元素。

### <a name="statements-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的陳述式

* [別名](/azure/kusto/query/aliasstatement)
* [查詢參數](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的函式

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current()、current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的運算子

* [跨叢集聯結](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Azure 監視器中不支援的外掛程式

* [Python 外掛程式](/azure/kusto/query/pythonplugin)
* [sql_request 外掛程式](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Azure 監視器中的其他運算子
下列運算子支援特定 Azure 監視器功能，且無法在 Azure 監視器以外的地方使用。

* [應用程式 ( # B1 ](app-expression.md)
* [工作區 ( # B1 ](workspace-expression.md)

## <a name="next-steps"></a>後續步驟

- 取得[撰寫 Azure 監視器記錄查詢的不同資源](query-language.md)參考。
- 存取完整的 [Kusto 查詢語言的參考文件](/azure/kusto/query/)。
