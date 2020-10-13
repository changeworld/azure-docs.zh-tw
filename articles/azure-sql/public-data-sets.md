---
title: 適用于 Azure 分析的公用資料集
description: 了解您可以用來建立原型及測試 Azure 分析服務和解決方案的公用資料集。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 30f6bf7629152050bc1249a1e22aff0aa30642de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209537"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>用於測試和建立原型的公用資料集
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

瀏覽這份公用資料集清單，找出您可以用來建立原型及測試儲存體和分析服務與解決方案的資料。

## <a name="us-government-and-agency-data"></a>美國政府和代理商資料

| 資料來源 | 關於資料 | 關於檔案 |
|---|---|---|
| [US Gov 資料](https://catalog.data.gov/dataset) | 250000以上的資料集，涵蓋了農業、氣候、消費者、生態系統、教育、能源、財務、健康、當地政府、製造業、maritime、海運、公共安全，以及美國的科學和研究 | 各種格式 (包括 HTML、XML、CSV、JSON、Excel 和其他許多格式)、各種大小的檔案。 您可以依照檔案格式篩選可用的資料集。 |
| [美國人口普查資料](https://www.census.gov/data.html) | 有關美國人口的統計資料 | 資料集採用各種格式。 |
| [來自 NASA 的地球科學資料](https://earthdata.nasa.gov/) | 超過 32,000 個資料集合，涵蓋農業、大氣、生物圈、氣候、冰雪圈、人類範圍、水圈、地表、海洋、太陽與地球互動等資料。 | 資料集採用各種格式。 |
| [航班延遲和其他運輸資料](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | 「運輸的美國 (點) 的運輸統計局 (BTS) 會追蹤由大型空氣電訊商營運的國內航班的即時效能。 這個網站上公布的摘要表會顯示準時、延遲、取消和轉降航班數目的摘要資訊。」 | 檔案採用 CSV 格式。 |
| [交通事故死亡率 - 美國事故死亡率分析報告系統 (FARS)](https://www.nhtsa.gov/FARS) | 「FARS 是一項全國人口普查，提供 NHTSA、美國國會和美國大眾有關在汽機車交通碰撞中遭受嚴重傷害的年度資料。」 | 「使用 FARS 查詢系統建立自己線上執行的事故死亡率資料。 或從 FTP 網站下載自 1975 年起的所有 FARS 資料。」 |
| [有毒化學資料-EPA 毒性預測器 (ToxCast &trade;) 資料](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | 「EPA 針對數千種化學物質所提供之可公開取得的最新高通量毒性資料。 這項資料是透過 EPA 的 ToxCast 研究技術產生。」 | 資料集是以各種格式提供，包括試算表、R 套件和 MySQL 資料庫檔案。 |
| [有毒化學物質資料 - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21) | 「2014 Tox21 Data Challenge 主要是為了幫助科學家了解經由「21 世紀毒理學 (Toxicology in the 21st Century)」計畫測試之化學物質和化合物的潛能，進而以可能導致毒效應的方式來擾亂生物路徑。」 | 資料集是以 SMILES 和 SDF 格式提供。 此資料提供「檢驗活動資料和多達 10,000 種化合物之 Tox21 集合 (Tox21 10k) 的化學結構。」 |
| [來自 NCBI 的生物技術和基因體資料](https://www.ncbi.nlm.nih.gov/guide/data-software/) | 涵蓋基因、基因體和蛋白質的多個資料集。 | 資料集採用文字、XML、BLAST 和其他格式。 可以使用 BLAST 應用程式。 |

## <a name="other-statistical-and-scientific-data"></a>其他統計和科學資料

| 資料來源 | 關於資料 | 關於檔案 |
|---|---|---|
| [紐約市計程車資料](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | 「計程車車程記錄的欄位會擷取上車和下車日期/時間、上車和下車地點、車程距離、車資明細、費率類型、付款類型和司機回報的乘客數。」 | 資料集依月份放在 CSV 檔案中。 |
| [Microsoft Research 資料集 -「研究用的資料科學」](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | 多個資料集，涵蓋人機互動、音訊/視訊、資料採礦/資訊擷取、地理空間/位置、自然語言處理和機器人/電腦視覺。 | 資料集採用各種格式 (經過壓縮以供下載)。 |
| [Open Science Data Cloud 資料](https://www.opensciencedatacloud.org/projects/) | 「Open Science Data Cloud 為科學界提供用於儲存、共用及分析 TB 和 PB 規模科學資料集的資源」。| 資料集採用各種格式。 |
| [全球氣候資料 - WorldClim](https://worldclim.org/) | 「WorldClim 是一組全球氣候圖層 (格狀氣候資料)，包含大約 1 平方公里的空間解析。 這些資料可用於對應和空間模擬。」 | 這些檔案包含地理空間資料。 如需詳細資訊，請參閱[資料格式](https://worldclim.org/formats1)。 |
| [人類社會相關資料 - GDELT 專案](https://www.gdeltproject.org/data.html) | 「GDELT 專案是人類社會曾建立過的最大、最詳盡且最高解析度的開放資料庫。」 | 未經處理的資料檔採用 CSV 格式。 |
| [來自 Criteo 且可供機器學習的廣告點擊預測資料](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | 「曾公開發行的最大 ML 資料集。」 如需詳細資訊，請參閱 [Criteo 的 1 TB 點擊預測資料集](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/)。 | |
| [來自 Lemur 專案的 ClueWeb09 文字採礦資料集](https://www.lemurproject.org/clueweb09.php/) | 「已建立 ClueWeb09 資料集，以支援資訊擷取和相關人類語言技術的研究。 其中包含在 2009 年 1 月和 2 月以 10 種語言收集的大約 10 億個網頁。」 | 請參閱[資料集資訊](https://www.lemurproject.org/clueweb09/datasetInformation.php)。|

## <a name="online-service-data"></a>線上服務資料

| 資料來源 | 關於資料 | 關於檔案 |
|---|---|---|
| [GitHub 封存](https://www.githubarchive.org/) | 「GitHub 封存是一個專案，可記錄 [事件] 公用 GitHub 時間表，加以封存，並讓它可輕鬆存取以供進一步分析。」 | 從 Web 用戶端下載 .gz (Gzip) 格式的 JSON 編碼事件封存。 |
| [來自 GHTorrent 專案的 GitHub 活動資料](http://ghtorrent.org/) | 「GHTorrent 專案 [是] 建立透過 GitHub REST API 提供之資料的可調整、可查詢、離線鏡像的成果。 GHTorrent 會監視 GitHub 公用事件時間表。 對於每個事件，它會詳盡地擷取其內容及其相依性。」 | MySQL 資料庫傾印採用 CSV 格式。 |
| [Stack Overflow 資料傾印](https://archive.org/details/stackexchange) | 「這是 Stack Exchange 網路 [包括 Stack Overflow] 上所有使用者貢獻之內容的匿名傾印。」 | "每個網站 [例如 Stack Overflow] 會格式為個別的封存，而此封存是由透過使用 bzip2 壓縮的 7-zip 進行壓縮的 XML 檔案所組成。 每個網站封存都包含貼文、使用者、投票、註解、PostHistory 和 PostLinks。」 |
