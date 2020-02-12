---
title: 從 Excel 遷移分析
titleSuffix: ML Studio (classic) - Azure
description: Excel 和 Azure Machine Learning Studio 中線性回歸模型的比較（傳統）
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 6976f0bb671b94f71b71287483c2ab88d0959899
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152766"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>將分析從 Excel 遷移至 Azure Machine Learning Studio （傳統）

> *Kate Baroni* 和 *Ben Boatman* 是 Microsoft 的 Data Insights Center of Excellence 的企業解決方案架構設計人員。 在本文中，他們會說明他們使用 Azure Machine Learning Studio （傳統）將現有迴歸分析套件遷移至雲端式解決方案的經驗。

## <a name="goal"></a>目標

我們的專案開始時有兩個目標： 

1. 使用預測分析來改善我們組織每月營收預測的準確度 
2. 使用 Azure Machine Learning Studio （傳統）來確認、優化、提高結果的速度和規模。 

與許多企業一樣，我們的組織會每個月都會經歷營收預測程序。 我們的商務分析師小組會負責使用 Azure Machine Learning Studio （傳統）來支援程式並改善預測準確度。 小組花費數個月收集多個來源的資料，並透過可識別與服務銷售預測相關的索引鍵屬性的統計分析，來執行資料屬性。 下一步是要開始在 Excel 中建立為資料建立統計迴歸模型的原型。 在幾週內我們便有了 Excel 迴歸模型，其效果優於目前的欄位和財務預測程序。 這也成為預測結果的比較基準。 

接著，我們會進行下一個步驟，將預測分析移至 Studio （傳統），以瞭解 Studio （傳統）如何改善預測效能。

## <a name="achieving-predictive-performance-parity"></a>達成預測效能同位檢查
我們的第一優先是達到 Studio （傳統）與 Excel 回歸模型之間的同位檢查。 假設有相同的資料，以及用於定型和測試資料的相同分割，我們想要在 Excel 和 Studio （傳統）之間達到預測效能同位檢查。 一開始我們失敗了。 Excel model 效能勝過 Studio （傳統）模型。 失敗的原因是缺乏瞭解 Studio （傳統）中的基本工具設定。 與 Studio （傳統）產品小組同步之後，我們對資料集所需的基本設定有更好的瞭解，並達成兩個模型之間的同位檢查。 

### <a name="create-regression-model-in-excel"></a>在 Excel 中建立迴歸模型
我們的 Excel 迴歸使用可在 Excel 分析工具箱找到的標準的線性迴歸模型。 

我們計算 *平均絕對 %錯誤* ，並用它做為模型的效能量值。 大約花費 3 個月來達成使用 Excel 的工作模型。 我們將許多學習到 Studio （傳統）實驗中，最後對了解需求有説明。

### <a name="create-comparable-experiment-in-studio-classic"></a>在 Studio 中建立可比較的實驗（傳統）
我們遵循下列步驟，在 Studio （傳統）中建立實驗： 

1. 將資料集以 csv 檔案的形式上傳至 Studio （傳統）（非常小的檔案）
2. 建立新的實驗，並使用 [[選取資料集中][select-columns]的資料行] 模組來選取 Excel 中使用的相同資料功能 
3. 使用[分割資料][split]模組（使用*相對運算式*模式），將資料分割成與 Excel 中已完成的相同訓練資料集 
4. 具有[線性回歸][linear-regression]模組的實驗（僅限預設選項）、已記載，並會將結果與我們的 Excel 回歸模型進行比較

### <a name="review-initial-results"></a>檢閱初步結果
一開始，Excel 模型會清楚地效能勝過 Studio （傳統）模型： 

|  | Excel | Studio (傳統) |
| --- |:---:|:---:|
| 效能 | | |
| <ul style="list-style-type: none;"><li>調整 R 平方</li></ul> |0.96 |N/A |
| <ul style="list-style-type: none;"><li>決定 <br />係數</li></ul> |N/A |0.78<br />(低度精確度) |
| 平均絕對誤差 |$9.5M |$ 19.4M |
| 平均絕對誤差 (%) |6.03% |12.2% |

當我們向 Machine Learning 小組的開發人員和資料科學家執行我們的程序和結果時，他們快速提供一些實用的秘訣。 

* 當您使用 Studio （傳統）中的[線性回歸][linear-regression]模組時，會提供兩種方法：
  * 線上梯度下降：可能比較適合較大規模的問題
  * 一般最小平方：這是大多數人聽到線性迴歸時會想到的方法。 對於小型資料集，一般最小平方是較佳的選擇。
* 考慮調整 L2 正規化加權參數，以改善效能。 它預設設定為 0.001，但對我們的小型資料集，請將它設定為 0.005 以改善效能。 

### <a name="mystery-solved"></a>謎題解開了！
當我們套用建議時，我們在 Studio （傳統）中達到與 Excel 相同的基準效能： 

|  | Excel | Studio （傳統）（初始） | Studio （傳統） w/最小平方 |
| --- |:---:|:---:|:---:|
| 加上標籤的值 |實際值 (數值) |相同 |相同 |
| 學習模組 |Excel -> 資料分析 -> 迴歸 |線性迴歸。 |線性迴歸 |
| 學習模組選項 |N/A |預設值 |普通最小平方<br />L2 = 0.005 |
| 資料集 |26 個資料列，3 個功能，1 個標籤。 全部數值。 |相同 |相同 |
| 分割：訓練 |Excel 會在前 18 個資料列上訓練，在最後 8 個資料列上測試。 |相同 |相同 |
| 分割：測試 |Excel 迴歸公式會套用至最後 8 個資料列 |相同 |相同 |
| **效能** | | | |
| 調整 R 平方 |0.96 |N/A | |
| 決定係數 |N/A |0.78 |0.952049 |
| 平均絕對誤差 |$9.5M |$ 19.4M |$9.5M |
| 平均絕對誤差 (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

此外，Excel 係數相較與 Azure 訓練模型中的功能加權不相上下：

|  | Excel 係數 | Azure 功能加權 |
| --- |:---:|:---:|
| 截距/偏差 |19470209.88 |19328500 |
| 功能 A |0.832653063 |0.834156 |
| 功能 B |11071967.08 |11007300 |
| 功能 C |25383318.09 |25140800 |

## <a name="next-steps"></a>後續步驟
我們想要在 Excel 內使用 Machine Learning Web 服務。 我們的商務分析師依賴 Excel，且我們需要方法來呼叫 Machine Learning Web 服務與一列 Excel 資料列，讓它將預測值傳回 Excel。 

我們也想要使用 Studio （傳統）中提供的選項和演算法來優化我們的模型。

### <a name="integration-with-excel"></a>與 Excel 整合
我們的解決方案要使我們的 Machine Learning 迴歸模型作業化，方法是透過從訓練的模型建立 Web 服務。 在幾分鐘內，Web 服務即已建立，我們可以直接從 Excel 呼叫它，以傳回預測的收入值。 

*Web 服務儀表板* 一節包含可下載的 Excel 活頁簿。 活頁簿已使用 Web 服務 API 預先格式化並內嵌結構描述資訊。 按一下 [下載 Excel 活頁簿] 開啟活頁簿，您可以將它儲存到本機電腦。 

![從 Web 服務儀表板下載 Excel 活頁簿](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

在活頁簿開啟時，請將預先定義的參數複製到藍色的 Parameter 區段，如下所示。 一旦輸入參數，Excel 即會對外呼叫 Machine Learning Web 服務，而會在綠色的預測值區段中顯示預測的計分標籤。 活頁簿將會針對在 Parameters 下輸入參數下的所有資料列項目，繼續根據您的訓練模型建立預測。 如需有關如何使用這項功能的詳細資訊，請參閱 [從 Excel 使用 Azure Machine Learning Web 服務](consuming-from-excel.md)。 

![連接至已部署 web 服務的範本 Excel 活頁簿](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>最佳化及進一步實驗
現在我們已具備 Excel 模型的基準，我們可以進行最佳化 Machine Learning 線性迴歸模型。 我們使用模組以[篩選器為基礎的特徵選取][filter-based-feature-selection]來改善我們選取的初始資料元素，並協助我們達到4.6% 平均絕對錯誤的效能改進。 在未來的專案中，我們將使用這項功能，可在反復查看資料屬性時節省數周的時間，以找出適合用來建立模型的功能集。 

接下來，我們打算在實驗中包含其他演算法，例如[貝氏][bayesian-linear-regression]或促進式[決策樹][boosted-decision-tree-regression]，以比較效能。 

如果您想要實驗迴歸，「能量效益迴歸」範例資料集即是可用來嘗試的良好的資料集，其中包含多個數值屬性。 資料集是以 Studio （傳統）中的範例資料集的一部分提供。 您可以使用各種不同的學習模組，來預測加熱負載或冷卻負載。 下列圖表是針對目標變數冷卻負載預測的能源效率資料集所學習不同的迴歸的效能比較： 

| 模型 | 平均絕對誤差 | 均方根誤差 | 相對絕對誤差 | 相對平方誤差 | 決定係數 |
| --- | --- | --- | --- | --- | --- |
| 推進式決策樹 |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| 線性迴歸 (梯度下降) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| 類神經網路迴歸 |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| 線性迴歸 (一般最小平方) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>重要心得
我們從並存執行 Excel 回歸和 Studio （傳統）實驗中學習了許多功能。 在 Excel 中建立基準模型並與使用 Machine Learning[線性回歸][linear-regression]的模型進行比較，可協助我們學習 Studio （傳統），並探索改善資料選取和模型效能的機會。 

我們也發現，建議使用以篩選為[基礎的特徵選取][filter-based-feature-selection]來加速未來的預測專案。 藉由將特徵選取套用至您的資料，您可以在 Studio （傳統）中建立改良的模型，以獲得更好的整體效能。 

將預測性分析預測從 Studio （傳統）轉移至 Excel 可的能力，可大幅增加成功將結果提供給廣大企業使用者物件的能力。 

## <a name="resources"></a>資源
以下是一些可幫助您處理迴歸的資源： 

* Excel 中的迴歸。 如果您從未嘗試過使用 Excel 進行迴歸，此教學課程可讓您輕鬆地完成：[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* 迴歸與預測。 Tyler Chessman 撰寫部落格文章，說明如何在 Excel 中執行時間序列預測，其中包含初學者適用的良好線性迴歸描述。 [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* 一般最小平方線性迴歸：缺點、問題和陷阱。 如需回歸的簡介和討論： [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

