---
title: 將 Azure 串流分析與 Azure 機器學習整合
description: 本文介紹如何將 Azure 流分析作業與 Azure 機器學習模型集成。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481977"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>將 Azure 串流分析與 Azure 機器學習(預覽)整合

您可以在 Azure 串流分析工作中實現機器學習模型作為使用者定義的函數 (UDF),以便對流式輸入數據進行即時評分和預測。 [Azure 機器學習](../machine-learning/overview-what-is-azure-ml.md)允許您使用任何流行的開源工具(如 Tensorflow、scikit-學習或 PyTorch)來準備、訓練和部署模型。

> [!NOTE]
> 此功能處於公共預覽版中。 只能通過使用[流分析門戶預覽連結](https://aka.ms/asaportalpreview)在 Azure 門戶上訪問此功能。 此功能也可在[最新版本的流分析工具為可視化工作室](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)提供。

## <a name="prerequisites"></a>Prerequisites

在將機器學習模型作為函數添加到流分析作業之前,請完成以下步驟:

1. 使用 Azure 機器學習[將模型部署為 Web 服務](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)。

2. 評分文本應具有 Azure 機器學習用於產生架構規範[的範例輸入和輸出](../machine-learning/how-to-deploy-and-where.md#example-entry-script)。 流分析使用架構來瞭解 Web 服務的函數簽名。

3. 請確保您的 Web 服務接受並返回 JSON 序列化數據。

4. 在 Azure[庫伯奈斯服務](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target)上部署模型,用於大規模生產部署。 如果 Web 服務無法處理來自作業的請求數,則流分析作業的性能將降低,從而影響延遲。 部署在 Azure 容器實例上的模型今天不受支援,但將在未來幾個月提供。

## <a name="add-a-machine-learning-model-to-your-job"></a>將機器學習模型加入工作中

可以直接從 Azure 門戶將 Azure 機器學習功能添加到流分析作業。

1. 導覽到 Azure 門戶中的流分析作業,然後選擇**工作拓撲**下的**函數**。 然後,從 **+ 新增**下拉選單中選擇 Azure ML**服務**。

   ![新增 Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. 使用以下屬性值填寫**Azure 機器學習服務函數**窗體:

   ![設定 Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

下表描述了流分析中 Azure ML 服務函數的每個屬性。

|屬性|描述|
|--------|-----------|
|函式別名|輸入名稱以在查詢中調用函數。|
|訂用帳戶|您的 Azure 訂閱。|
|Azure ML 工作區|用於將模型部署為 Web 服務的 Azure 機器學習工作區。|
|部署|託管模型的 Web 服務。|
|函式簽章|從 API 的架構規範推斷出 Web 服務的簽名。 如果簽名無法載入,請檢查您在評分文本中提供了範例輸入和輸出以自動生成架構。|
|每個分割區的並行請求數|這是優化大規模輸送量的高級配置。 此數位表示從作業的每個分區發送到 Web 服務的併發請求。 具有六個流式處理單元 (SU) 和較低的作業有一個分區。 具有 12 個 S 的作業有兩個分區,18 個 SUs 有三個分區,等等。<br><br> 例如,如果作業有兩個分區,並且將此參數設置為 4 個,則作業將同時向 Web 服務請求八個。 在公開預覽的這個時候,此值預設為 20,無法更新。|
|批次計數上限|這是用於優化大規模輸送量的高級配置。 此數位表示發送到 Web 服務的單個請求中一起批處理的最大事件數。|

## <a name="supported-input-parameters"></a>支援的輸入參數

當流分析查詢調用 Azure 機器學習 UDF 時,作業將創建到 Web 服務的 JSON 序列化請求。 請求基於特定於模型的架構。 您必須在評分文稿中提供範例輸入和輸出,才能[自動產生架構](../machine-learning/how-to-deploy-and-where.md)。 該架構允許流分析為任何受支援的數據類型(如 numpy、熊貓和 PySpark)構造 JSON 序列化請求。 多個輸入事件可以在單個請求中批處理在一起。

以分析查詢是如何呼叫 Azure 機器學習 UDF 的範例:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

流分析僅支援為 Azure 機器學習函數傳遞一個參數。 您可能需要先準備數據,然後再將其作為輸入傳遞給機器學習 UDF。

## <a name="pass-multiple-input-parameters-to-the-udf"></a>將多個輸入參數傳遞給 UDF

機器學習模型輸入的最常見示例是數位陣組和 DataFrame。 您可以使用 JavaScript UDF 建立陣列,`WITH`並使用子 句創建 JSON 序列化的數據框架。

### <a name="create-an-input-array"></a>建立輸入陣列

您可以創建 JavaScript UDF,它接受*N*個輸入數,並建立可用作 Azure 機器學習 UDF 輸入的陣列。

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

將 JavaScript UDF 新增到作業後,可以使用以下查詢呼叫 Azure 機器學習 UDF:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

以下 JSON 是一個範例請求:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>建立熊貓或 PySpark 資料架構

可以使用子`WITH`句創建 JSON 序列化數據幀,該幀可以作為輸入傳遞給 Azure 機器學習 UDF,如下所示。

以下查詢通過選擇必要的欄位創建 DataFrame,並使用 DataFrame 作為 Azure 機器學習 UDF 的輸入。

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

以下 JSON 是上一個查詢的範例請求:

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>最佳化 Azure 機器學習 UF 的效能

將模型部署到 Azure 庫伯奈斯服務時,可以[分析模型以確定資源利用率](../machine-learning/how-to-deploy-and-where.md#profilemodel)。 您還可以[為部署啟用應用見解](../machine-learning/how-to-enable-app-insights.md),以瞭解請求率、回應時間和失敗率。

如果方案具有高事件輸送量,則可能需要更改流分析中的以下參數,以便在低端到端延遲時實現最佳性能:

1. 最大批數。
2. 每個分區的並行請求數。

### <a name="determine-the-right-batch-size"></a>確定正確的批次大小

部署 Web 服務後,發送具有不同批次處理大小的樣本請求,從 50 開始,以數百個順序增加。 例如,200、500、1000、2000 等。 您會注意到,在一定的批處理大小之後,回應的延遲會增加。 回應延遲增加的點應該是作業的最大批處理計數。

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>確定每個分割區的並行要求數

在最佳縮放時,流分析作業應該能夠向 Web 服務發送多個並行請求,並在幾毫秒內獲得回應。 Web 服務回應的延遲可能會直接影響流分析作業的延遲和性能。 如果從作業到 Web 服務的呼叫需要很長時間,則浮浮水印延遲可能會增加,並且可能還會看到積壓的輸入事件數增加。

為了防止此類延遲,請確保 Azure 庫伯奈斯服務 (AKS) 叢集已預配了[正確數量的節點和副本](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)。 Web 服務的高度可用並返回成功的回應至關重要。 如果作業從 Web 服務收到不可用回應 (503),則它將不斷重試,並關閉指數。 成功 (200) 和服務不可用 (503) 以外的任何回應都將導致作業進入失敗狀態。

## <a name="next-steps"></a>後續步驟

* [教學課程：Azure 串流分析 JavaScript 使用者定義函式](stream-analytics-javascript-user-defined-functions.md)
* [使用 Azure 機器學習工作室(經典)功能擴展流分析作業](stream-analytics-scale-with-machine-learning-functions.md)

