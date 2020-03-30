---
title: 從 Excel 遷移分析
titleSuffix: ML Studio (classic) - Azure
description: Excel 和 Azure 機器學習工作室中的線性回歸模型的比較（經典）
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217802"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>將分析從 Excel 遷移到 Azure 機器學習工作室（經典）

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *凱特·巴羅尼*和*本·博曼*是微軟卓越資料洞察中心的企業解決方案架構師。 在本文中，他們描述了他們使用 Azure 機器學習工作室（經典）將現有迴歸分析套件遷移到基於雲的解決方案的經驗。

## <a name="goal"></a>目標

我們的專案開始時有兩個目標： 

1. 使用預測分析提高我們組織每月收入預測的準確性 
2. 使用 Azure 機器學習工作室（經典版）確認、優化、提高速度和擴展結果。 

與許多企業一樣，我們的組織會每個月都會經歷營收預測程序。 我們的小型商務分析師團隊的任務是使用 Azure 機器學習工作室（經典版）來支援流程並提高預測準確性。 小組花費數個月收集多個來源的資料，並透過可識別與服務銷售預測相關的索引鍵屬性的統計分析，來執行資料屬性。 下一步是要開始在 Excel 中建立為資料建立統計迴歸模型的原型。 在幾週內我們便有了 Excel 迴歸模型，其效果優於目前的欄位和財務預測程序。 這也成為預測結果的比較基準。 

然後，我們採取了下一步，將預測分析轉移到 Studio（經典），以瞭解如何提高預測性能。

## <a name="achieving-predictive-performance-parity"></a>達成預測效能同位檢查
我們的首要任務是實現 Studio（經典）和 Excel 回歸模型之間的同位。 給定相同的資料，以及培訓和測試資料的相同拆分，我們希望在 Excel 和 Studio（經典）之間實現預測性能同位。 一開始我們失敗了。 Excel 模型優於 Studio（經典）模型。 失敗的原因是對 Studio 中的基本工具設置缺乏瞭解（經典）。 在與 Studio（經典）產品團隊同步後，我們更好地瞭解了資料集所需的基本設置，並實現了兩種型號之間的同位。 

### <a name="create-regression-model-in-excel"></a>在 Excel 中建立迴歸模型
我們的 Excel 迴歸使用可在 Excel 分析工具箱找到的標準的線性迴歸模型。 

我們計算 *平均絕對 %錯誤* ，並用它做為模型的效能量值。 大約花費 3 個月來達成使用 Excel 的工作模型。 我們把大部分學習帶到了Studio（經典）實驗中，最終對理解要求是有益的。

### <a name="create-comparable-experiment-in-studio-classic"></a>在工作室中創建可比較的實驗（經典）
我們按照以下步驟在 Studio 中創建實驗（經典）： 

1. 將資料集作為 csv 檔上載到 Studio（經典）（非常小的檔）
2. 建立新的實驗，並使用[選取資料集中的資料行][select-columns]模組來選取 Excel 中所使用的相同資料特徵 
3. 使用[資料分割][split]模組 (與「相對運算式」** 模式)，將資料分割成完全如同 Excel 中產生的相同訓練資料集 
4. 使用[線性迴歸][linear-regression]模組進行實驗 (只使用預設選項)、記載，並將結果與我們的 Excel 迴歸模型相互比較

### <a name="review-initial-results"></a>檢閱初步結果
首先，Excel 模型明顯優於 Studio（經典）模型： 

|  | Excel | Studio (傳統) |
| --- |:---:|:---:|
| 效能 | | |
| <ul style="list-style-type: none;"><li>調整 R 平方</li></ul> |0.96 |N/A |
| <ul style="list-style-type: none;"><li>決定 <br />係數</li></ul> |N/A |0.78<br />(低度精確度) |
| 平均絕對誤差 |$9.5M |$ 19.4M |
| 平均絕對誤差 (%) |6.03% |12.2% |

當我們向 Machine Learning 小組的開發人員和資料科學家執行我們的程序和結果時，他們快速提供一些實用的秘訣。 

* 當您在 Studio 中使用[線性回歸][linear-regression]模組（經典）時，提供兩種方法：
  * 線上梯度下降：可能比較適合較大規模的問題
  * 一般最小平方：這是大多數人聽到線性迴歸時會想到的方法。 對於小型資料集，一般最小平方是較佳的選擇。
* 考慮調整 L2 正規化加權參數，以改善效能。 它預設設定為 0.001，但對我們的小型資料集，請將它設定為 0.005 以改善效能。 

### <a name="mystery-solved"></a>謎題解開了！
應用建議時，我們在 Studio（經典）中實現了與 Excel 相同的基準性能： 

|  | Excel | 工作室（經典） （初始） | 工作室（經典）帶最小方塊 |
| --- |:---:|:---:|:---:|
| 加上標籤的值 |實際值 (數值) |相同 |相同 |
| 學習模組 |Excel -> 資料分析 -> 迴歸 |線性迴歸。 |線性迴歸 |
| 學習模組選項 |N/A |Defaults |普通最小平方<br />L2 = 0.005 |
| 資料集 |26 個資料列，3 個功能，1 個標籤。 全部數值。 |相同 |相同 |
| 分割：訓練 |Excel 會在前 18 個資料列上訓練，在最後 8 個資料列上測試。 |相同 |相同 |
| 分割：測試 |Excel 迴歸公式會套用至最後 8 個資料列 |相同 |相同 |
| **效能** | | | |
| 調整 R 平方 |0.96 |N/A | |
| 決定係數 |N/A |0.78 |0.952049 |
| 平均絕對誤差 |$9.5M |$ 19.4M |$9.5M |
| 平均絕對誤差 (%) |<span style="background-color: 00FF00;">6.03%</span> |12.2% |<span style="background-color: 00FF00;">6.03%</span> |

此外，Excel 係數相較與 Azure 訓練模型中的功能加權不相上下：

|  | Excel 係數 | Azure 功能加權 |
| --- |:---:|:---:|
| 截距/偏差 |19470209.88 |19328500 |
| 功能 A |0.832653063 |0.834156 |
| 功能 B |11071967.08 |11007300 |
| 功能 C |25383318.09 |25140800 |

## <a name="next-steps"></a>後續步驟
我們想要在 Excel 內使用 Machine Learning Web 服務。 我們的商務分析師依賴 Excel，且我們需要方法來呼叫 Machine Learning Web 服務與一列 Excel 資料列，讓它將預測值傳回 Excel。 

我們還希望使用 Studio 中提供的選項和演算法（經典）來優化我們的模型。

### <a name="integration-with-excel"></a>與 Excel 整合
我們的解決方案要使我們的 Machine Learning 迴歸模型作業化，方法是透過從訓練的模型建立 Web 服務。 在幾分鐘內，Web 服務即已建立，我們可以直接從 Excel 呼叫它，以傳回預測的收入值。 

*Web 服務儀表板* 一節包含可下載的 Excel 活頁簿。 活頁簿已使用 Web 服務 API 預先格式化並內嵌結構描述資訊。 按一下 [下載 Excel 活頁簿]** 開啟活頁簿，您可以將它儲存到本機電腦。 

![從 Web 服務儀表板下載 Excel 活頁簿](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

在活頁簿開啟時，請將預先定義的參數複製到藍色的 Parameter 區段，如下所示。 一旦輸入參數，Excel 即會對外呼叫 Machine Learning Web 服務，而會在綠色的預測值區段中顯示預測的計分標籤。 活頁簿將會針對在 Parameters 下輸入參數下的所有資料列項目，繼續根據您的訓練模型建立預測。 如需有關如何使用這項功能的詳細資訊，請參閱 [從 Excel 使用 Azure Machine Learning Web 服務](consuming-from-excel.md)。 

![範本 Excel 活頁簿連接到已部署的 Web 服務](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>最佳化及進一步實驗
現在我們已具備 Excel 模型的基準，我們可以進行最佳化 Machine Learning 線性迴歸模型。 我們使用[以篩選為基礎的特徵選取][filter-based-feature-selection]模組，改善我們選取的初始資料元素，有助於我們的效能提升達到平均絕對誤差 4.6%。 對於未來的專案，我們將使用此功能，這可以節省我們數周的時間反覆運算資料屬性，以找到用於建模的正確功能集。 

接下來，我們打算在實驗中納入其他演算法來比較效能，例如 [Bayesian][bayesian-linear-regression]或[推進式決策樹][boosted-decision-tree-regression]。 

如果您想要實驗迴歸，「能量效益迴歸」範例資料集即是可用來嘗試的良好的資料集，其中包含多個數值屬性。 資料集作為 Studio 中示例資料集的一部分提供（經典）。 您可以使用各種不同的學習模組，來預測加熱負載或冷卻負載。 下列圖表是針對目標變數冷卻負載預測的能源效率資料集所學習不同的迴歸的效能比較： 

| 模型 | 平均絕對誤差 | 均方根誤差 | 相對絕對誤差 | 相對平方誤差 | 決定係數 |
| --- | --- | --- | --- | --- | --- |
| 推進式決策樹 |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| 線性迴歸 (梯度下降) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| 類神經網路迴歸 |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| 線性迴歸 (一般最小平方) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>重要心得
通過並行運行 Excel 回歸和工作室（經典）實驗，我們學到了很多東西。 在 Excel 中創建基準模型並將其與使用機器學習[線性回歸][linear-regression]的模型進行比較有助於我們學習 Studio（經典），我們發現了改進資料選擇和模型性能的機會。 

我們也發現，最好使用[以篩選為基礎的特徵選取][filter-based-feature-selection]來加速未來的預測專案。 通過將要素選擇應用於資料，您可以在 Studio 中創建具有更好整體性能的改進模型（ 經典）。 

系統化將預測分析預測從 Studio（經典）傳輸到 Excel 的能力可顯著提高成功向廣大企業用戶群體提供結果的能力。 

## <a name="resources"></a>資源
以下是一些可幫助您處理迴歸的資源： 

* Excel 中的迴歸。 如果您從未嘗試過 Excel 中的回歸，本教程將使其變得簡單：[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* 迴歸與預測。 泰勒·切斯曼寫了一篇博客文章，解釋如何在Excel中進行時間序列預測，其中包含了一個很好的初學者對線性回歸的描述。 [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* 一般最小平方線性迴歸：缺點、問題和陷阱。 如需迴歸的簡介與討論：[https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

