---
title: 設計工具的範例管線和資料集
titleSuffix: Azure Machine Learning
description: 在 Azure Machine Learning 設計工具中使用範例，以快速了解您的機器學習管線。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 10/14/2020
ms.custom: designer
ms.openlocfilehash: ecdb011310d8aa5bd63e9b9e9679bb525cb4f7b3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074595"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Azure Machine Learning 設計工具的範例管線和資料集

使用 Azure Machine Learning 設計工具中的內建範例，快速開始建置您自己的機器學習管線。 Azure Machine Learning 設計工具 [GitHub 存放庫](https://github.com/Azure/MachineLearningDesigner)包含詳細的文件，可協助您了解一些常見的機器學習案例。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://aka.ms/AMLFree)
* Azure Machine Learning 工作區 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>使用範例管線

設計工具會將範例管線的複本儲存至您的工作室工作區。 您可以編輯管線以符合您的需求，並以您自己的方式加以儲存。 使用這些資訊開始進行專案。

以下是使用設計工具範例的方法：

1. 登入 <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>，並選取您要使用的工作區。

1. 選取 [設計工具]。

1. 在 [新增管線] 區段下，選取範例管線。

    如需完整的範例清單，請選取 [顯示更多範例]。

1. 若要執行管線，您必須先設定要在其上執行管線的預設計算目標。

   1. 在畫布右側的 [設定] 窗格中，選取 [選取計算目標]。

   1. 在出現的對話方塊中，選取現有計算目標或建立新的計算目標。 選取 [儲存]。

   1. 選取畫布頂端的 [提交] 以提交管線執行。

   視範例管線和計算設定而定，執行可能需要一些時間才能完成。 預設計算設定的最小節點大小為 0，這表示設計工具必須在閒置之後配置資源。 重複的管線執行花費較少的時間，因為已經配置計算資源。 此外，設計工具會針對每個模組使用快取的結果，進一步提升效率。


1. 在管線完成執行之後，您可以檢閱管線及檢視每個模組的輸出，以便深入了解。 請使用下列步驟來檢視模組輸出：

   1. 以滑鼠右鍵按一下要在畫布上看到輸出結果的模組。
   1. 選取 [視覺化]。


   針對一些最常見的機器學習案例，使用範例作為起點。

## <a name="regression"></a>迴歸

探索這些內建的迴歸範例。

| 範例標題 | 描述 | 
| --- | --- |
| [迴歸 - 汽車價格預測 (基本)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-basic.md) | 使用線性回歸來預測汽車價格。 |
| [迴歸 - 汽車價格預測 (進階)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md) | 使用決策樹系和推進式決策樹迴歸輸入變數來預測汽車價格。 比較這兩個模型找出最佳的演算法。

## <a name="classification"></a>分類

探索這些內建的分類範例。 您可以開啟範例並改為檢視模組註解，以深入了解沒有文件連結的範例。

| 範例標題 | 描述 | 
| --- | --- |
| [具有特徵選取的二元分類 - 收入預測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-feature-selection-income-prediction.md) | 使用二元推進式決策樹來預測收入的高或低。 使用皮爾森相關來選取功能。
| [具有自訂 Python 指令碼的二元分類 - 信用風險預測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-python-credit-prediction.md) | 將信用應用程式分類為高或低風險。 使用執行 Python 指令碼模組來為資料加權。
| [二元分類 - 客戶關係預測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-customer-relationship-prediction.md) | 使用二元推進式決策樹來預測客戶流失。 使用 SMOTE 來取樣偏差資料。
| [文字分類 - 維基百科 SP 500 資料集](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/text-classification-wiki.md) | 使用多元羅吉斯迴歸將維基百科文章的公司類型分類。 |
| 多元分類 - 字母辨識 | 建立完善的二元分類器將寫好的字母分類。 |

## <a name="computer-vision"></a>電腦視覺

探索這些內建的電腦視覺範例。 您可以開啟範例並改為檢視模組註解，以深入了解沒有文件連結的範例。

| 使用 DenseNet 進行影像分類 | 使用電腦視覺模組來建立以 PyTorch DenseNet 為基礎的影像分類模型。| 

## <a name="recommender"></a>推薦程式

探索這些內建的推薦程式範例。 您可以開啟範例並改為檢視模組註解，以深入了解沒有文件連結的範例。

| 範例標題 | 描述 | 
| --- | --- |
| 以 Wide & Deep 為基礎的建議 - 餐廳評等預測 | 從餐廳/使用者特徵和評等建立餐廳推薦程式引擎。|
| 建議 - 電影分級推文 | 根據電影/使用者特徵和評等建置電影推薦引擎。|

## <a name="utility"></a>公用程式

深入了解示範機器學習公用程式和功能的範例。 您可以開啟範例並改為檢視模組註解，以深入了解沒有文件連結的範例。

| 範例標題 | 描述 | 
| --- | --- |
| 使用 Vowpal Wabbit 模型的二元分類 - 成人收入預測 | Vowpal Wabbit 是一個機器學習系統，其會使用像是線上、雜湊，allreduce、簡化、learning2search、主動和互動式學習的技術，來擴展機器學習的應用。 此範例會示範如何使用 Vowpal Wabbit 模型來建立二元分類模型。 
| [使用自訂 R 指令碼 - 航班延遲預測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/r-script-flight-delay-prediction.md) | 使用自訂的 R 指令碼來預測排定的客機是否會誤點超過 15 分鐘。
| 二元分類的交叉驗證 - 成人收入預測 | 使用交叉驗證為成人收入建立二元分類器。
| 排列功能重要性 | 使用排列功能重要性來計算測試資料集的重要性分數。 
| 二元分類的參數調整 - 成人收入預測 | 使用調整模型超參數來尋找最佳的超參數，以建置二元分類器。 |

## <a name="datasets"></a>資料集

在 Azure Machine Learning 設計工具中建立新的管線時，預設會包含一些範例資料集。 這些範例資料集是由設計工具首頁中的範例管線所使用。 

範例資料集可在**資料集**-**範例**類別目錄下取得。 您可以在設計工具的畫布面板左側的模組選擇區中找到此項目。 您可以將資料集拖曳到您的畫布面板上，在自己的管線中使用任一資料集。

| 資料集&nbsp;名稱&nbsp;&nbsp;&nbsp;&nbsp;| 資料集說明 |
|-------------|:--------------------|
| 成人收入普查二進位分類資料集 | 1994 年普查資料的子集，使用年齡 16 歲以上的成年工作者，和 > 100 的調整收入指數。<br/>**使用量**：使用人口統計來將人口分類，以預測個人年收入是否超過 5 萬元。<br/> **相關研究**：Kohavi, R., Becker, B. (1996)。 [UCI Machine Learning 存放庫](https://archive.ics.uci.edu/ml)。 Irvine, CA:University of California, School of Information and Computer Science|
|汽車價格資料 (原始)|依構造和型號分類的汽車相關資訊，包括價格、性能 (例如汽缸數和油耗)，以及保險風險評分。<br/> 風險分數一開始與自動價格相關聯。 然後它會在精算師稱為符號化的程序中根據實際風險進行調整。 若值為 +3，表示該汽車屬於高風險，若值為 -3，表示大致而言很安全。<br/>**使用量**：使用迴歸或多變量分類，依特性預測風險評分。<br/>**相關研究**：Schlimmer, J.C. (1987 年)。 [UCI Machine Learning 存放庫](https://archive.ics.uci.edu/ml)。 Irvine, CA:University of California, School of Information and Computer Science。 |
| 共用 CRM Appetency 標籤 |KDD Cup 2009 客戶關係預測挑戰 ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)) 中的標籤。|
|共用 CRM 流失標籤|KDD Cup 2009 客戶關係預測挑戰 ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)) 中的標籤。|
|共用 CRM 資料集 | 此資料來自 KDD Cup 2009 客戶關係預測挑戰 ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip))。 <br/>資料集包含來自法國電信公司 Orange 的 50K 個客戶。 每個客戶都有 230 項不具名的特性，其中有 190 項數值特性和 40 項類別特性。 這些特性非常稀疏。 |
|共用 CRM 向上銷售標籤|KDD Cup 2009 客戶關係預測挑戰 ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)) 中的標籤|
|航班誤點資料|美國交通部收集的 TranStats 資料所包含的客機航班準點率資料([準點](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time))。<br/>此資料集涵蓋的其間為 2013 年 4 月至 10 月。 上傳至設計工具之前，資料集已經過下列處理： <br/>- 資料集已經過篩選，僅涵蓋美國大陸 70 個最繁忙的機場 <br/>- 取消的航班已標示為誤點達 15 分鐘以上 <br/>- 已篩選掉更改路徑的航班 <br/>- 已選取下列資料行：Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Canceled|
|德國信用卡 UCI 資料集|UCI Statlog (德國信用卡) 資料集 ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)))，使用 german.data 檔案。<br/>此資料集會將申請者 (以一組屬性說明) 分類為低或高信用風險。 每個範例代表一名申請者。 共有 20 項特性 (包括數值和類別) 以及一個二進位標籤 (信用風險值)。 高信用風險項目的標籤 = 2，低信用風險項目的標籤 = 1。 將低風險範例誤判為高風險的成本為 1，而將高風險範例誤判為低風險的成本為 5。|
|IMDB 影片標題|資料集包含 Twitter 推文中已評分之影片的資訊：IMDB 影片識別碼、影片名稱、內容類型及製作年份。 資料集中有 1 萬 7 千部影片。 該資料集在論文 "S. Dooms, T. De Pessemier and L. Martens" 中推出。 MovieTweetings：收集自 Twitter 的影片分級資料集。 Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013."|
|電影分級|資料集是 Movie Tweetings 資料集的擴充版本。 資料集包含 17 萬個影片的分級，擷取自 Twitter 上結構良好的推文。 每個執行個體代表推文，並且是 Tuple：使用者識別碼、IMDB 影片識別碼、分級、時間戳記、推文的收藏數，以及這個推文的轉推數。 資料集是由 A. Said、S. Dooms、B. Loni 和 D. Tikk 為 Recommender Systems Challenge 2014 提供。|
|天氣資料集|NOAA 提供的每小時起降天候觀測值 ([將 201304 的資料合併至 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv))。<br/>天氣資料涵蓋從機場天候觀測站進行的觀測，涵蓋期間為 2013 年 4 月至 10 月。 上傳至設計工具之前，資料集已經過下列處理：    <br/> - 天候觀測站識別碼已對應至相對應的機場識別碼    <br/> - 已篩選掉與 70 個最繁忙的機場沒有關聯的天候觀測站    <br/> - Date 資料行已分割為個別的 Year、Month 和 Day 資料行    <br/> - 已選取下列資料行：AirportID、Year、Month、Day、Time、TimeZone、SkyCondition、Visibility、WeatherType、DryBulbFarenheit、DryBulbCelsius、WetBulbFarenheit、WetBulbCelsius、DewPointFarenheit、DewPointCelsius、RelativeHumidity、WindSpeed、WindDirection、ValueForWindCharacter、StationPressure、PressureTendency、PressureChange、SeaLevelPressure、RecordType、HourlyPrecip、Altimeter|
|Wikipedia SP 500 資料集|資料是從 Wikipedia (https://www.wikipedia.org/) ) 上每家 S&P 500 公司的文章衍生而來 (儲存為 XML 資料)。    <br/>上傳至設計工具之前，資料集已經過下列處理：    <br/> - 擷取每家特定公司的文字內容    <br/> - 移除 wiki 格式    <br/> - 移除非英數字元    <br/> - 將所有文字轉換為小寫    <br/> - 新增了知名公司類別    <br/>請注意，對某些公司而言，找不到某篇文章，所以記錄筆數小於 500 筆。|
|餐廳特色資料| 一組關於餐廳及其特色的中繼資料，例如食物類型、用餐風格和地點等。 <br/>**使用量**：將此資料集與其他兩個餐廳資料集搭配使用，以將推薦系統定型並進行測試。<br/> **相關研究**：Bache, K. 和 Lichman, M.(2013 年)。 [UCI Machine Learning 存放庫](https://archive.ics.uci.edu/ml)。 Irvine, CA:University of California, School of Information and Computer Science。|
|餐廳評等| 包含使用者給予餐廳的評等，最低為 0，最高為 2。<br/>**使用量**：將此資料集與其他兩個餐廳資料集搭配使用，以將推薦系統定型並進行測試。 <br/>**相關研究**：Bache, K. 和 Lichman, M.(2013 年)。 [UCI Machine Learning 存放庫](https://archive.ics.uci.edu/ml)。 Irvine, CA:University of California, School of Information and Computer Science。|
|餐廳顧客資料| 一組關於顧客的中繼資料，包括人口統計和喜好。 <br/>**使用量**：將此資料集與其他兩個餐廳資料集搭配使用，以將推薦系統定型並進行測試。 <br/> **相關研究**：Bache, K. 和 Lichman, M.(2013 年)。 [UCI Machine Learning 存放庫](https://archive.ics.uci.edu/ml) Irvine, CA：University of California, School of Information and Computer Science。|

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

透過以下教學課程，了解預測性分析和機器學習的基礎概念：[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)來試用設計工具

