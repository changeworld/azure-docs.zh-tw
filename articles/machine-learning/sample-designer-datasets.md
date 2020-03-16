---
title: 在 Azure Machine Learning 設計工具中使用範例資料集
titleSuffix: Azure Machine Learning
description: 深入司解 Azure Machine Learning 設計工具中包含的範例資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037313"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>在 Azure Machine Learning 設計工具中使用範例資料集 (預覽)

在 Azure Machine Learning 設計工具 (預覽) 中建立新的管線時，預設會包含一些範例資料集。 這些範例資料集是由設計工具首頁中的範例管線所使用。 

範例資料集可在**資料集**-**範例**類別目錄下取得。 您可以在設計工具的畫布面板左側的模組選擇區中找到此項目。 您可以將資料集拖曳到您的畫布面板上，在自己的管線中使用任一資料集。

## <a name="datasets"></a>資料集


| 資料集&nbsp;名稱&nbsp;&nbsp;&nbsp;&nbsp;| 資料集說明 |
|-------------|:--------------------|
| 成人收入普查二進位分類資料集 | 1994 年普查資料的子集，使用年齡 16 歲以上的成年工作者，和 > 100 的調整收入指數。<br/>**使用量**：使用人口統計來將人口分類，以預測個人年收入是否超過 5 萬元。<br/> **相關研究**：Kohavi, R., Becker, B. (1996)。 [UCI Machine Learning 存放庫](https://archive.ics.uci.edu/ml)。 Irvine, CA:University of California, School of Information and Computer Science|
|汽車價格資料 (原始)|依構造和型號分類的汽車相關資訊，包括價格、性能 (例如汽缸數和油耗)，以及保險風險評分。<br/> 風險分數一開始與自動價格相關聯。 然後它會在精算師稱為符號化的程序中根據實際風險進行調整。 若值為 +3，表示該汽車屬於高風險，若值為 -3，表示大致而言很安全。<br/>**使用方式**：</b>使用迴歸或多變量分類，依特性預測風險評分。<br/>**相關研究**：</b>Schlimmer, J.C. (1987 年)。 [UCI Machine Learning 存放庫](https://archive.ics.uci.edu/ml)。 Irvine, CA:University of California, School of Information and Computer Science。 |
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

## <a name="next-steps"></a>後續步驟

* 透過以下教學課程，了解預測性分析和機器學習的基本概念：[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)來試用設計工具

* 了解如何修改現有的[設計工具範例](samples-designer.md)，以符合您的需求。
