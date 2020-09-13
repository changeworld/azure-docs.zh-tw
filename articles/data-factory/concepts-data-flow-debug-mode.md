---
title: 對應資料流程的偵測模式
description: 在建置資料流程時啟動互動式偵錯工作階段
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/11/2020
ms.openlocfilehash: 1d996e62fe60606c3eb93a638d229028ee0471e6
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030583"
---
# <a name="mapping-data-flow-debug-mode"></a>對應資料流程的偵測模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>概觀

Azure Data Factory 對應資料流程的 debug 模式，可讓您在建立及偵測資料流程時，以互動方式監看資料圖形轉換。 您可以在資料流程設計會話中以及在管線的資料流程執行時，使用 debug 會話。 若要開啟 [偵測模式]，請使用設計介面頂端的 [資料流程偵錯工具] 按鈕。

![調試滑杆](media/data-flow/debugbutton.png "調試滑杆")

一旦開啟滑杆，系統將會提示您選取想要使用的整合執行時間設定。 如果選擇了 AutoResolveIntegrationRuntime，則會啟動具有8個一般計算核心計算的叢集，且具有60分鐘的存留時間。 如需有關「資料流程整合執行時間」的詳細資訊，請參閱 [資料流程效能](concepts-data-flow-performance.md#ir)。

![Debug IR 選取專案](media/data-flow/debugbutton2.png "Debug IR 選取專案")

當偵錯工具模式為 on 時，您會以互動方式建立使用中 Spark 叢集的資料流程。 在 Azure Data Factory 中關閉偵錯後，將會關閉此工作階段。 您應該留意在您已開啟偵錯工作階段的期間內，由 Azure Databricks 所產生的每小時費用。

在大部分的情況下，最好是在「偵錯工具」模式中建立資料流程，以便在 Azure Data Factory 中發佈工作之前，先驗證您的商務邏輯並查看資料轉換。 使用管線面板上的 [偵錯工具] 按鈕來測試管線中的資料流程。

## <a name="cluster-status"></a>叢集狀態

當叢集準備進行 debug 時，設計介面頂端的叢集狀態指標會變成綠色。 如果您的叢集已經暖機，則綠色指示器幾乎會立即出現。 如果您在輸入「偵測模式」時，您的叢集尚未執行，則您必須等候5-7 分鐘，叢集才能啟動。 指標會旋轉，直到其就緒為止。

當您完成偵錯工具時，請關閉 Debug 參數，讓您的 Azure Databricks 叢集可以終止，而不會再向您收取 Debug 活動的費用。

## <a name="debug-settings"></a>偵錯設定

開啟「偵測模式」之後，您就可以編輯資料流程預覽資料的方式。 您可以按一下 [資料流程畫布] 工具列上的 [偵錯工具設定] 來編輯偵錯工具設定。 您可以在此處選取要用於每個來源轉換的資料列限制或檔案來源。 這項設定中的資料列限制只適用于目前的偵錯工具會話。 您也可以選取要用於 Azure Synapse Analytics 來源的暫存連結服務。 

![Debug 設定](media/data-flow/debug-settings.png "偵錯設定")

如果您的資料流程或其任何參考資料集有參數，您可以選取 [ **參數** ] 索引標籤來指定要在調試期間使用的值。

![Debug settings 參數](media/data-flow/debug-settings2.png "Debug settings 參數")

ADF 資料流程中用於偵測模式的預設 IR 是具有4核心單一驅動程式節點的小型4核心單一背景工作節點。 當測試您的資料流程邏輯時，這會很適合用來處理較小的資料樣本。 如果您在資料預覽期間展開偵錯工具設定中的資料列限制，或在管線偵測期間于來源中設定較大量的取樣資料列，則您可能會想要考慮在新的 Azure Integration Runtime 中設定較大的計算環境。 然後，您可以使用較大的計算環境來重新開機您的偵錯工具會話。

## <a name="data-preview"></a>資料預覽

若開啟偵錯，[資料預覽] 索引標籤在底部面板上亮起。 若未開啟 debug 模式，資料流程將只會顯示您在 [檢查] 索引標籤中的每個轉換的目前中繼資料。資料預覽只會查詢您已在偵錯工具設定中設定為限制的資料列數目。 按一下 **[** 重新整理] 以提取資料預覽。

![資料預覽](media/data-flow/datapreview.png "資料預覽")

> [!NOTE]
> 檔案來源只會限制您看到的資料列，而不是所讀取的資料列。 針對非常大型的資料集，建議您只使用該檔案的一小部分，並將它用於您的測試。 您可以針對每個屬於檔案資料集類型的來源，在「偵錯工具設定」中選取暫存檔案。

在資料流程的偵錯模式中執行時，您的資料不會寫入至接收轉換。 Debug 會話旨在做為您轉換的測試控管。 偵錯期間不需要接收，而且會在資料流程中忽略。 如果您想要測試在接收中寫入資料，請從 Azure Data Factory 管線執行資料流程，然後從管線使用 Debug 執行。

資料預覽是您已轉換資料的快照，其使用資料列限制和資料從 Spark 記憶體中的資料框架進行資料取樣。 因此，在此情況下，不會使用或測試接收器驅動程式。

### <a name="testing-join-conditions"></a>測試聯結條件

當單元測試聯結、存在或查閱轉換時，請確定您針對測試使用一組較少的已知資料。 您可以使用上述的 [偵錯工具設定] 選項，設定要用於測試的暫存檔案。 這是必要的，因為當限制或取樣大型資料集中的資料列時，您無法預測要將哪些資料列和哪些索引鍵讀入流程中以進行測試。 結果並不具決定性，這表示您的聯結條件可能會失敗。

### <a name="quick-actions"></a>快速動作

一旦您看到資料預覽，就可以產生快速轉換來轉換、移除或修改資料行。 按一下資料行標題，然後從 [資料預覽] 工具列中選取其中一個選項。

![快速動作](media/data-flow/quick-actions1.png "快速動作")

一旦您選取了修改，資料預覽就會立即重新整理。 在右上角按一下 [ **確認** ]，以產生新的轉換。

![快速動作](media/data-flow/quick-actions2.png "快速動作")

**轉換** 和 **Modify** 將會產生「衍生的資料行」轉換，而「 **移除** 」將會產生「選取」轉換。

![快速動作](media/data-flow/quick-actions3.png "快速動作")

> [!NOTE]
> 如果您編輯資料流程，您必須在新增快速轉換之前，先重新提取資料預覽。

### <a name="data-profiling"></a>資料分析

在 [資料預覽] 索引標籤中選取資料行，然後按一下 [資料預覽] 工具列中的 [ **統計** 資料]，將會在資料方格最右側顯示圖表，其中包含每個欄位的詳細統計資料。 Azure Data Factory 會根據要顯示圖表類型的資料取樣近進行判斷。 高基數位段將預設為 Null/NOT Null 圖表，而具有低基數的類別和數值資料會顯示顯示資料值頻率的橫條圖。 您也會看到字串欄位的最大/長度長度、數值欄位中的最小/最大值、標準開發、百分位數、計數和平均值。

![資料行統計資料](media/data-flow/stats.png "資料行統計資料")

## <a name="next-steps"></a>接下來的步驟

* 當您完成建立及偵測資料流程之後，請 [從管線執行它。](control-flow-execute-data-flow-activity.md)
* 使用資料流程測試您的管線時，請使用「管線 [偵錯工具執行」選項。](iterative-development-debugging.md)
