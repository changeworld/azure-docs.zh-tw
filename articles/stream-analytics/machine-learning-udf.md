---
title: 將 Azure 串流分析與 Azure Machine Learning 整合
description: 本文說明如何將 Azure 串流分析作業與 Azure Machine Learning 模型整合。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81481977"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>整合 Azure 串流分析與 Azure Machine Learning （預覽）

您可以將機器學習模型實作為 Azure 串流分析作業中的使用者定義函式（UDF），以對串流輸入資料執行即時評分和預測。 [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md)可讓您使用任何熱門的開放原始碼工具，例如 Tensorflow、scikit-learn、學習或 PyTorch，以準備、定型和部署模型。

> [!NOTE]
> 這項功能目前處於公開預覽階段。 您只能使用[串流分析入口網站預覽連結](https://aka.ms/asaportalpreview)，在 Azure 入口網站上存取這項功能。 這項功能也適用于[Visual Studio 的串流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)的最新版本。

## <a name="prerequisites"></a>先決條件

請先完成下列步驟，再將機器學習模型新增為串流分析作業的函式：

1. 使用 Azure Machine Learning，將[您的模型部署為 web 服務](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)。

2. 您的評分腳本應該會有 Azure Machine Learning 用來產生架構規格的[範例輸入和輸出](../machine-learning/how-to-deploy-and-where.md#example-entry-script)。 串流分析會使用架構來瞭解 web 服務的功能簽章。

3. 請確定您的 web 服務接受並傳回 JSON 序列化的資料。

4. 在大規模生產部署的[Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target)上部署您的模型。 如果 web 服務無法處理來自您作業的要求數，串流分析作業的效能將會降低，因而影響延遲。 目前不支援部署在 Azure 容器實例上的模型，但會在接下來的幾個月內推出。

## <a name="add-a-machine-learning-model-to-your-job"></a>將機器學習模型新增至您的作業

您可以直接從 Azure 入口網站將 Azure Machine Learning 函式新增至您的串流分析作業。

1. 流覽至 Azure 入口網站中的串流分析作業，**然後選取 [** **作業拓撲**] 下的 [函式]。 然後，從 [ **+ 新增**] 下拉式功能表中選取 [ **Azure ML 服務**]。

   ![新增 Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. 在 [ **Azure Machine Learning 服務**函式] 表單中填入下列屬性值：

   ![設定 Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

下表描述串流分析中 Azure ML 服務函式的每個屬性。

|屬性|描述|
|--------|-----------|
|函式別名|輸入要在查詢中叫用函式的名稱。|
|訂用帳戶|您的 Azure 訂用帳戶。|
|Azure ML 工作區|您用來將模型部署為 web 服務的 Azure Machine Learning 工作區。|
|部署|裝載模型的 web 服務。|
|函式簽章|從 API 的架構規格推斷而來的 web 服務簽章。 如果您的簽章無法載入，請檢查您是否已在評分腳本中提供範例輸入和輸出，以自動產生架構。|
|每個分割區的平行要求數目|這是優化高擴充輸送量的先進設定。 此數位代表從您的作業的每個資料分割傳送到 web 服務的並行要求。 具有六個串流單位（SU）和更低層的作業有一個分割區。 具有12個 su 的作業有兩個分割區，18個 su 有三個磁碟分割，依此類推。<br><br> 例如，如果您的作業有兩個分割區，而且您將此參數設定為四個，則您的作業將會有八個從您的工作到 web 服務的並行要求。 在此公開預覽階段，此值預設為20，而且無法更新。|
|批次計數上限|這是優化大規模輸送量的先進設定。 此數位代表在傳送至 web 服務的單一要求中，將事件分批放在一起的最大數目。|

## <a name="supported-input-parameters"></a>支援的輸入參數

當您的串流分析查詢叫用 Azure Machine Learning UDF 時，作業會對 web 服務建立 JSON 序列化要求。 要求是以模型特定的架構為基礎。 您必須在評分腳本中提供範例輸入和輸出，才能[自動產生架構](../machine-learning/how-to-deploy-and-where.md)。 此架構可讓串流分析針對任何支援的資料類型（例如 numpy、pandas 和 PySpark）來建立 JSON 序列化要求。 在單一要求中，可以將多個輸入事件批次處理在一起。

下列串流分析查詢是如何叫用 Azure Machine Learning UDF 的範例：

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

串流分析只支援傳遞 Azure Machine Learning 函數的一個參數。 您可能需要先準備您的資料，然後再將它當做機器學習 UDF 的輸入來傳遞。

## <a name="pass-multiple-input-parameters-to-the-udf"></a>將多個輸入參數傳遞至 UDF

最常見的機器學習模型輸入範例為 numpy 陣列和資料框架。 您可以使用 JavaScript UDF 建立陣列，並使用`WITH`子句建立 JSON 序列化資料框架。

### <a name="create-an-input-array"></a>建立輸入陣列

您可以建立會接受*N*個輸入數目的 JavaScript UDF，並建立可用來做為 Azure Machine Learning UDF 輸入的陣列。

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

將 JavaScript UDF 新增至作業之後，您可以使用下列查詢叫用您的 Azure Machine Learning UDF：

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

您可以使用`WITH`子句來建立 JSON 序列化資料框架，以做為輸入傳遞至您的 Azure Machine Learning UDF，如下所示。

下列查詢會藉由選取必要欄位來建立資料框架，並使用資料框架做為 Azure Machine Learning UDF 的輸入。

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

下列 JSON 是來自前一個查詢的範例要求：

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>將 Azure Machine Learning Udf 的效能優化

當您將模型部署到 Azure Kubernetes Service 時，您可以[分析您的模型，以判斷資源使用率](../machine-learning/how-to-deploy-and-where.md#profilemodel)。 您也可以[為您的部署啟用 App Insights](../machine-learning/how-to-enable-app-insights.md) ，以瞭解要求率、回應時間和失敗率。

如果您有高事件輸送量的案例，您可能需要變更串流分析中的下列參數，才能以較低的端對端延遲來達到最佳效能：

1. 批次計數上限。
2. 每個分割區的平行要求數目。

### <a name="determine-the-right-batch-size"></a>判斷正確的批次大小

在您部署 web 服務之後，您會以不同的批次大小（從50開始）傳送範例要求，並依數百個順序來增加。 例如，200、500、1000、2000等等。 您會注意到在特定批次大小之後，回應的延遲會增加。 回應增加延遲的時間點應該是作業的最大批次計數。

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>判斷每個分割區的平行要求數目

在最佳調整的情況下，您的串流分析作業應該能夠將多個平行要求傳送至您的 web 服務，並在幾毫秒內取得回應。 Web 服務回應的延遲可能會直接影響串流分析作業的延遲和效能。 如果從您的作業到 web 服務的呼叫需要很長的時間，您可能會發現浮水印延遲增加，而且可能也會看到待處理的輸入事件數目增加。

若要避免這類延遲，請確定您的 Azure Kubernetes Service （AKS）叢集已布建[正確的節點和複本數目](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)。 您的 web 服務非常重要，而且會傳回成功的回應。 如果您的作業從您的 web 服務收到服務無法使用的回應（503），它會持續以指數後重試。 除了 [成功] （200）和 [服務無法使用] （503）以外的任何回應都會導致您的作業進入 [失敗] 狀態。

## <a name="next-steps"></a>後續步驟

* [教學課程：Azure 串流分析 JavaScript 使用者定義函式](stream-analytics-javascript-user-defined-functions.md)
* [使用 Azure Machine Learning Studio （傳統）功能調整您的串流分析作業](stream-analytics-scale-with-machine-learning-functions.md)

