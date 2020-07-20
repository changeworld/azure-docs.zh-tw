---
title: 對應資料流程的偵錯模式
description: 在建置資料流程時啟動互動式偵錯工作階段
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 4a763a3bb4d46ba03808423d4d1283381c1174a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81605400"
---
# <a name="mapping-data-flow-debug-mode"></a>對應資料流程的偵錯模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>總覽

Azure Data Factory 對應資料流程的 [偵錯工具模式] 可讓您在建立和偵錯工具資料流程時，以互動方式監看「資料圖形」轉換。 您可以在資料流程設計會話以及管線的資料流程執行期間，使用 debug 會話。 若要開啟 [偵錯模式]，請使用設計介面頂端的 [資料流程 Debug] 按鈕。

![Debug 滑杆](media/data-flow/debugbutton.png "Debug 滑杆")

當您開啟滑杆時，系統會提示您選取想要使用的整合執行時間設定。 如果選擇 AutoResolveIntegrationRuntime，則會啟動具有8個一般計算核心（具有60分鐘存留時間）的叢集。 如需有關「資料流程整合執行時間」的詳細資訊，請參閱[資料流程效能](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)。

![Debug IR 選取範圍](media/data-flow/debugbutton2.png "Debug IR 選取範圍")

當開啟 [偵錯工具] 模式時，您會以互動方式建立使用中 Spark 叢集的資料流程。 在 Azure Data Factory 中關閉偵錯後，將會關閉此工作階段。 您應該留意在您已開啟偵錯工作階段的期間內，由 Azure Databricks 所產生的每小時費用。

在大部分情況下，最好是以「偵錯工具」模式建立資料流程，以便在 Azure Data Factory 中發佈工作之前，先驗證您的商務邏輯並查看資料轉換。 使用 [管線] 面板上的 [偵錯工具] 按鈕來測試管線中的資料流程。

## <a name="cluster-status"></a>叢集狀態

當叢集準備好進行 debug 時，設計介面頂端的叢集狀態指標會變成綠色。 如果您的叢集已經暖機，則綠色指示器幾乎會立即出現。 如果您在進入 [偵錯工具] 模式時，叢集尚未執行，則您必須等候5-7 分鐘，讓叢集加速。 指標會旋轉，直到其就緒為止。

當您完成偵錯工具後，請關閉 Debug 參數，讓您的 Azure Databricks 叢集可以終止，而且您不再需要為 Debug 活動計費。

## <a name="debug-settings"></a>偵錯設定

您可以按一下 [資料流程畫布] 工具列上的 [偵錯工具設定] 來編輯 debug 設定。 您可以在這裡選取每個來源轉換所使用的資料列限制或檔案來源。 此設定中的資料列限制僅適用于目前的「偵錯工具」會話。 您也可以選取要用於 SQL DW 來源的預備連結服務。 

![Debug 設定](media/data-flow/debug-settings.png "偵錯設定")

如果您的資料流程或其任何參考的資料集內有參數，您可以選取 [**參數**] 索引標籤，指定要在偵錯工具中使用的值。

![Debug 設定參數](media/data-flow/debug-settings2.png "Debug 設定參數")

## <a name="data-preview"></a>資料預覽

若開啟偵錯，[資料預覽] 索引標籤在底部面板上亮起。 若未開啟任何偵錯工具模式，資料流程只會在 [檢查] 索引標籤中顯示每個轉換的目前中繼資料和輸出。資料預覽只會查詢您在 [偵錯工具] 設定中設定為限制的資料列數目。 按一下 **[** 重新整理] 以提取資料預覽。

![資料預覽](media/data-flow/datapreview.png "資料預覽")

> [!NOTE]
> 檔案來源只會限制您看到的資料列，而不是讀取的資料列。 對於非常大型的資料集，建議您取得該檔案的一小部分，並將它用於您的測試。 您可以針對每個屬於檔案資料集類型的來源，在 [偵錯工具] 設定中選取暫存檔案。

在資料流程的偵錯模式中執行時，您的資料不會寫入至接收轉換。 Debug 會話的目的是做為您轉換的測試控管。 偵錯期間不需要接收，而且會在資料流程中忽略。 如果您想要測試您的接收中的資料寫入，請從 Azure Data Factory 管線執行資料流程，並從管線使用 Debug 執行。

### <a name="testing-join-conditions"></a>測試聯結條件

當單元測試聯結、存在或查閱轉換時，請確定您使用一小組已知的資料進行測試。 您可以使用上述的 [偵錯工具設定] 選項，設定要用於測試的暫存檔案。 這是必要的，因為在限制或取樣大型資料集的資料列時，您無法預測要將哪些資料列和哪些金鑰讀入流程中進行測試。 結果是不具決定性的，這表示您的聯結條件可能會失敗。

### <a name="quick-actions"></a>快速動作

看到資料預覽之後，您就可以產生快速轉換，以轉換、移除或修改資料行。 按一下欄標題，然後從 [資料預覽] 工具列中選取其中一個選項。

![快速動作](media/data-flow/quick-actions1.png "快速動作")

一旦您選取修改後，資料預覽就會立即重新整理。 按一下右上角的 [**確認**]，以產生新的轉換。

![快速動作](media/data-flow/quick-actions2.png "快速動作")

**轉換**和**Modify**會產生「衍生的資料行」轉換，而「**移除**」將會產生「選取」轉換。

![快速動作](media/data-flow/quick-actions3.png "快速動作")

> [!NOTE]
> 如果您編輯資料流程，則必須先重新提取資料預覽，再新增快速轉換。

### <a name="data-profiling"></a>資料分析

在 [資料預覽] 索引標籤中選取資料行，然後按一下 [資料預覽] 工具列中的 [**統計**資料]，就會在資料格的最右邊顯示圖表，其中包含每個欄位的詳細統計資料。 Azure Data Factory 會根據要顯示圖表類型的資料取樣近進行判斷。 高基數位段會預設為 Null/NOT Null 圖表，而具有低基數的類別和數值資料將會顯示顯示資料值頻率的橫條圖。 您也會看到字串欄位的最大/len 長度、數值欄位中的最小/最大值、標準 dev、百分位數、計數和平均值。

![資料行統計資料](media/data-flow/stats.png "資料行統計資料")

## <a name="next-steps"></a>後續步驟

* 當您完成建立和偵測資料流程之後，請[從管線加以執行。](control-flow-execute-data-flow-activity.md)
* 以資料流程測試管線時，請使用管線的「偵測[執行」執行選項。](iterative-development-debugging.md)
