---
title: 資料科學中的特徵工程設計 - Team Data Science Process
description: 了解在機器學習增強資料過程中的特徵工程及其角色。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperfq4
ms.openlocfilehash: 5e84a3930d350ec45cef7119342e3e4d2d5daaee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250652"
---
# <a name="feature-engineering-in-data-science"></a>資料科學特徵工程設計

在本文中，您將了解在機器學習中增強資料時的特徵工程及其角色。 從 [Azure Machine Learning Studio (傳統版)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) 實驗中取出的說明範例中學習。 

* **特徵設計**：從原始資料建立新特徵的程序，其可增加學習演算法的預測能力。 經過工程設計的特徵應該會擷取不容易出現在原始特徵集的其他資訊。
* **特徵選取**：選取主要特徵子集的程序，其可縮小定型問題的維度。

通常會先套用**特徵工程**以產生其他特徵，然後執行**特徵選取**以排除不相關、多餘或高度相關的特徵。

特徵工程和選取是 Team Data Science Process (TDSP) 的[模型化階段](lifecycle-modeling.md) 一部分。 若要深入了解 TDSP 和資料科學生命週期，請參閱[什麼是 TDSP？](overview.md)

## <a name="what-is-feature-engineering"></a>什麼是特徵工程？

定型資料是由包含資料列和資料行的矩陣所組成。 矩陣中的每個資料列都是觀察或記錄。 每個資料列的資料行都是描述每一筆記錄的特徵。 在實驗設計中指定的特徵應該會將資料中的模式特性化。

雖然許多原始資料欄位都可以直接用來定型模型，但通常必須為增強的定型資料集建立額外的 (工程) 特徵。

經過工程設計的特徵可增強定型，還會提供用以區分資料中模式的資訊。 但此程序是一種藝術。 健全且有建設性的決策通常需要網域專業知識。

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>範例 1：新增迴歸模型的暫時特徵

讓我們在 Azure Machine Learning Studio (傳統版) 中使用[單車租用的需求預測](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4)實驗，示範如何設計迴歸工作的特徵。 這項實驗的目標在於預測特定月份/日期/小時內單車租用的需求。

### <a name="bike-rental-dataset"></a>單車租用資料集

[單車租用 UCI 資料集](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/)是以實際資料為基礎，這些資料來自位於美國的單車公享公司。 此資料集代表 2011 年與 2012 年，一天中某個特定小時內的單車租用數量。 其中包含 17379 列和 17 欄。

原始特性集包含天氣條件 (溫度/溼度/風速) 和當天的類型 (假日/工作日)。 要預測的欄位為計數，代表特定小時內的單車租用數。 計數範圍從 1 到 977。

### <a name="create-a-feature-engineering-experiment"></a>建立特徵工程實驗

為了達到在定型資料中建構有效特性的目的，會使用相同的演算法建立四個各有不同定型資料集的迴歸模型， 這四個資料集代表相同的原始輸入資料，但設定的特性數量增加。 這些特性可分為四類：

1. A = 預測日的天氣 + 假日 + 工作日 + 週末特性
2. B = 過去的 12 小時以來，每小時租出的單車數量
3. C = 過去的 12 天以來，每天在同一個時間租出的單車數量
4. D = 過去的 12 週以來，在同一天同一個時間租出的單車數量

除了已存在於原先未經處理資料中的特徵集 A 以外，其他三個特徵集都是透過特徵工程設計程序來建立。 特徵集 B 會擷取最近的單車需求。 特性集 C 會擷取某一個小時的單車需求。 特性集 D 會擷取一週當中某一天某一個小時的單車需求。 四個定型資料集分別包含特性集 A、A+B、A+B+C 和 A+B+C+D。

### <a name="feature-engineering-using-studio-classic"></a>使用 SQL 函式設計特徵 (傳統版)

在 Studio (傳統版) 實驗中，這四個定型資料集是透過預先處理的輸入資料集中的分支形成。 除了最左邊的分支以外，每個分支都包含[執行 R 指令碼](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/)模組，衍生特徵 (特徵集 B、C 和 D) 是在此模組中建構並附加至匯入的資料集。

下圖示範左邊第二個分支中用來建立特性集 B 的 R 指令碼。

![建立特性](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>結果

下表彙總了四個模型的效能結果比較： 

![結果比較](./media/create-features/result1.png)

特性 A+B+C 所呈現的結果最理想。 當定型資料中包含其他特性集時，錯誤率會降低。 這證實了我們的推測：特徵集 B、C 會針對迴歸工作提供其他相關資訊。 但新增 D 特性似乎不會讓錯誤率降低。

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> 範例 2：建立文字採礦的特徵

特徵工程廣泛運用於文字採礦的相關工作，例如文件分類和情感分析。 因為個別的原始文字通常可作為輸入資料，所以建立文字/片語次數相關特性時需要特徵工程程序。

### <a name="feature-hashing"></a>特徵雜湊

為了達成此工作，會套用名為[特性雜湊](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/feature-hashing)的技術，有效地將任意文字特性變成索引。 此方法不會將每個文字特徵 (文字/片語) 關聯至特定索引，而是將雜湊函數套用至特徵，並直接使用其雜湊值作為索引。

Studio (傳統版) 中有一個[特徵雜湊](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/feature-hashing)模組，方便建立這些文字/片語特徵。 下圖顯示使用此模組的範例。 輸入資料集包含兩個資料行：1 至 5 的書籍評比，以及實際評論內容。 此模組的目標在於擷取一些新特性，以顯示特定書籍評論中對應文字/片語的發生次數。 若要使用此模組，請完成下列步驟：

* 第一步，選取包含輸入文字的資料行 (此例中的 "Col2")。
* 第二步，將 "Hashing bitsize" 設定為 8，表示將建立 2^8=256 個特性。 所有文字中的文字/片語會雜湊至 256 個索引。 "Hashing bitsize" 參數的範圍是 1 至 31。 如果將此值設定為較大的數字，文字/片語比較不可能雜湊至相同的索引。
* 第三步，將 "N-grams" 參數設定為 2。 此值可從輸入文字中取得 unigrams (適用於每一個文字的特徵) 和 bigrams (適用於每一對相鄰文字的特徵) 的發生次數。 "N-grams" 參數的範圍是 0 至 10，這表示要包含在一個特性中的循序文字數目上限。  

![「特性雜湊」模組](./media/create-features/feature-Hashing1.png)

下圖顯示這些新特徵的外觀。

![「特性雜湊」範例](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>結論
工程設計和選取的特徵可提高下列定型過程的效率：嘗試擷取資料中內含的重要資訊。 此外，還可改善這些模型的功效，正確地分類輸入資料以及更精確地預測感興趣的結果。

特性工程設計和選取也可結合起來，讓學習更易於以運算方式處理。 其作法是提高而後減少校正或定型模型所需的特性數量。 從數學的角度來看，選取的特徵是極小的一組獨立變數，可供解釋資料中的模式，然後成功地預測結果。

您不一定要執行特徵工程設計或特徵選取。 這取決於資料、選取的演算法，以及實驗的目標。

## <a name="next-steps"></a>後續步驟

若要在特定環境中建立資料功能，請參閱下列文章：

* [在 SQL Server 中建立資料的特性](create-features-sql-server.md)
* [針對使用 Hive 查詢之 Hadoop 叢集中的資料建立特徵](create-features-hive.md)