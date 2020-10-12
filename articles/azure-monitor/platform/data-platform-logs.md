---
title: Azure 監視器中的記錄 | Microsoft Docs
description: 描述 Azure 監視器中的記錄，這些記錄是用於監視資料的先進分析。
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032757"
---
# <a name="azure-monitor-logs-overview"></a>Azure 監視器記錄檔總覽
Azure 監視器記錄是 Azure 監視器的功能，可從各種來源收集和組織記錄資料，並使用精密的查詢語言來進行分析。 來自不同來源的資料可以合併到單一工作區中，並一起分析以執行這類工作和趨勢分析、警示和視覺效果。

## <a name="relationship-to-azure-monitor-metrics"></a>Azure 監視器計量的關聯性
Azure 監視器計量會將數值資料儲存在時間序列資料庫中，這可讓此資料比 Azure 監視器記錄更輕量，而且能夠支援近乎即時的案例，因此特別適用于警示和快速偵測問題。 不過，計量只能將數值資料儲存在特定結構中，而記錄可以儲存各種不同的資料類型，每個資料類型都有自己的結構。 您也可以使用無法用於分析計量資料的記錄查詢，對記錄資料執行複雜的分析。

除了計量以外，數值資料通常會從資料來源傳送到記錄。 雖然在記錄檔中收集和保留這項資料還有額外的費用，但它可讓您在記錄查詢中包含計量資料，並使用其他監視資料進行分析。

## <a name="relationship-to-azure-data-explorer"></a>與 Azure 資料總管的關聯性
Azure 監視器記錄以 Azure 資料總管為基礎。 Log Analytics 工作區大致上等同于 Azure 資料總管中的資料庫，資料表的結構相同，而且兩者都使用相同的 Kusto 查詢語言 (KQL) 。 使用 Log Analytics 來處理 Azure 入口網站中 Azure 監視器查詢的經驗，與使用 Azure 資料總管 Web UI 的體驗類似。 您甚至可以 [在 Azure 資料總管查詢中包含 Log Analytics 工作區的資料](/azure/data-explorer/query-monitor-data)。 


## <a name="structure-of-data"></a>資料結構
Azure 監視器記錄所收集的資料會儲存在 [Log Analytics 工作區](./design-logs-deployment.md) 中，其中包含多個資料表，每個資料表都會儲存來自特定來源的資料。 工作區會定義資料的地理位置、定義使用者可以存取資料的存取權限，以及定價層和資料保留等設定。 您可以針對所有監視資料使用單一工作區，或根據您的需求建立多個工作區。 如需建立多個工作區的考慮，請參閱 [設計 Azure 監視器記錄部署](design-logs-deployment.md) 。

每個工作區包含多個資料表，這些資料表會組織成具有多個資料列的個別資料行。 每個資料表都是由資料來源所提供的資料列所共用的唯一資料行集合所定義。 

[![Azure 監視器記錄結構](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Application Insights 中的記錄資料也會儲存在 Azure 監視器記錄中，但會根據您的應用程式設定方式儲存在不同的位置。 針對以工作區為基礎的應用程式，資料會儲存在一組標準資料表的 Log Analytics 工作區中，以保存應用程式要求、例外狀況和頁面流覽等資料。 多個應用程式可以使用相同的工作區。 若是傳統的應用程式，資料不會儲存在 Log Analytics 工作區中。 它會使用相同的查詢語言，您可以使用 Azure 入口網站中的相同 Log Analytics 工具來建立和執行查詢。 不過，傳統應用程式的資料會彼此分開儲存。 它的一般結構與工作區應用程式相同，但資料表和資料行名稱不同。 如需這兩者的詳細比較，請參閱以 [工作區為基礎的資源變更](../app/apm-tables.md) 。


> [!NOTE]
> 我們仍然會針對 Application Insights 體驗中的 Application Insights 傳統資源查詢、活頁簿、記錄型警示，提供完整的回溯相容性。 若要針對[新的工作區型資料表結構/結構描述](../app/apm-tables.md)進行查詢/檢視，您必須先瀏覽至您的 Log Analytics 工作區。 在預覽期間，從 Application Insights 窗格中選取 [記錄]，可讓您存取傳統 Application Insights 查詢體驗。 如需詳細資訊，請參閱 [查詢範圍](../log-query/scope.md) 。


[![Application Insights 的 Azure 監視器記錄結構](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>記錄查詢
您可以使用 [記錄查詢](../log-query/log-query-overview.md) 來從 log Analytics 工作區取出資料，此查詢是處理資料並傳回結果的唯讀要求。 記錄查詢是以 [Kusto 查詢語言撰寫 (KQL) ](/azure/data-explorer/kusto/query/)，也就是 Azure 資料總管所使用的查詢語言。 使用 Log Analytics，這是 Azure 入口網站中用來編輯和執行記錄查詢並以互動方式分析其結果的工具。 然後，您可以使用您所建立的查詢來支援 Azure 監視器中的其他功能，例如記錄查詢警示和活頁簿。


## <a name="sources-of-data-for-azure-monitor-logs"></a>Azure 監視器記錄的資料來源
Azure 監視器會從各種來源收集記錄資料，包括 Azure 監視器中的資源，以及在虛擬機器中執行的代理程式。 如需將資料傳送至 Log Analytics 工作區的資料來源完整清單，請參閱 [Azure 監視器所監視的內容](../monitor-reference.md) 。



## <a name="next-steps"></a>後續步驟

- 深入瞭解 [記錄查詢](../log-query/log-query-overview.md) ，以從 log Analytics 工作區取出和分析資料。
- 了解 [Azure 監視器中的計量](data-platform-metrics.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。

