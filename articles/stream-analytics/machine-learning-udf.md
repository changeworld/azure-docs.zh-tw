---
title: 整合 Azure 串流分析與 Azure Machine Learning
description: 本文說明如何整合 Azure 串流分析作業與 Azure Machine Learning 模型。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: devx-track-js
ms.openlocfilehash: 14f7462aec65d2a13eb36b291331c347b995d281
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130675"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>整合 Azure 串流分析與 Azure Machine Learning (預覽)

您可以將機器學習模型實作為 Azure 串流分析作業中的使用者定義函式 (UDF)，以對串流輸入資料執行即時評分和預測。 [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) 可讓您使用任何熱門的開放原始碼工具，例如 Tensorflow、scikit-learn 或 PyTorch，以準備、定型和部署模型。

## <a name="prerequisites"></a>Prerequisites

請先完成下列步驟，再將機器學習模型新增為串流分析作業的函式：

1. 使用 Azure Machine Learning [將您的模型部署為 Web 服務](../machine-learning/how-to-deploy-and-where.md)。

2. 您的評分指令碼應該會有[範例輸入和輸出](../machine-learning/how-to-deploy-and-where.md)，由 Azure Machine Learning 用來產生結構描述規格。 串流分析會使用結構描述來了解 Web 服務的函式簽章。 您可以使用這個 [範例 swagger 定義](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) 作為參考，以確保您已正確設定。

3. 請確定您的 Web 服務接受並傳回 JSON 序列化資料。

4. 將模型部署到 [Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) 上以進行大規模生產部署。 如果 Web 服務無法處理來自您作業的要求數，串流分析作業的效能會降低，因而影響延遲。 只有當您使用 Azure 入口網站時，才支援在 Azure 容器執行個體上部署的模型。 串流分析尚未支援使用 [Azure Machine Learning 設計](../machine-learning/concept-designer.md) 工具建立的模型。

## <a name="add-a-machine-learning-model-to-your-job"></a>將機器學習模型新增至您的作業

您可以直接從 Azure 入口網站或 Visual Studio Code，將 Azure Machine Learning 函式新增至串流分析作業。

### <a name="azure-portal"></a>Azure 入口網站

1. 在 Azure 入口網站中瀏覽至您的串流分析作業，然後選取 [作業拓撲] 底下的 [函式]。 然後，從 [ **+ 新增** ] 下拉式功能表選取 [ **Azure Machine Learning 服務** ]。

   ![新增 Azure Machine Learning UDF](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. 以下列屬性值填入 **Azure Machine Learning 服務函式** 表單：

   ![設定 Azure Machine Learning UDF](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visual Studio Code

1. 在 Visual Studio Code 中開啟您的串流分析專案，然後以滑鼠右鍵按一下 [ **函數** ] 資料夾。 然後選擇 [ **新增** 函式]。 從下拉式清單中選取 **MACHINE LEARNING UDF** 。

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="在 VS Code 中新增 UDF":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="在 VS Code 中新增 UDF":::

2. 輸入函式名稱，並在 CodeLens 的訂用帳戶中使用 [ **選取** ] 填入設定檔中的設定。

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="在 VS Code 中新增 UDF":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="在 VS Code 中新增 UDF":::

下表說明串流分析中 Azure Machine Learning 服務函式的每個屬性。

|屬性|描述|
|--------|-----------|
|函式別名|輸入名稱以在查詢中叫用函式。|
|訂用帳戶|您的 Azure 訂用帳戶..|
|Azure Machine Learning 工作區|您用來將模型部署為 Web 服務的 Azure Machine Learning 工作區。|
|部署|裝載模型的 Web 服務。|
|函式簽章|從 API 結構描述規格推斷而來的 Web 服務簽章。 如果您的簽章無法載入，請檢查您是否已在評分指令碼中提供範例輸入和輸出，以自動產生結構描述。|
|每個分割的平行要求數目|這是最佳化大量輸送量的進階設定。 此數字代表從作業的每個分割傳送到 Web 服務的並行要求。 具有六個串流單位 (SU) 和更低單位的作業有一個分割。 具有 12 個 SU 的作業有兩個分割，具有 18 個 SU 的作業有三個分割，依此類推。<br><br> 例如，如果您的作業有兩個分割，而且您將此參數設定為 4，則您的作業至 Web 服務將會有八個並行要求。 在此公開預覽階段，此值預設為 20，而且無法更新。|
|批次計數上限|這是最佳化大量輸送量的進階設定。 此數字代表在傳送至 Web 服務的單一要求中，將事件一起批次處理的數目上限。|

## <a name="supported-input-parameters"></a>支援的輸入參數

當您的串流分析查詢叫用 Azure Machine Learning UDF 時，作業會對 Web 服務建立 JSON 序列化要求。 要求是以模型特定的結構描述為基礎。 您必須在評分指令碼中提供範例輸入和輸出，以[自動產生結構描述](../machine-learning/how-to-deploy-and-where.md)。 此結構描述可讓串流分析針對任何支援的資料類型 (例如 numpy、pandas 和 PySpark) 建構 JSON 序列化要求。 在單一要求中，可以將多個輸入事件一起批次處理。

下列串流分析查詢是如何叫用 Azure Machine Learning UDF 的範例：

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

串流分析只支援針對 Azure Machine Learning 函式傳遞一個參數。 您可能需要先準備您的資料，然後再將其當做機器學習 UDF 的輸入來傳遞。

## <a name="pass-multiple-input-parameters-to-the-udf"></a>將多個輸入參數傳遞至 UDF

最常見的機器學習模型輸入範例是 numpy 陣列和資料框架。 您可以使用 JavaScript UDF 建立陣列，以及使用 `WITH` 子句建立 JSON 序列化資料框架。

### <a name="create-an-input-array"></a>建立輸入陣列

您可以建立會接受 N 個輸入數目的 JavaScript UDF，以及建立可用來作為 Azure Machine Learning UDF 輸入的陣列。

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

將 JavaScript UDF 新增至作業之後，您可以使用下列查詢來叫用您的 Azure Machine Learning UDF：

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

下列 JSON 是範例要求：

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>建立 Pandas 或 PySpark 資料框架

您可以使用 `WITH` 子句來建立 JSON 序列化資料框架，此資料框架可以作為輸入傳遞至您的 Azure Machine Learning UDF，如下所示。

下列查詢會藉由選取必要欄位來建立資料框架，並使用資料框架作為 Azure Machine Learning UDF 的輸入。

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM Dataframe
```

下列 JSON 是前一個查詢的範例要求：

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>將 Azure Machine Learning UDF 的效能最佳化

當您將模型部署到 Azure Kubernetes Service 時，您可以[分析模型以判斷資源使用率](../machine-learning/how-to-deploy-profile-model.md)。 您也可以[為您的部署啟用 App Insights](../machine-learning/how-to-enable-app-insights.md)，以了解要求速率、回應時間和失敗率。

如果您有高事件輸送量的案例，您可能需要變更串流分析中的下列參數，才能以較低的端對端延遲來達到最佳效能：

1. 批次計數上限。
2. 每個分割的平行要求數目。

### <a name="determine-the-right-batch-size"></a>決定正確的批次大小

在您部署 Web 服務之後，您會以不同的批次大小 (從 50 個開始) 傳送範例要求，並以百為單位來增加。 例如，200、500、1000、2000 等等。 您會注意到在特定批次大小之後，回應的延遲會增加。 回應延遲增加的時間點應該是作業的批次計數上限。

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>判斷每個分割的平行要求數目

在最佳調整的情況下，您的串流分析作業應該能夠將多個平行要求傳送至您的 Web 服務，並且在幾毫秒內取得回應。 Web 服務回應的延遲會直接影響串流分析作業的延遲和效能。 如果從您的作業到 Web 服務的呼叫需要很長的時間，您可能會發現浮水印延遲增加，而且可能也會看到待處理的輸入事件數目增加。

若要避免這類延遲，請確定您的 Azure Kubernetes Service (AKS) 叢集已佈建[正確數目的節點和複本](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)。 您的 Web 服務具有高可用性且會傳回成功的回應非常重要。 如果您的作業從您的 Web 服務收到服務無法使用的回應 (503)，則作業會持續以指數次數重試。 成功 (200) 和服務無法使用 (503) 以外的任何回應都會導致您的作業進入失敗狀態。

## <a name="next-steps"></a>後續步驟

* [教學課程：Azure 串流分析 JavaScript 使用者定義函式](stream-analytics-javascript-user-defined-functions.md)
* [使用 Azure Machine Learning Studio (傳統版) 函式調整串流分析作業](stream-analytics-scale-with-machine-learning-functions.md)