---
title: 對應資料流程
description: 在 Azure Data Factory 中對應資料流程的總覽
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/12/2020
ms.openlocfilehash: ad3fa9db5a15f68f0538b5de29d9a89858c472e9
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212074"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>對應 Azure Data Factory 中的資料流程

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="what-are-mapping-data-flows"></a>什麼是對應資料流程？

對應資料流程會以視覺化方式設計 Azure Data Factory 中的資料轉換。 資料流程可讓資料工程師開發資料轉換邏輯，而不需要撰寫程式碼。 產生的資料流程會當做使用向外延展 Apache Spark 叢集 Azure Data Factory 管線內的活動來執行。 資料流程活動可以使用現有的 Azure Data Factory 排程、控制、流程和監視功能來運作。

對應資料流程可提供完全視覺化的體驗，而不需要撰寫任何程式碼。 您的資料流程會在 ADF 管理的執行叢集上執行，以進行相應放大的資料處理。 Azure Data Factory 會處理所有的程式碼轉譯、路徑優化，以及資料流程作業的執行。

## <a name="getting-started"></a>開始使用

資料流程是從 [factory 資源] 窗格建立的，例如管線和資料集。 若要建立資料流程，請選取 [ **Factory 資源**] 旁的加號，然後選取 [**資料流程]。** 

![新增資料流程](media/data-flow/new-data-flow.png "新增資料流程")

此動作會帶您前往 [資料流程] 畫布，您可以在其中建立轉換邏輯。 選取 [ **新增來源** ]，開始設定您的來源轉換。 如需詳細資訊，請參閱 [來源轉換](data-flow-source.md)。

## <a name="authoring-data-flows"></a>撰寫資料流程

對應資料流程具有獨特的撰寫畫布，其設計目的是要讓建立轉換邏輯變得更容易。 資料流程畫布分成三個部分：頂端列、圖形和設定面板。 

![畫布](media/data-flow/canvas1.png "畫布")

### <a name="graph"></a>圖形

圖形會顯示轉換資料流程。 它會顯示來源資料流入一或多個接收時的歷程。 若要新增來源，請選取 [新增 **來源**]。 若要加入新的轉換，請選取現有轉換右下方的加號。

![畫布](media/data-flow/canvas2.png "畫布")

### <a name="configuration-panel"></a>設定面板

[設定] 面板會顯示目前選取之轉換的特定設定。 如果未選取任何轉換，則會顯示資料流程。 在整體資料流程設定中，您可以編輯 [ **一般** ] 索引標籤底下的 [名稱] 和 [描述]，或透過 [ **參數** ] 索引標籤新增參數。如需詳細資訊，請參閱 [對應資料流程參數](parameters-data-flow.md)。

每個轉換都包含至少四個設定索引標籤。

#### <a name="transformation-settings"></a>轉換設定

每個轉換的 [設定] 窗格中的第一個索引標籤包含該轉換的特定設定。 如需詳細資訊，請參閱該轉換的檔頁面。

![[來源設定] 索引標籤](media/data-flow/source1.png "[來源設定] 索引標籤")

#### <a name="optimize"></a>最佳化

[ **優化** ] 索引標籤包含用於設定資料分割配置的設定。 若要深入瞭解如何優化您的資料流程，請參閱 [對應資料流程效能指南](concepts-data-flow-performance.md)。

![最佳化](media/data-flow/optimize.png "最佳化")

#### <a name="inspect"></a>檢查

[ **檢查** ] 索引標籤可讓您查看要轉換之資料流程的中繼資料。 您可以看到資料行計數、變更的資料行、加入的資料行、資料類型、資料行順序和資料行參考。 [**檢查**] 是您的中繼資料的唯讀視圖。 您不需要啟用 [偵測] 模式，即可在 [ **檢查** ] 窗格中查看中繼資料。

![檢查](media/data-flow/inspect1.png "檢查")

當您透過轉換來變更資料的圖形時，您會在 [ **檢查** ] 窗格中看到中繼資料變更流程。 如果您的來源轉換中沒有已定義的架構，則中繼資料將不會顯示在 [ **檢查** ] 窗格中。 缺乏中繼資料在架構漂移案例中很常見。

#### <a name="data-preview"></a>資料預覽

如果開啟 [偵錯工具] 模式，[ **資料預覽** ] 索引標籤會提供您每個轉換之資料的互動式快照集。 如需詳細資訊，請參閱 [在偵錯工具模式中的資料預覽](concepts-data-flow-debug-mode.md#data-preview)。

### <a name="top-bar"></a>頂端列

頂端列包含會影響整個資料流程的動作，例如儲存和驗證。 您也可以查看轉換邏輯的基礎 JSON 程式碼和資料流程腳本。 如需詳細資訊，請瞭解 [資料流程腳本](data-flow-script.md)。

## <a name="available-transformations"></a>可用的轉換

若要取得可用轉換的清單，請參閱 [對應資料流程轉換總覽](data-flow-transformation-overview.md) 。

## <a name="data-flow-activity"></a>資料流程活動

使用 [資料流程活動](control-flow-execute-data-flow-activity.md)，在 ADF 管線內運作對應的資料流程。 所有使用者都必須指定要使用的整合執行時間，並傳入參數值。 如需詳細資訊，請瞭解 [Azure 整合運行](concepts-integration-runtime.md#azure-integration-runtime)時間。

## <a name="debug-mode"></a>偵錯模式

[偵錯工具] 模式可讓您在建立及調試資料流程時，以互動方式查看每個轉換步驟的結果。 當您建立資料流程邏輯並使用資料流程活動執行管線 debug 回合時，可以在中使用 debug 會話。 若要深入瞭解，請參閱 [debug 模式檔](concepts-data-flow-debug-mode.md)。

## <a name="monitoring-data-flows"></a>監視資料流程

對應資料流程會與現有的 Azure Data Factory 監視功能整合。 若要瞭解如何瞭解資料流程監視輸出，請參閱 [監視對應資料流程](concepts-data-flow-monitoring.md)。

Azure Data Factory 小組已建立 [效能微調指南](concepts-data-flow-performance.md) ，可協助您在建立商務邏輯之後，將資料流程的執行時間優化。

## <a name="available-regions"></a>可用區域

對應的資料流程可在下欄區域中取得：

| Azure 區域 | ADF 中的資料流程 | Synapse Studio 中的資料流程 |
| ------------ | ----------------- | ---------------------------- |
|  澳大利亞中部 | | |  
| 澳大利亞中部 2 | | |
| 澳大利亞東部 | ✓ |  ✓ |
| 澳大利亞東南部   | ✓ | ✓ |
| 巴西南部  | ✓ |  |
| 加拿大中部 | ✓ |  |
| 印度中部 | ✓ |   ✓ |
| 美國中部    | ✓ |   ✓ |
| 中國東部 |      | ✓ |
| 中國東部 2  |   |    |
| 中國非地區 | | |
| 中國北部 |     | |
| 中國北部 2 | |  |
| 東亞 | ✓ | |
| 美國東部   | ✓ | ✓ |
| 美國東部 2 | ✓ | ✓ |
| 法國中部 | ✓ | ✓ |
| 法國南部  | | |
| 德國中部 (主權)  | | |
| 德國非區域 (主權)  | | |
| 德國北部 (公用)  | | |
| 德國東北部 (主權)  | | |
| 德國中西部 (公用)  |  | ✓ |
| 日本東部 | ✓ |  |
| 日本西部 |  | |
| 南韓中部 | ✓ |  |
| 南韓南部 | | |
| 美國中北部  | ✓ | ✓ |
| 歐洲北部  | ✓ |    |
| 挪威東部 | | |
| 挪威西部 | | |
| 南非北部    | ✓ | |
| 南非西部 |  |    |
| 美國中南部  | | ✓ |
| 印度南部 | | |
| 東南亞    | ✓ | ✓ |
| 瑞士北部 |   |  |
| 瑞士西部 | | |
| 阿拉伯聯合大公國中部 | | |
| 阿拉伯聯合大公國北部 |  |    |
| 英國南部  | ✓ |   | ✓ |
| 英國西部 |     | ✓ |
| US DoD 中部 | |  |
| US DoD 東部 | |  |
| US Gov 亞利桑那州 |      |  |
| US Gov (非區域)  | |  |
| US Gov 德克薩斯州 | |  |
| US Gov 維吉尼亞州 |     |  |
| 美國中西部 |     | ✓ |
| 西歐   | ✓ |   ✓ |
| 印度西部 | | |
| 美國西部   | ✓ |   |
| 美國西部 2 | ✓ |   ✓ | 

## <a name="next-steps"></a>後續步驟

* 瞭解如何建立 [來源轉換](data-flow-source.md)。
* 瞭解如何在「偵錯工具」 [模式](concepts-data-flow-debug-mode.md)中建立資料流程。
