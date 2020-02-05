---
title: 設計工具：預測航班誤點的範例
titleSuffix: Azure Machine Learning
description: 利用 Azure Machine Learning 設計工具建立分類器及使用自訂 R 程式碼來預測航班誤點。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: d459350572d68cc5dcbbfd56933483404b94f918
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963270"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>利用 Azure Machine Learning 設計工具建立分類器及使用 R 來預測航班誤點

**設計工具 (預覽) 範例 6**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

此管線會使用航班記錄和天氣資料來預測排定的客機是否會誤點超過 15 分鐘。 此問題可以視為分類問題來處理，我們將預測兩個類別：誤點或準時。

以下是此範例的最終管線圖表：

[![管線的圖表](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 6] 來將其開啟。

## <a name="get-the-data"></a>取得資料

此範例會使用**航班誤點資料**的資料集。 這是美國運輸部門中 TranStats 資料收集的一部分。 此資料集包含 2013 年 4 月到 10 月的航班誤點資訊。 此資料集已經過預先處理，如下所示：

* 已篩選為包含美國境內 70 個最忙碌的機場。
* 將取消的航班重新標示為誤點達 15 分鐘以上。
* 已篩選掉更改路徑的航班。
* 已選取 14 個資料行。

為了補充航班資料，我們會使用**氣象資料集**。 天氣資料包含來自 NOAA 的每小起降天候觀測值，並且代表機場氣象站的觀測值，其中涵蓋的時間範圍與航班資料集相同。 此資料集已經過預先處理，如下所示：

* 天候觀測站識別碼已對應至相對應的機場識別碼。
* 已移除與 70 個最繁忙的機場沒有關聯的天候觀測站。
* 日期資料行已分割為個別的資料行：年、月及日。
* 已選取 26 個資料行。

## <a name="pre-process-the-data"></a>對資料進行前置處理

資料集通常必須先經過某些前置處理，才能進行分析。

![data-process](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>航班資料

**Carrier**、**OriginAirportID** 和 **DestAirportID** 資料行會儲存為整數。 不過，這些是類別屬性，應使用**編輯中繼資料**模組來轉換成類別。

![edit-metadata](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

然後使用資料集模組中的**選取資料行**，將可能洩漏目標的資料行中從資料集的資料行中排除：**DepDelay**、**DepDel15**、**ArrDelay**、**Canceled**、**Year**。 

若要聯結航班記錄與每小時氣象記錄，請使用已排定的出發時間作為其中一個聯結索引鍵。 若要執行此聯結，必須將 CSRDepTime 資料行無條件捨去到最接近的一小時，這會在**執行 R 指令碼**模組中完成。 

### <a name="weather-data"></a>天氣資料

具有大量遺漏值的資料行會利用**專案資料行**模組來加以排除。 這些資料行包含所有字串值的資料行：**ValueForWindCharacter**、**WetBulbFarenheit**、**WetBulbCelsius**、**PressureTendency**、**PressureChange**、**SeaLevelPressure** 和 **StationPressure**。

接著會將**清除遺漏資料**模組套用到其餘的資料行，以移除具有遺漏資料的資料列。

天氣觀測時間會無條件進位到最接近的完整時數。 排定的航班時間和天氣觀測時間會以相反的方向取到整數值，以確保模型只會使用航班時間之前的天氣資料。 

由於天氣資料會以當地時間回報，因此我們會將時區欄位從排定的出發時間和氣象觀測時間中移除，藉此來處理時差問題。 這些作業會使用**執行 R 指令碼**模組來完成。

### <a name="joining-datasets"></a>聯結資料集

航班記錄透過**聯結資料**模組來與航班出發地的天氣資料 (**OriginAirportID**) 聯結。

 ![根據出發地聯結航班和天氣](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


航班記錄透過航班目的地 (**DestAirportID**) 與氣象資料聯結。

 ![根據目的地聯結航班和天氣](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>準備訓練和測試範例

**分割資料**模組會分割資料，將四月到九月的記錄用於訓練，而十月的記錄用於測試。

 ![分割訓練和測試資料](./media/how-to-designer-sample-classification-flight-delay/split.png)

我們使用 [選取資料行] 模組將 [年]、[月] 及 [時區] 資料行從訓練資料集中移除。

## <a name="define-features"></a>定義功能

在機器學習中，特徵是您感興趣的某項目的個別可測量屬性。 尋找一組強大的特徵需要實驗和領域知識。 有些功能會比其他功能更適合用來預測目標。 此外，某些特徵可能與其他特徵有很強的關聯，所以不會將新資訊加入模型。 這些特徵都可以移除。

若要建立模型，您可以使用所有可用的特徵，或選取特徵子集。

## <a name="choose-and-apply-a-learning-algorithm"></a>選擇及套用學習演算法

使用**二元羅吉斯迴歸**模組來建立模型，並在訓練資料集上進行訓練。 

**訓練模型**模組的結果是經過訓練的分類模型，可用來為新範例評分以進行預測。 使用測試集從已訓練的模型中產生分數。 然後使用**評估模型**模組來分析和比較模型的品質。
若要在執行管線之後檢視**計分模型**模組的輸出，您可以按一下輸出連接埠並選取 [視覺化]  。 輸出會包含已計分的標籤和標籤的機率。

最後，若要測試結果的品質，請將**評估模型**模組新增至管線畫布，然後將左側的輸入連接埠連結到計分模型模組的輸出。 若要執行管線及檢視**評估模型**模組的輸出，請按一下輸出連接埠並選取 [視覺化]  。

## <a name="evaluate"></a>評估
羅吉斯迴歸模型在測試集上的 AUC 為 0.631。

 ![評估](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>後續步驟

探索設計工具的其他範例：

- [範例 1 - 迴歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2 - 迴歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3 - 使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 4 - 分類：預測信用風險 (成本導向)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5 - 分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 7 - 文字分類：Wikipedia SP 500 資料集](how-to-designer-sample-text-classification.md)