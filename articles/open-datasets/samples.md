---
title: 使用 NOAA 資料的 Jupyter Notebook 範例
titleSuffix: Azure Open Datasets
description: 使用 Azure 開放資料集的 Jupyter Notebook 範例，了解如何載入開放資料集，並使用它們擴充示範資料。 這些技術包括使用 Spark 和 Pandas 處理資料。
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 8b96a35db91a282be1fb5e4c6143e6bd0a0203f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73606151"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>顯示如何運用開放資料集擴充資料的 Jupyter Notebook 範例 
Azure 開放資料集的 Jupyter Notebook 範例會示範如何載入開放資料集，並使用它們擴充示範資料。 這些技術包括使用 Apache Spark 和 Pandas 處理資料。

>[!IMPORTANT]
>在非 Spark 環境中工作時，開放資料集僅允許一次下載一個月含特定類別的資料，以避免使用大型資料集時發生 MemoryError。

## <a name="load-noaa-integrated-surface-database-isd-data"></a>載入 NOAA Integrated Surface Database (ISD) 資料 
|Notebook        | 描述                                    |
|----------------|------------------------------------------------|
|[將最近一個月的氣象資料載入至 Pandas 資料框架](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) \(英文\) | 了解如何將歷史氣象資料載入至您最愛的 Pandas 資料框架。 |
|[將最近一個月的氣象資料載入至 Spark 資料框架](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) \(英文\) | 了解如何將歷史氣象資料載入至您最愛的 Spark 資料框架。  |

## <a name="join-demo-data-with-noaa-isd-data"></a>聯結示範資料與 NOAA ISD 資料 
|Notebook        | 描述                                    |
|----------------|------------------------------------------------|
|[聯結示範資料與氣象資料 - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) \(英文\) | 聯結感應器位置的 1 個月示範資料集與 Pandas 資料框架中的氣象讀數。  |
|[聯結示範資料與氣象資料 - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) \(英文\) | 聯結感應器位置的示範資料集與 Spark 資料框架中的氣象讀數。 |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>聯結紐約市計程車資料與 NOAA ISD 資料 
|Notebook        | 描述                                    |
|----------------|------------------------------------------------|
|[加入氣象資料擴充的計程車行程資料 - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) \(英文\) | 載入紐約市綠色計程車資料 (超過 1 個月)，並加入 Pandas 資料框架中的氣象資料進行擴充。 此範例會覆寫方法 `get_pandas_limit` 並在資料載入效能與資料量之間取得平衡。|
|[加入氣象資料擴充的計程車行程資料 – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) \(英文\) | 載入紐約市綠色計程車資料，並加入 Spark 資料框架中的氣象資料進行擴充。  |

## <a name="next-steps"></a>後續步驟

* [教學課程：使用自動化機器學習和開放資料集來建置迴歸模型](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [適用於開放資料集的 Python SDK](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure 開放資料集目錄](https://azure.microsoft.com/services/open-datasets/catalog/) \(英文\)
