---
title: 使用範例資料集
titleSuffix: Azure Machine Learning
description: Machine Learning 設計工具中包含的範例模型所使用之資料集的描述。 您可以將這些範例資料集用於管線。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165058"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>在 Azure Machine Learning 設計工具中使用範例資料集（預覽）

當您在 Azure Machine Learning 設計工具（預覽）中建立新管線時，預設會包含一些範例資料集。 其中許多範例資料集是由設計工具首頁中的範例模型所使用。 其他則是機器學習中經常使用的各種範例。

其中的部分資料集可在 Azure Blob 儲存體中使用。 下表提供這些資料集的直接連結。 您可以使用 [匯[入資料](./algorithm-module-reference/import-data.md)] 模組，在管線中使用這些資料集。

這些範例資料集的其餘部分可在 [**資料集**-**範例**] 類別之下取得。 您可以在設計工具畫布左邊的模組選擇區中找到此工具。 您可以將這些資料集拖曳到畫布上，以在自己的管線中使用其中任何一個。

## <a name="datasets"></a>資料集

<table>

<tr>
  <th>資料集名稱</th>
  <th>資料集說明</th>
</tr>

<tr>
  <td>成人收入普查二進位分類資料集</td>
  <td>
1994 年普查資料的子集，使用年齡 16 歲以上的成年工作者，和 > 100 的調整收入指數。
<p></p>
<b>使用方式：</b>使用人口統計來將人口分類，以預測個人年收入是否超過 5 萬元。
<p></p>
<b>相關研究：</b>Kohavi, R.、Becker, B. (1996 年)。 UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>汽車價格資料 (原始)</td>
  <td>
依構造和型號分類的汽車相關資訊，包括價格、性能 (例如汽缸數和油耗)，以及保險風險評分。
<p></p>
風險分數一開始與自動價格相關聯。 然後它會在精算師稱為符號化的程序中根據實際風險進行調整。 若值為 +3，表示該汽車屬於高風險，若值為 -3，表示大致而言很安全。
<p></p>
<b>使用方式：</b>使用迴歸或多變量分類，依特性預測風險評分。 
<p></p>
<b>相關研究：</b>Schlimmer, J.C. (1987 年)。 UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>


<tr>
  <td>共用 CRM Appetency 標籤</td>
  <td>
KDD Cup 2009 客戶關係預測挑戰 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>) 中的標籤。
  </td>
</tr>

<tr>
  <td>共用 CRM 流失標籤</td>
  <td>
KDD Cup 2009 客戶關係預測挑戰 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>) 中的標籤。
  </td>
</tr>

<tr>
  <td>共用 CRM 資料集</td>
  <td>
此資料來自 KDD Cup 2009 客戶關係預測挑戰 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>)。
<p></p>
資料集包含來自法國電信公司 Orange 的 50K 個客戶。 每個客戶都有 230 項不具名的特性，其中有 190 項數值特性和 40 項類別特性。 這些特性非常稀疏。
  </td>
</tr>

<tr>
  <td>共用 CRM 向上銷售標籤</td>
  <td>
KDD Cup 2009 客戶關係預測挑戰 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>) 中的標籤。
  </td>
</tr>

<tr>
  <td>航班誤點資料</td>
  <td>
乘客從美國運輸部門的 TranStats 資料收集（<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">準時</a>）取得的航班準時效能資料。
<p></p>
此資料集涵蓋的其間為 2013 年 4 月至 10 月。 在上傳至設計工具之前，資料集的處理方式如下：
<ul>
  <li>資料集已經過篩選，僅涵蓋美國大陸 70 個最繁忙的機場</li>
  <li>取消的航班已標示為誤點達 15 分鐘以上</li>
  <li>已篩選掉更改路徑的航班</li>
  <li>已選取下列資料行：Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>德國信用卡 UCI 資料集</td>
  <td>
UCI Statlog (德國信用卡) 資料集 (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>)，使用 german.data 檔案。
<p></p>
此資料集會將申請者 (以一組屬性說明) 分類為低或高信用風險。 每個範例代表一名申請者。 共有 20 項特性 (包括數值和類別) 以及一個二進位標籤 (信用風險值)。 高信用風險項目的標籤 = 2，低信用風險項目的標籤 = 1。 將低風險範例誤判為高風險的成本為 1，而將高風險範例誤判為低風險的成本為 5。
  </td>
</tr>

<tr>
  <td>IMDB 影片標題</td>
  <td>
資料集包含 Twitter 推文中分級影片的資訊：IMDB 影片識別碼、影片名稱、內容類型、製作年份。 資料集中有 1 萬 7 千部影片。 該資料集在論文 "S. Dooms, T. De Pessemier and L. Martens" 中推出。 MovieTweetings：收集自 Twitter 的影片分級資料集。 Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013."
  </td>
</tr>

<tr>
  <td>電影分級</td>
  <td>
資料集是 Movie Tweetings 資料集的擴充版本。 資料集包含 17 萬個影片的分級，擷取自 Twitter 上結構良好的推文。 每個實例都代表一個推文，而且是一個元組：使用者識別碼、IMDB 影片識別碼、評等、時間戳記、此推文的我的最愛數目，以及此推文的轉數目。 資料集是由 A. Said、S. Dooms、B. Loni 和 D. Tikk 為 Recommender Systems Challenge 2014 提供。
  </td>
</tr>


<tr>
  <td>天氣資料集</td>
  <td>
NOAA 提供的每小時起降天候觀測值 (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">將 201304 的資料合併至 201310</a>)。
<p></p>
天氣資料涵蓋從機場天候觀測站進行的觀測，涵蓋期間為 2013 年 4 月至 10 月。 在上傳至設計工具之前，資料集的處理方式如下：
<ul>
  <li>天候觀測站識別碼已對應至相對應的機場識別碼</li>
  <li>已篩選掉與 70 個最繁忙的機場沒有關聯的天候觀測站</li>
  <li>Date 資料行已分割為個別的 Year、Month 和 Day 資料行</li>
  <li>已選取下列資料行：AirportID、Year、Month、Day、Time、TimeZone、SkyCondition、Visibility、WeatherType、DryBulbFarenheit、DryBulbCelsius、WetBulbFarenheit、WetBulbCelsius、DewPointFarenheit、DewPointCelsius、RelativeHumidity、WindSpeed、WindDirection、ValueForWindCharacter、StationPressure、PressureTendency、PressureChange、SeaLevelPressure、RecordType、HourlyPrecip、Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 資料集</td>
  <td>
資料是從 Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) 上每家 S&P 500 公司的文章衍生而來 (儲存為 XML 資料)。
<p></p>
在上傳至設計工具之前，資料集的處理方式如下：
<ul>
  <li>擷取每家特定公司的文字內容</li>
  <li>移除 wiki 格式</li>
  <li>移除非英數字元</li>
  <li>將所有文字轉換為小寫</li>
  <li>新增了知名公司類別</li>
</ul>
<p></p>
請注意，對某些公司而言，找不到某篇文章，所以記錄筆數小於 500 筆。
  </td>
</tr>

</table>

## <a name="next-steps"></a>後續步驟

* 瞭解預測性分析和機器學習服務的基本概念[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)

* 使用匯[入資料](./algorithm-module-reference/import-data.md)模組匯入範例資料集
