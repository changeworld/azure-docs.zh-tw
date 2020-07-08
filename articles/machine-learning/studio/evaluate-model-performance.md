---
title: 評估 & 交叉驗證模型
titleSuffix: ML Studio (classic) - Azure
description: 瞭解您可用來監視 Azure Machine Learning Studio （傳統）中模型效能的計量。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: bd6c291cd703d56f86dc26c041eb39023bba0578
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945098"
---
# <a name="evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>評估 Azure Machine Learning Studio 中的模型效能（傳統）

在本文中，您可以瞭解可用來監視 Azure Machine Learning Studio （傳統）中模型效能的計量。  評估模型的效能是資料科學流程中的核心階段之一。 它會指出定型模型如何成功地為資料集評分 (預測)。 Azure Machine Learning Studio （傳統）支援透過兩個主要機器學習服務模組進行模型評估： 
+ [評估模型][evaluate-model] 
+ [交叉驗證模型][cross-validate-model]

這些模組可讓您根據 Machine Learning 和統計資料中常用的一些度量，查看您模型的運作方式。

評估模型應該與一起考慮：
+ [演算法的參數優化](algorithm-parameters-optimize.md)
+ [模型可解釋性](interpret-model-results.md)

提供三種常見的受監督的學習案例： 
* 迴歸
* 二進位分類 
* 多元分類


## <a name="evaluation-vs-cross-validation"></a>評估與交叉驗證
評估與交叉驗證是測量模型效能的標準方式。 它們都會產生您可以對照其他模型的度量檢查或比較的評估度量。

「[評估模型][evaluate-model]」需要計分資料集作為輸入（如果您想要比較兩個不同模型的效能，則為兩個）。 因此，您必須使用 [[定型模型][train-model]] 模組來訓練模型，並使用 [[評分模型][score-model]] 模組對某個資料集進行預測，然後才可以評估結果。 評估是以評分標籤/機率及真實性標籤為基礎，這些全都由[評分模型][score-model]模組來輸出。

或者，您可以使用交叉驗證，對不同的輸入資料子集自動執行一「訓練-評分-評估」作業 (10 次交疊)。 輸入資料會分割成 10 個部分，其中 1 個部分保留供測試之用，其他 9 個部分則供訓練之用。 此程序會重複 10 次，然後取得評估度量的平均值。 這有助於判斷將模型一般化為新資料集的程度。 [交叉驗證模型][cross-validate-model]模組接受非定型模型和一些標註的資料集，除了輸出平均結果，還會輸出各有 10 次交疊的評估結果。

在以下幾節中，我們將同時使用[評估模型][evaluate-model]和[交叉驗證模型][cross-validate-model]模組，建置簡單的迴歸和分類模型，並評估其效能。

## <a name="evaluating-a-regression-model"></a>評估迴歸模型
假設我們想要使用維度、動力、引擎規格等功能來預測汽車的價格。 這是典型的迴歸問題，其中的目標變數 (*價格*) 是連續的數值。 我們可以配合線性回歸模型，並指定特定汽車的功能值，以預測該汽車的價格。 這個迴歸模型可用來為我們所訓練的相同資料集評分。 當我們擁有預測的汽車價格之後，我們就可以查看預測與實際價格的平均偏離多少，以評估模型效能。 為了說明這一點，我們使用 Machine Learning Studio （傳統）中**儲存的資料集**區段提供的*汽車價格資料（原始）資料集*。

### <a name="creating-the-experiment"></a>建立實驗
將下列模組新增至您在 Azure Machine Learning Studio （傳統）中的工作區：

* 汽車價格資料 (原始)
* [線性迴歸][linear-regression]
* [訓練模型][train-model]
* [評分模型][score-model]
* [評估模型][evaluate-model]

如下圖 1 所示連接連接埠，並將[訓練模型][train-model]模組的 [標籤] 資料行設定為 [價格]**。

![評估迴歸模型](./media/evaluate-model-performance/1.png)

圖 1。 評估迴歸模型。

### <a name="inspecting-the-evaluation-results"></a>檢查評估結果
執行實驗之後，您可以按一下[評估模型][evaluate-model]模組的輸出連接埠，然後選取 [視覺化]** 以查看評估結果。 適用於迴歸模型的評估度量包括：「平均絕對誤差」**、「平均根絕對誤差」**、「相對絕對誤差」**、「相對平方誤差」** 和「決定係數」**。

「誤差」一詞代表預測的值和真正的值之間的差異。 系統通常會計算這項差異的絕對值或平方來擷取所有案例中的總誤差大小，因為在某些情況下，預測的值和真正的值之間的差異可能是負數。 誤差度量會根據預測與真正的值之間的平均偏差，測量迴歸模型的預測性效能。 誤差值越低，表示進行預測的模型越精確。 整體誤差度量為零時，表示模型完全符合資料。

決定係數亦稱為「R 平方值」，也是測量模型對於資料的適用程度的一種標準方式。 它可以解譯為模型所說明的變化的比例。 在此情況下，比例越高越好，其中 1 表示完全符合。

![線性迴歸評估度量](./media/evaluate-model-performance/2.png)

圖 2. 線性迴歸評估度量。

### <a name="using-cross-validation"></a>使用交叉驗證
如先前所述，您可以使用[交叉驗證模型][cross-validate-model]模組，自動執行重複的訓練、評分和評估。 在這種情況下，您只需要一個資料集、一個非定型模型，以及一個[交叉驗證模型][cross-validate-model]模組 (請參閱下圖)。 您必須在[交叉驗證模型][cross-validate-model]模組的屬性中，將 [標籤] 資料行設定為 [*價格*]。

![交叉驗證迴歸模型](./media/evaluate-model-performance/3.png)

圖 3. 交叉驗證迴歸模型。

執行實驗之後，您可以按一下[交叉驗證模型][cross-validate-model]模組右側的輸出連接埠，以查看評估結果。 這將為每個反覆項目 (交疊) 提供度量的詳細檢視，以及每個度量的平均結果 (圖 4)。

![迴歸模型的交叉驗證結果](./media/evaluate-model-performance/4.png)

圖 4. 迴歸模型的交叉驗證結果。

## <a name="evaluating-a-binary-classification-model"></a>評估二進位分類模型
例如，在二元分類的情況下，目標變數只有兩個可能的結果：{0, 1} 或 {false, true}, {negative, positive}。 假設您有一位成人員工的資料集，其中包含一些人口統計和雇用變數，而且系統要求您預測收入層級，也就是值為 {"<= 50 K"，">50 K"} 的二元變數。 換句話說，負數類別表示年收入低於或等於 5 萬元的員工，而正數類別則表示其他所有員工。 如同在迴歸情況下，我們會訓練模型、為某些資料評分，並評估結果。 此處的主要差異在於計量 Azure Machine Learning Studio （傳統）計算和輸出的選擇。 為了說明收入層級預測案例，我們將使用[成人](https://archive.ics.uci.edu/ml/datasets/Adult)資料集來建立 Studio （傳統）實驗，並評估雙類別羅吉斯回歸模型的效能，這是常用的二進位分類器。

### <a name="creating-the-experiment"></a>建立實驗
將下列模組新增至您在 Azure Machine Learning Studio （傳統）中的工作區：

* 成人收入普查二進位分類資料集
* [二元羅吉斯迴歸][two-class-logistic-regression]
* [訓練模型][train-model]
* [評分模型][score-model]
* [評估模型][evaluate-model]

如下圖 5 所示連接連接埠，並將[訓練模型][train-model]模組的 [標籤] 資料行設定為 [收入]**。

![評估二進位分類模型](./media/evaluate-model-performance/5.png)

圖 5. 評估二進位分類模型。

### <a name="inspecting-the-evaluation-results"></a>檢查評估結果
執行實驗之後，您可以按一下[評估模型][evaluate-model]模組的輸出連接埠，然後選取 [視覺化]** 以查看評估結果 (圖 7)。 可用於二元分類模型的評估度量包括：「正確性」**、「精確度」**、「回收」**、「F1 分數」** 和 AUC**。 此外，這個模組會輸出一個混淆矩陣，其中顯示真肯定、誤否定、誤肯定、真否定，以及 ROC**、「精確度/回收** 和「升力」** 曲線的數目。

準確性只是正確分類的案例的比例。 它通常是評估分類器時，您看到的第一個度量。 不過，當測試資料不對稱（其中大部分的實例都屬於其中一個類別），或您對其中一個類別的效能更有興趣時，精確度並不會真正捕捉到分類器的效果。 在收入層級的分類案例中，假設您要測試的特定資料中，99% 的案例代表年收入少於或等於 5 萬元的人。 藉由預測所有實例的 "<= 50K" 類別，可以達到0.99 準確度。 在此案例中，此分類器整體而言似乎做得不錯，但事實上，它無法正確分類任何高收入的個人 (1%)。

因此，最好先計算可擷取更明確的評估層面的其他度量。 在進入這類度量的細節之前，最好先了解二進位分類評估的混淆矩陣。 定型集中的類別標籤只能接受兩個可能的值，我們通常會將其稱為正面或負面。 分類器正確預測的正和負案例分別稱為真肯定 (TP) 和真否定 (TN)。 同樣地，分類不正確的案例稱為誤肯定 (FP) 和誤否定 (FN)。 混淆矩陣只是一個資料表，其中顯示落在這四個類別中每一個的實例數目。 Azure Machine Learning Studio （傳統）會自動決定資料集的兩個類別中哪一個是正類別。 如果類別標籤是布林值或整數，則標示為實例的 ' true ' 或 ' 1 ' 會被指派正類別。 如果標籤為字串（例如使用收入資料集），則標籤會依字母順序排序，而第一個層級會被選擇為負類別，而第二個層級是正類別。

![二元分類混淆矩陣](./media/evaluate-model-performance/6a.png)

圖 6. 二進位分類混淆矩陣。

回到收入分類問題，我們會想要提出幾個評估問題，幫助我們了解所使用的分類器的效能。 自然的問題是：「模型預測要賺取的個人 >50 K （TP + FP），有多少分類正確（TP）？」 」 透過查看模型的 **精確度** ，也就是正確分類的正數比例，可以回答這個問題：TP/(TP+FP)。 另一個常見的問題是「收入 >50k （TP + FN）的所有高收益員工，分類器正確分類的次數（TP）」。 」實際上，這是 **回收**，或真肯定比率：分類器的 TP/(TP+FN)。 您可能會注意到在精確度與回收之間有明顯的取捨。 例如，假設是相當對稱資料集，預測大部分是正案例的分類器會有高回收，但是因為許多負案例分類錯誤造成的精確度低會導致大量的誤肯定。 若要查看這兩個度量如何變化的繪圖，您可以按一下 [評估結果輸出] 頁面中的**精確度/回收**曲線 (圖 7 的左側部分)。

![二元分類評估結果](./media/evaluate-model-performance/7.png)

圖 7. 二進位分類評估結果。

常用的另一個相關度量是 **F1 分數**，這會將精確度和回收同時列入考量。 這是這兩個計量的調和平均值，其計算方式如下： F1 = 2 （精確度 x 召回率）/（精確度 + 召回率）。 F1 分數是在單一數位中總結評估的好方法，但最好同時查看精確度和回收，以進一步瞭解分類器的行為。

此外，您還可以在**受測者操作特徵 (ROC)** 曲線及對應的**曲線下面積 (AUC)** 值中，查看真肯定比率與誤肯定比率的比較。 此曲線越接近左上角，分類器的效能就愈好（也就是將真正的正面速率最大化，同時將錯誤的正向降至最低）。 接近繪圖對角線的曲線是因為分類器想要進行接近隨機猜測的預測所造成。

### <a name="using-cross-validation"></a>使用交叉驗證
如同在回歸範例中，我們可以執行交叉驗證，以自動反復定型、評分和評估不同的資料子集。 同樣地，我們可以使用[交叉驗證模型][cross-validate-model]模組、非定型的羅吉斯迴歸模型和資料集。 [標籤] 資料行必須在[交叉驗證模型][cross-validate-model]模組的屬性中設定為 [*收入*]。 執行實驗並按一下[交叉驗證模型][cross-validate-model]模組右側的輸出連接埠之後，除了每次交疊的平均值和標準差，我們還可以看到每次交疊的二元分類度量值。 

![交叉驗證二元分類模型](./media/evaluate-model-performance/8.png)

圖 8. 交叉驗證二進位分類模型。

![二元分類器的交叉驗證結果](./media/evaluate-model-performance/9.png)

圖 9. 二進位分類器的交叉驗證結果。

## <a name="evaluating-a-multiclass-classification-model"></a>評估多元分類模型
在此實驗中，我們將使用熱門的[鳶尾花](https://archive.ics.uci.edu/ml/datasets/Iris "鳶尾花")資料集，其中包含鳶尾花工廠的三種不同類型（類別）的實例。 每個實例都有四個功能值（萼片長度/寬度和花瓣長度/寬度）。 在先前的實驗中，我們使用相同的資料集來定型和測試模型。 在這裡，我們將使用[分割資料][split]模組來建立兩個數據子集、先進行定型，然後在第二個分數和評估。 [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.html) 上公開提供鳶尾資料集，可透過[匯入資料][import-data]模組來下載。

### <a name="creating-the-experiment"></a>建立實驗
將下列模組新增至您在 Azure Machine Learning Studio （傳統）中的工作區：

* [匯入資料][import-data]
* [多元決策樹系][multiclass-decision-forest]
* [資料分割][split]
* [訓練模型][train-model]
* [評分模型][score-model]
* [評估模型][evaluate-model]

連接連接埠，如以下圖 10 中所示。

將[訓練模型][train-model]模組的 [標籤] 資料行索引設定為 5。 此資料集沒有標頭資料列，但是我們知道類別標籤位於第五個資料行中。

按一下 [匯入資料][import-data] 模組，並將 [資料來源]** 屬性設定為 [透過 HTTP 的 Web URL]**，並將 *URL* 設為 http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data。

在[分割資料][split]模組中，設定要用於訓練的執行個體分數 (例如 0.7)。

![評估多元分類器](./media/evaluate-model-performance/10.png)

圖 10. 評估多元分類器

### <a name="inspecting-the-evaluation-results"></a>檢查評估結果
執行實驗，然後按一下[評估模型][evaluate-model]的輸出連接埠。 在此案例中，評估結果會以混淆矩陣的形式呈現。 矩陣會顯示所有三個類別的實際與預測實例。

![多元分類評估結果](./media/evaluate-model-performance/11.png)

圖 11. 多元分類評估結果。

### <a name="using-cross-validation"></a>使用交叉驗證
如先前所述，您可以使用[交叉驗證模型][cross-validate-model]模組，自動執行重複的訓練、評分和評估。 您需要一個資料集、一個非定型模型，以及一個[交叉驗證模型][cross-validate-model]模組 (請參閱下圖)。 同樣地，您必須設定[交叉驗證模型][cross-validate-model]模組的 [標籤] 資料行 (在本例中為資料行索引 5)。 執行實驗並按一下[交叉驗證模型][cross-validate-model]右側的輸出連接埠之後，您可以查看每次交疊的度量值，以及平均值和標準差。 在此顯示的度量類似於在二進位分類案例中討論的度量。 不過，在多元分類中，計算真肯定/否定和誤報/否定是藉由計算每個類別的方式來完成，因為沒有整體的正或負類別。 例如，在計算 ' 鳶尾花-iris-setosa ' 類別的精確度或召回時，會假設這是正類別，而其他專案則是負值。

![交叉驗證多元分類模型](./media/evaluate-model-performance/12.png)

圖 12. 交叉驗證多元分類模型。

![多元分類模型的交叉驗證結果](./media/evaluate-model-performance/13.png)

圖 13. 多元分類模型的交叉驗證結果。

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
