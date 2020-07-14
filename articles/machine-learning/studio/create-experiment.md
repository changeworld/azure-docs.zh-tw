---
title: 快速入門：建立資料科學實驗
titleSuffix: ML Studio (classic) - Azure
description: 此機器學習快速入門會引導您輕鬆進行資料科學實驗。 我們將使用迴歸演算法預測汽車價格。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: f8984b3b712d591b64c383dabb723ba5afc61efc
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965139"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio-classic"></a>快速入門：在 Azure Machine Learning Studio (傳統) 中建立您的第一個資料科學實驗

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

在此快速入門中，您將在 [Azure Machine Learning Studio (傳統)](what-is-ml-studio.md) 中建立一個機器學習實驗，根據使用製造和技術規格等不同變數來預測汽車的價格。

如果您之前未曾接觸過機器學習，可以觀看[適用於初學者的資料科學](data-science-for-beginners-the-5-questions-data-science-answers.md)影片系列，其淺白的語言和概念講解將帶領您認識機器學習。

本快速入門將依照預設工作流程進行實驗：

1. **建立模型**
    - [取得資料]
    - [準備資料]
    - [定義特性]
1. **訓練模型**
    - [選擇及套用演算法]
1. **對模型評分與測試**
    - [預測新的汽車價格]

[取得資料]: #get-the-data
[準備資料]: #prepare-the-data
[定義特性]: #define-features
[選擇及套用演算法]: #choose-and-apply-an-algorithm
[預測新的汽車價格]: #predict-new-automobile-prices

## <a name="get-the-data"></a>取得資料

要執行機器學習，首先您必須要有資料。
Studio (傳統) 附有多個範例資料集供您使用，或者，您可以從許多來源匯入資料。 在此範例中，我們將使用您的工作區內含的範例資料集**汽車價格資料 (原始)** 。
此資料集將包含許多個別汽車的項目，包括製造、模型、技術規格和價格等資訊。

> [!TIP]
> 您可以在 [Azure AI 資源庫](https://gallery.azure.ai)中找到下列實驗的工作複本。 移至 **[您的第一個資料科學實驗 - 汽車價格預測](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** \(英文\)，然後按一下 [在 Studio 中開啟]，以將實驗的複本下載到您的 Machine Learning Studio (傳統) 工作區。

以下說明如何匯入資料集到您的實驗。

1. 按一下 [Machine Learning Studio (傳統)] 視窗底部的 [+新增]，來建立新的實驗。 選取 [實驗] >  [空白實驗]。

1. 您可以在畫布頂端看到實驗的預設名稱。 選取這段文字，然後重新命名為有意義的名稱，例如**汽車價格預測**。 此名稱不必是唯一的。

    ![將實驗重新命名](./media/create-experiment/rename-experiment.png)

1. 實驗畫布左側是資料集和模組的調色盤。 在此調色盤頂端的 [搜尋] 方塊中輸入**汽車**，可尋找標示為**汽車價格資料 (原始)** 的資料集。 將此資料集拖曳到實驗畫布。

    ![找出汽車資料集，並將其拖曳到實驗畫布上](./media/create-experiment/type-automobile.png)

若想知道此資料的呈現情形，請按一下汽車資料集底部的輸出連接埠，然後選取 [視覺化]。

![按一下輸出連接埠，然後選取 [視覺化]](./media/create-experiment/select-visualize.png)

> [!TIP]
> 資料集和模組以小圓圈代表輸入和輸出連接埠，輸入連接埠位在頂端，輸出連接埠在底端。
若要在您的實驗中建立資料流程，您要將某個模組的輸出連接埠連接到另一個模組的輸入連接埠。
您隨時可以按一下資料集或模組的輸出連接埠，以查看資料於該時間點在資料流程中的型態。

在此資料集中，每個資料列分別代表一款汽車，而與每款汽車相關聯的變數會顯示為資料行。 我們將使用特定一款汽車的變數，來預測最右側資料行 (資料行 26，標題為「價格」) 中的價格。

![檢視資料視覺效果視窗中的汽車資料](./media/create-experiment/visualize-auto-data.png)

按一下右上角的 "**x**"，以關閉視覺化視窗。

## <a name="prepare-the-data"></a>準備資料

資料集通常必須先經過某些前置處理，才能進行分析。 您可能已經注意到在各種不同資料列的資料行中有遺漏的值。 必須清除這些遺漏的值，讓模型才能正確地分析資料。 我們將移除含有遺漏值的所有資料列。 此外， **自負虧損** 資料行含有比例很高的遺漏值，因此我們會將該資料行從模型中完全排除。

> [!TIP]
> 在使用大部分的模組時，都必須從輸入資料中清除遺漏值。

首先，我們新增一個完全移除 [自負虧損] 資料行的模組。 然後，再新增一個將任何含有遺漏資料的資料列移除的模組。

1. 在模組選擇區頂端的搜尋方塊中輸入**選取資料行**，以尋找[選取資料集中的資料行][select-columns]模組。 然後，將其拖曳到實驗畫布上。 此模組可讓我們選取要將哪些資料行包含在模型中，或是從模型中排除。

1. 將**汽車價格資料 (原始)** 資料集的輸出連接埠連線至「選取資料集中的資料行」的輸入連接埠。

    ![將「選取資料集中的資料行」模組新增至實驗畫布，並加以連線](./media/create-experiment/type-select-columns.png)

1. 按一下[選取資料集中的資料行][select-columns]模組，然後按一下 [屬性] 窗格中的 [啟動資料行選取器]。

   - 在左側按一下 [套用規則] 
   - 在 [開始於] 下，按一下 [所有資料行]。 這些規則會指示[選取資料集中的資料行][select-columns]傳遞所有資料行 (但我們將排除的資料行除外)。
   - 在下拉式清單中，選取 [排除] 和 [資料行名稱]，然後按一下文字方塊內部。 資料行清單隨即顯示。 選取 [自負虧損]，該資料行就會新增到文字方塊中。
   - 按一下核取記號 ([確定]) 按鈕，以關閉資料行選取器 (位於右下方)。

     ![啟動資料行選取器，並排除 [自負虧損] 資料行](./media/create-experiment/launch-column-selector.png)

     現在，[選取資料集中的資料行] 的屬性窗格指出它會傳遞資料集中的所有資料行，但 [自負虧損] 除外。

     ![[屬性] 窗格顯示 [自負虧損] 資料行已排除](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > 您可以按兩下模組並輸入文字，為模組新增註解。 這有助於您快速檢視模組在您實驗中的執行情況。 在此案例中，按兩下 [選取資料集中的資料行][select-columns] 模組，然後輸入註解「排除自負虧損」。

     ![按兩下模組以新增註解](./media/create-experiment/add-comment.png)

1. 將[清除遺漏的資料][clean-missing-data]模組拖曳到實驗畫布，然後將其連線至[選取資料集中的資料行][select-columns]模組。 在 [屬性] 窗格中，選取 [清除模式] 底下的 [移除整個資料列]。 這些選項會指示[清除遺漏的資料][clean-missing-data]藉由移除含任何遺漏值的資料列來清除資料。 按兩下模組，並輸入註解「移除遺漏值資料列」。

    ![將 [清除遺漏的資料] 的清除模式設為 [移除整個資料列]](./media/create-experiment/set-remove-entire-row.png)

1. 按一下頁面底部的 [執行]，以執行實驗。

    實驗執行完成時，所有模組都會呈現綠色核取標記，表示它們已順利完成。 同時也請留意位於右上角的 **執行完成** 狀態。

    ![執行後，實驗應該會顯示如下](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> 為什麼要立即執行實驗？ 藉由執行實驗，就會從資料集傳遞我們資料的資料行定義 (透過[選取資料集中的資料行][select-columns]模組，及透過[清除遺漏的資料][clean-missing-data]模組)。 這表示，我們連線到[清除遺漏的資料][clean-missing-data]的任何模組也會有相同的資訊。

現在我們已有清除過的資料。 若要檢視已清除的資料集，請按一下[清除遺漏的資料][clean-missing-data]模組的左側輸出連接埠，然後選取 [視覺化]。 請注意，此時已不包含 **自負虧損** 資料行，而且也沒有遺漏值。

現在我們已清除資料，而可以指定要在預測模型中使用哪些功能。

## <a name="define-features"></a>定義功能

在機器學習中，「特徵」是您感興趣的某項目的個別可測量屬性。 在我們的資料集中，每個資料列都代表一部汽車，而每個資料行是該汽車的功能。

要找到一組理想的功能來建立預測模型，必須針對您要解決的問題進行實驗，並具備相關知識。 有些功能會比其他功能更適合用來預測目標。 此外，某些特性與其他特性具有很高的相關性，可以移除。 例如，市區油耗和高速公路油耗密切相關，我們可以保留其中一項而移除另一項，這對預測不會有大幅影響。

我們將建置一個模型，並在其中使用我們資料集中的功能子集。 您稍後可以重新選取不同功能、再次執行實驗，並確認是否會有較理想的結果。 但是若要開始，讓我們試用下列功能︰

> make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. 將另一個[選取資料集中的資料行][select-columns]模組拖曳到實驗畫布。 將[清除遺漏資料][clean-missing-data]模組的左側輸出連接埠連線到[選取資料集中的資料行][select-columns]模組的輸入。

    ![將「選取資料集中的資料行」模組連線至「清除遺漏的資料」模組](./media/create-experiment/connect-clean-to-select.png)

1. 按兩下模組，並輸入「選取要預測的功能」。

1. 按一下 [屬性] 窗格中的 [啟動資料行選取器]。

1. 按一下 [套用規則] 。

1. 在 [開始於] 下，按一下 [無資料行]。 在 [篩選] 資料列中，選取 [包含] 和 [資料行名稱]，然後在文字方塊中選取資料行名稱的清單。 此篩選條件會指示模組不要傳遞我們未指定的任何資料行 (特性)。

1. 按一下核取記號 ([確定]) 按鈕。

    ![選取要包含在預測中的資料行 (特性)](./media/create-experiment/select-columns-to-include.png)

此模組會產生已篩選的資料集，其中只包含我們想要傳遞至下一個步驟供學習演算法使用的特性。 稍後您可以返回，並選取不同的功能重新嘗試。

## <a name="choose-and-apply-an-algorithm"></a>選擇及套用演算法

現在，資料已備妥，訓練和測試是建構預測模型的要素。 我們將使用我們的資料來訓練模型，然後測試模型，以檢驗它預測價格的精準度。
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*分類*和*迴歸*是兩種受監督的機器學習服務演算法。 「分類」可從一組已定義的類別預測答案，例如色彩 (紅色、藍色或綠色)。 「迴歸」可用來預測數字。

因為要預測價格，也就是一個數字，因此我們將使用迴歸演算法。 在此範例中，我們將使用*線性迴歸*模型。


我們藉由提供一組包含價格的資料來訓練模型。 模型會掃描的資料，然後尋找汽車功能與價格之間的關聯性。 然後，我們將測試模型 - 提供它一組我們熟悉的汽車功能，接著檢驗模型預測已知價格的精準度。

我們將資料分割成個別的訓練和測試資料集，用來訓練和測試模型。

1. 選取 [分割資料][split] 模組並拖曳到實驗畫布，然後將其連線到最後一個[選取資料集中的資料行][select-columns]模組。

1. 按一下[分割資料][split]模組以選取它。 尋找 [第一個輸出資料集中的資料列分數] \(在畫布右邊的 [屬性] 窗格)，並將它設為 0.75。 如此，我們將使用百分之 75 的資料來訓練模型，並保留百分之 25 做為測試之用。

    ![將「分割資料」模組的分割分數設為 0.75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > 變更 [ **隨機種子** ] 參數，可讓您為訓練和測試產生不同的隨機範例。 此參數會控制虛擬隨機數字產生器的植入。

1. 執行實驗。 執行實驗時，[選取資料集中的資料行][select-columns]和[分割資料][split]模組會將資料行定義傳遞至我們接下來要新增的模組。  

1. 若要選取學習演算法，請在畫布左側的模組調色盤中展開 [機器學習服務] 類別，然後展開 [初始化模型]。 這會顯示數個可用來初始化機器學習演算法的模組類別。 在此實驗中，請選取 [迴歸] 類別下的[線性迴歸][linear-regression]模組，然後將其拖曳到實驗畫布。 (您也可以在調色盤搜尋方塊中輸入「線性迴歸」以尋找模組。)

1. 找出[訓練模型][train-model]模組，並將其拖曳到實驗畫布。 將[線性迴歸][linear-regression]模組的輸出連線至[訓練模型][train-model]模組的左側輸入，並將[分割資料][split]模組的訓練資料輸出 (左側連接埠) 連線至[訓練模型][train-model]模組的右側輸入。

    ![將「訓練模型」模組連線至「線性迴歸」和「分割資料」模組](./media/create-experiment/connect-train-model.png)

1. 按一下[訓練模型][train-model]模組，按一下 [屬性] 窗格中的 [啟動資料行選取器]，然後選取 [價格] 資料行。 **價格**是我們的模型所將預測的值。

    在資料行選取器中選取 [價格] 資料行 (將它從 [可用的資料行] 清單 移到 [選取的資料行] 清單)。

    ![選取「訓練模型」模組的價格資料行](./media/create-experiment/select-price-column.png)

1. 執行實驗。

我們現在有經過訓練的迴歸模型可用來為新的汽車資料評分，以藉此預測價格。

![執行後，實驗此時應該會顯示如下](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>預測新的汽車價格

現在已完成使用百分之 75 資料模型的訓練，我們可以用它來為其他百分之 25 的資料評分，以了解模型的運作是否理想。

1. 找出[評分模型][score-model]模組，並將其拖曳到實驗畫布。 將[訓練模型][train-model]模組的輸出連線至[評分模型][score-model]的左側輸入連接埠。 將[分割資料][split]模組的測試資料輸出 (右側連接埠) 連線至[評分模型][score-model]的右側輸入連接埠。

    ![將「評分模型」模組連線至「訓練模型」和「分割資料」模組](./media/create-experiment/connect-score-model.png)

1. 執行實驗，然後按一下[評分模型][score-model]的輸出連接埠並選取 [視覺化]，以檢視[評分模型][score-model]模組的輸出。 輸出會顯示價格的預測值，以及來自測試資料的已知值。  

    ![「評分模型」模組的輸出](./media/create-experiment/score-model-output.png)

1. 最後，我們要測試結果的品質。 選取[評估模型][evaluate-model]模組，並將其拖曳至實驗畫布，然後將[評分模型][score-model]模組的輸出連線到[評估模型][evaluate-model]的左側輸入。 實驗最終應呈現如下：

    ![最終實驗](./media/create-experiment/complete-linear-regression-experiment.png)

1. 執行實驗。

若要檢視[評估模型][evaluate-model]模組的輸出，請按一下輸出連接埠，然後選取 [視覺化]。

![實驗的評估結果](./media/create-experiment/evaluation-results.png)

我們的模型會顯示下列統計資料：

- **平均絕對誤差** (MAE)：絕對誤差的平均值 (「誤差」是指預測值與實際值之間的差異)。
- **均方根誤差** (RMSE)：對測試資料集所做之預測的平方誤差的評分根平均值。
- **相對絕對誤差**：相對於實際值與所有實際值之平均值之間的絕對差異的絕對誤差平均值。
- **相對平方誤差**：相對於實際值與所有實際值之平均值之間的平方差異的平方誤差平均值。
- **決定係數**：也稱為 **R 平方值**，這是一個統計度量，可指出模型對於資料的適用程度。

針對每個誤差統計資料，越小越好。 值越小，表示預測越接近實際值。 就 [決定係數] 而言，其值愈接近一 (1.0)，預測就愈精準。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用範例資料集建立了簡單的實驗。 若要深入探索建立和部署模型的程序，請繼續進行預測解決方案教學課程。

> [!div class="nextstepaction"]
> [教學課程：在 Studio (傳統) 中開發預測性解決方案](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
