---
title: 資料流程
description: Azure Synapse Analytics 中的資料流程總覽
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592603"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的資料流程

## <a name="what-are-data-flows"></a>什麼是資料流程？

資料流程是以視覺化方式設計 Azure Synapse Analytics 中的資料轉換。 資料流程可讓資料工程師開發資料轉換邏輯，而不需要撰寫程式碼。 產生的資料流程會執行為使用相應放大 Apache Spark 叢集 Azure Synapse Analytics 管線內的活動。 您可以使用現有的 Azure Synapse Analytics 排程、控制、流程和監視功能來實際運作資料流程活動。

資料流程提供完全不需要撰寫程式碼的視覺體驗。 資料流程會在 Synapse 管理的執行叢集上執行，以進行相應放大的資料處理。 Azure Synapse Analytics 會處理所有程式碼轉譯、路徑優化，以及執行資料流程作業。

## <a name="getting-started"></a>開始使用

資料流程是從 Synapse studio 中的 [開發] 窗格所建立。 若要建立資料流程，請選取 [**開發**] 旁的加號，然後選取 [**資料流程]。** 

![新增資料流程](media/data-flow/new-data-flow.png)

此動作會帶您前往 [資料流程] 畫布，您可以在其中建立轉換邏輯。 選取 [ **新增來源** ] 開始設定來源轉換。 如需詳細資訊，請參閱 [來源轉換](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="authoring-data-flows"></a>編寫資料流程

資料流程有獨特的撰寫畫布，目的是讓建立轉換邏輯變得更容易。 資料流程畫布分為三個部分：頂端列、圖形和設定面板。 

![螢幕擷取畫面顯示已標示頂端橫條圖、圖形和設定面板的資料流程畫布。](media/data-flow/canvas-1.png)

### <a name="graph"></a>圖形

圖形會顯示轉換資料流程。 它會顯示來源資料流入一或多個接收時的歷程。 若要加入新的來源，請選取 [ **新增來源**]。 若要加入新的轉換，請選取現有轉換右下角的加號。 深入瞭解如何 [管理資料流程圖形](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

![螢幕擷取畫面顯示畫布的圖形元件，其中包含 [搜尋] 文字方塊。](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Configuration 面板

[設定] 面板會顯示目前所選轉換的特定設定。 如果未選取任何轉換，則會顯示資料流程。 在整體的資料流程設定中，您可以透過 [ **參數** ] 索引標籤加入參數。如需詳細資訊，請參閱 [資料流程參數](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

每個轉換都包含至少四個設定索引標籤。

#### <a name="transformation-settings"></a>轉換設定

每個轉換的設定窗格中的第一個索引標籤包含該轉換的特定設定。 如需詳細資訊，請參閱該轉換的檔頁面。

![[來源設定] 索引標籤](media/data-flow/source-1.png)

#### <a name="optimize"></a>最佳化

[ **優化** ] 索引標籤包含設定資料分割配置的設定。 若要深入瞭解如何優化您的資料流程，請參閱「 [對應資料流程效能指南](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)」。

![螢幕擷取畫面：顯示 [優化] 索引標籤](media/data-flow/optimize.png)

#### <a name="inspect"></a>檢查

[ **檢查** ] 索引標籤會提供您要轉換之資料流程的中繼資料的觀點。 您可以看到資料行計數、變更的資料行、新增的資料行、資料類型、資料行順序和資料行參考。 **檢查** 是您中繼資料的唯讀視圖。 您不需要啟用 [偵測模式]，即可查看 [ **檢查** ] 窗格中的中繼資料。

![檢查索引標籤](media/data-flow/inspect.png)

當您透過轉換來變更資料的形狀時，您會在 [ **檢查** ] 窗格中看到中繼資料變更流程。 如果來源轉換中沒有已定義的架構，則中繼資料將不會顯示在 [ **檢查** ] 窗格中。 架構漂移案例中很常見中繼資料的缺乏。

#### <a name="data-preview"></a>資料預覽

如果處於開啟狀態，[ **資料預覽** ] 索引標籤會提供每個轉換之資料的互動式快照集。 如需詳細資訊，請參閱 [在偵錯工具模式中的資料預覽](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview)。

### <a name="top-bar"></a>頂端列

頂端列包含會影響整個資料流程的動作，例如驗證和偵錯工具設定。 您也可以查看轉換邏輯的基礎 JSON 程式碼和資料流程腳本。

## <a name="available-transformations"></a>可用的轉換

請參閱 [對應資料流程轉換總覽](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ，以取得可用轉換的清單。

## <a name="data-flow-activity"></a>資料流程活動

使用「資料流程」 [活動](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)，在 Azure Synapse Analytics 管線內實際運作資料流程。 所有使用者都必須指定要使用的整合執行時間，並傳入參數值。 如需詳細資訊，請瞭解 [Azure integration runtime](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)。

## <a name="debug-mode"></a>偵錯模式

當您建立及偵測資料流程時，Debug 模式可讓您以互動方式查看每個轉換步驟的結果。 使用資料流程活動建立資料流程邏輯和執行管線偵錯工具時，可以在中使用 debug 會話。 若要深入瞭解，請參閱 [debug 模式檔](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)集。

## <a name="monitoring-data-flows"></a>監視資料流程

資料流程與現有的 Azure Synapse Analytics 監視功能整合。 若要瞭解如何瞭解資料流程監視輸出，請參閱 [監視對應資料流程](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

Azure Synapse Analytics 小組已建立 [效能微調指南](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ，可協助您在建立商務邏輯之後，將資料流程的執行時間優化。

## <a name="next-steps"></a>後續步驟

* 瞭解如何建立 [來源轉換](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* 瞭解如何在 [偵錯工具模式](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)中建立資料流程。
