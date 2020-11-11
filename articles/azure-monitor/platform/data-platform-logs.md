---
title: Azure 監視器記錄
description: 描述用來對監視資料進行先進分析的 Azure 監視器記錄。
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 35ed275419cc4319e7edfed608340853c986a927
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515620"
---
# <a name="azure-monitor-logs-overview"></a>Azure 監視器記錄檔總覽
Azure 監視器記錄是 Azure 監視器的功能，可從 [受監視的資源](../monitor-reference.md)收集及組織記錄和效能資料。 來自不同來源的資料，例如來自 Azure 服務的 [平臺記錄](platform-logs-overview.md) 、 [虛擬機器代理](agents-overview.md)程式的記錄和效能資料，以及 [應用程式](../app/app-insights-overview.md) 的使用方式和效能資料，都可以合併到單一工作區中，因此可以使用能夠快速分析數百萬筆記錄的精密查詢語言來分析它們。 您可以執行簡單的查詢，只抓取一組特定的記錄，或執行精密的資料分析，以識別監視資料中的重要模式。 使用 Log Analytics 以互動方式使用記錄查詢及其結果、在警示規則中使用這些查詢，以主動通知 br，或在活頁簿或儀表板中將其結果視覺化。

> [!NOTE]
> Azure 監視器記錄是支援 Azure 監視器的資料平臺的一半。 另一個是在時間序列資料庫中儲存數值資料的 [Azure 監視器計量](data-platform-metrics.md) 。 這可讓此資料比 Azure 監視器記錄中的資料更輕量，且能夠支援近乎即時的案例，使其特別適用于警示和快速偵測問題。 不過，計量只能將數值資料儲存在特定結構中，而記錄可以儲存各種不同的資料類型，每個資料類型都有自己的結構。 您也可以使用無法用於分析計量資料的記錄查詢，對記錄資料執行複雜的分析。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure 監視器記錄有何功用？
下表說明您可以在 Azure 監視器中使用記錄的一些不同方式：

|  |  |
|:---|:---|
| **分析** | 使用 Azure 入口網站中的 [Log Analytics](../log-query/get-started-portal.md) 來撰寫 [記錄查詢](../log-query/log-query-overview.md) ，並使用強大的分析引擎以互動方式分析記錄資料 |
| **警示** | 設定[記錄警示規則](alerts-log.md)，在查詢結果符合特定結果時，傳送通知或採取[自動化動作](action-groups.md)。 |
| **視覺化** | 將轉譯為表格或圖表的查詢結果釘選到 [Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)。<br>建立[活頁簿](../app/usage-workbooks.md)，以在互動式報告中與多組資料結合。 <br>將查詢的結果匯出到 [Power BI](powerbi.md) 以使用不同的視覺效果，並與 Azure 外部的使用者共用。<br>將查詢的結果匯出至 [Grafana](grafana-plugin.md) 以利用其儀表板功能，並與其他資料來源結合。|
| **深入解析** | 針對特定應用程式和服務提供自訂監視體驗的支援 [見解](../monitor-reference.md#insights-and-core-solutions) 。  |
| **擷取** | 使用 [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)，從命令列存取記錄查詢結果。<br>使用 [PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)，從命令列存取記錄查詢結果。<br>使用 [REST API](https://dev.loganalytics.io/)，從自訂應用程式存取記錄查詢結果。 |
| **匯出** | 設定將 [記錄資料自動匯出](logs-data-export.md) 到 Azure 儲存體帳戶或 Azure 事件中樞。<br>建立工作流程來擷取記錄資料，並使用 [Logic Apps](logicapp-flow-connector.md) 將其複製到外部位置。 |

![記錄概觀](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>資料集合
建立 Log Analytics 工作區之後，您必須設定不同的來源來傳送其資料。 不會自動收集任何資料。 視資料來源而定，此設定會有所不同。 例如， [建立診斷設定](diagnostic-settings.md) ，以將資源記錄從 Azure 資源傳送至工作區。 [啟用適用於 VM 的 Azure 監視器](../insights/vminsights-enable-overview.md) 以從虛擬機器收集資料。 設定 [工作區的資料來源](data-sources.md) ，以收集其他事件和效能資料。

- 查看 [Azure 監視器所監視的專案](../monitor-reference.md) ，以取得您可以設定來將資料傳送至 Azure 監視器記錄檔的完整資料來源清單。


## <a name="log-analytics-workspaces"></a>Log Analytics 工作區
Azure 監視器記錄所收集的資料會儲存在一個以上的 [Log Analytics 工作區](./design-logs-deployment.md)中。 工作區會定義資料的地理位置、定義使用者可以存取資料的存取權限，以及定價層和資料保留等設定。  

您必須建立至少一個工作區，才能使用 Azure 監視器記錄。 單一工作區對於您的所有監視資料而言可能就已足夠，也可以選擇根據您的需求建立多個工作區。 例如，您可能會有一個工作區用於生產資料，而另一個工作區用於測試。 

- 請參閱 [在 Azure 入口網站中建立 Log Analytics 工作區](../learn/quick-create-workspace.md) ，以建立新的工作區。
- 如需建立多個工作區的考慮，請參閱 [設計 Azure 監視器記錄部署](design-logs-deployment.md) 。

## <a name="data-structure"></a>資料結構
記錄查詢會從 Log Analytics 工作區取出其資料。 每個工作區包含多個資料表，這些資料表會組織成具有多個資料列的個別資料行。 每個資料表都是由資料來源所提供的資料列所共用的唯一資料行集合所定義。 

[![Azure 監視器記錄結構](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Application Insights 中的記錄資料也會儲存在 Azure 監視器記錄中，但會根據您的應用程式設定方式儲存在不同的位置。 針對以工作區為基礎的應用程式，資料會儲存在一組標準資料表的 Log Analytics 工作區中，以保存應用程式要求、例外狀況和頁面流覽等資料。 多個應用程式可以使用相同的工作區。 若是傳統的應用程式，資料不會儲存在 Log Analytics 工作區中。 它會使用相同的查詢語言，您可以使用 Azure 入口網站中的相同 Log Analytics 工具來建立和執行查詢。 不過，傳統應用程式的資料會彼此分開儲存。 它的一般結構與工作區應用程式相同，但資料表和資料行名稱不同。 如需工作區型和傳統應用程式之架構的詳細比較，請參閱以 [工作區為基礎的資源變更](../app/apm-tables.md) 。


> [!NOTE]
> 我們仍然會針對 Application Insights 體驗中的 Application Insights 傳統資源查詢、活頁簿、記錄型警示，提供完整的回溯相容性。 若要針對[新的工作區型資料表結構/結構描述](../app/apm-tables.md)進行查詢/檢視，您必須先瀏覽至您的 Log Analytics 工作區。 在預覽期間，從 Application Insights 窗格中選取 [記錄]，可讓您存取傳統 Application Insights 查詢體驗。 如需詳細資訊，請參閱 [查詢範圍](../log-query/scope.md) 。


[![Application Insights 的 Azure 監視器記錄結構](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>記錄查詢
您可以使用記錄查詢來從 Log Analytics 工作區取出資料，此查詢是處理資料並傳回結果的唯讀要求。 記錄查詢是以 [Kusto 查詢語言撰寫 (KQL) ](/azure/data-explorer/kusto/query/)，它是 Azure 資料總管所使用的相同查詢語言。 您可以在 Log Analytics 中撰寫記錄查詢，以互動方式分析結果、在警示規則中使用這些查詢，以主動通知問題，或是在活頁簿或儀表板中包含其結果。 深入解析包含預建查詢，以支援其觀點和活頁簿。

- 請參閱 [Azure 監視器中的記錄查詢](log-query/../../log-query/log-query-overview.md) ，以取得使用記錄查詢的位置，以及教學課程和其他檔的參考，以協助您開始使用。

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
使用 Log Analytics （這是 Azure 入口網站中的工具）來編輯和執行記錄查詢，並以互動方式分析其結果。 然後，您可以使用您所建立的查詢來支援 Azure 監視器中的其他功能，例如記錄查詢警示和活頁簿。 從 [Azure 監視器] 功能表中的 [ **記錄** ] 選項，或從 Azure 入口網站的大部分其他服務存取 Log Analytics。

- 如需 Log Analytics 的說明，請參閱 [Azure 監視器中的 Log Analytics 總覽](/log-query/log-analytics-overview.md) 。 
- 請參閱 [Log analytics 教學](/log-query/log-analytics-tutorial.md) 課程，以逐步解說使用 log analytics 功能來建立簡單的記錄查詢並分析其結果。



## <a name="relationship-to-azure-data-explorer"></a>與 Azure 資料總管的關聯性
Azure 監視器記錄以 Azure 資料總管為基礎。 Log Analytics 工作區大致上等同于 Azure 資料總管中的資料庫，資料表的結構相同，而且兩者都使用相同的 Kusto 查詢語言 (KQL) 。 使用 Log Analytics 來處理 Azure 入口網站中 Azure 監視器查詢的經驗，與使用 Azure 資料總管 Web UI 的體驗類似。 您甚至可以 [在 Azure 資料總管查詢中包含 Log Analytics 工作區的資料](/azure/data-explorer/query-monitor-data)。 


## <a name="next-steps"></a>後續步驟

- 深入瞭解 [記錄查詢](../log-query/log-query-overview.md) ，以從 log Analytics 工作區取出和分析資料。
- 了解 [Azure 監視器中的計量](data-platform-metrics.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
