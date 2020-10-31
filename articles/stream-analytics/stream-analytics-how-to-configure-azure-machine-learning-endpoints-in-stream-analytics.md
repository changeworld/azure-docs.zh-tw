---
title: 在 Azure 串流分析中使用 Azure Machine Learning Studio (傳統) 端點
description: 本文說明如何使用 Azure 串流分析中的機器語言使用者定義函式。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: 236191710dac19a08db0e8ce94dc695d393009a7
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127122"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>串流分析中的 Azure Machine Learning Studio (傳統版) 整合 (預覽)
串流分析支援對外呼叫 Azure Machine Learning Studio (傳統版) 端點的使用者定義函式。 [串流分析 REST API 程式庫](/rest/api/streamanalytics/)中會詳細說明此功能的 REST API 支援。 本文提供要在串流分析中成功實作這項功能所需的補充資訊。 您也可以在 [這裡](stream-analytics-machine-learning-integration-tutorial.md)取得已發佈的教學課程。

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>概觀：Azure Machine Learning Studio (傳統版) 術語
Microsoft Azure Machine Learning Studio (傳統版) 提供共同作業的拖放工具，讓您可用來依據資料建置、測試及部署預測性分析解決方案。 這項工具稱為 *Azure Machine Learning Studio (傳統)* 。 Studio (傳統) 用來與機器學習服務資源互動，並可讓您輕鬆地建立、測試及逐一查看您的設計。 這些資源和其定義如下。

* **工作區** ： *工作區* 是一種容器，可將所有其他機器學習資源一起保存在容器中，以供管理和控制。
* **實驗** ：資料科學家會建立「實驗」來利用資料集和訓練機器學習服務模型。
* **端點** ： *端點* 是 Studio (傳統) 物件，用來將功能當做輸入、套用指定的機器學習模型，並傳回計分的輸出。
* **評分 Web 服務** ：「評分 Web 服務」是上述端點的集合。

每個端點都有適用於批次執行和同步執行的 API。 串流分析使用同步執行。 該特定服務在 Azure Machine Learning Studio (傳統版) 中的名稱為[要求/回應服務](../machine-learning/classic/consume-web-services.md)。

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Studio (串流分析作業所需的傳統) 資源
為了處理串流分析作業，必須要有要求/回應端點、 [apikey](../machine-learning/classic/consume-web-services.md)和 swagger 定義才能順利執行。 串流分析有其他端點可建構 swagger 端點的 URL、查閱介面，以及將預設 UDF 定義傳回給使用者。

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>透過 REST API 設定串流分析和 Studio (傳統) UDF
藉由使用 REST Api，您可以設定您的作業來呼叫 Studio (傳統) 函數。 步驟如下：

1. 建立串流分析作業
2. 定義輸入
3. 定義輸出
4. 建立使用者定義函式 (UDF)
5. 撰寫呼叫 UDF 的串流分析轉換
6. 啟動工作

## <a name="creating-a-udf-with-basic-properties"></a>使用基本屬性建立 UDF
下列範例程式碼會建立名為 *newudf* 且繫結至 Azure Machine Learning Studio (傳統版) 端點的純量 UDF，來做為示範。 請注意，您可以在 API 說明頁面中找到所選服務的 *端點* (服務 URI)，以及在 [服務] 主頁面中找到 *apiKey* 。

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

要求本文範例：

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>呼叫預設 UDF 的 RetrieveDefaultDefinition 端點
一旦建立好基本架構 UDF，就需要 UDF 的完整定義。 RetrieveDefaultDefinition 端點可協助您取得繫結至 Azure Machine Learning Studio (傳統版) 端點之純量函式的預設定義。 下列承載需要您取得系結至 Studio (傳統) 端點之純量函式的預設 UDF 定義。 它不會指定實際的端點，因為它已經在 PUT 要求期間提供。 串流分析會呼叫要求中提供的端點 (如果已明確提供)。 否則，它會使用原本參考的端點。 UDF 在這邊會採用單一字串參數 (一個句子)，並傳回指出該句子的「情緒」標籤的單一類型字串輸出。

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

要求本文範例：

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

此項目的範例輸出會看起來像下面這樣。

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>使用回應修補 UDF
現在必須使用先前的回應修補 UDF，如下所示。

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

要求本文 (RetrieveDefaultDefinition 的輸出)：

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>實作串流分析轉換來呼叫 UDF
現在要查詢每一個輸入事件的 UDF (這裡稱為 scoreTweet)，並將該事件的回應寫入至輸出。

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>取得說明
如需進一步的協助，請嘗試 [Azure 串流分析的 Microsoft 問與答頁面](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](/rest/api/streamanalytics/)