---
title: 使用 Azure Data Factory 內嵌資料
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Data Factory 建立資料內嵌管線。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274784"
---
# <a name="data-ingestion-with-azure-data-factory"></a>使用 Azure Data Factory 內嵌資料

在本文中，您將瞭解如何使用 Azure Data Factory （ADF）來建立資料內嵌管線。 此管線是用來內嵌要與 Azure Machine Learning 搭配使用的資料。 Azure Data Factory 可讓您輕鬆地解壓縮、轉換和載入（ETL）資料。 一旦資料轉換並載入到儲存體之後，就可以用來定型您的機器學習模型。

簡單的資料轉換可以使用原生 ADF 活動和檢測（如[資料流程](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity)）來處理。 至於更複雜的案例，可以使用一些自訂程式碼來處理資料。 例如，Python 或 R 程式碼。

在內嵌期間，使用 Azure Data Factory 來轉換資料的常見技巧有好幾種。 每一種技術都有優缺點，判斷它是否適合用於特定的使用案例：

| 技巧 | 優點 | 缺點 |
| ----- | ----- | ----- |
| ADF + Azure Functions | 低延遲、無伺服器計算</br>具狀態函式</br>可重複使用的函式 | 僅適用于短期執行的處理 |
| ADF + 自訂群組件 | 大規模平行運算</br>適用于繁重的演算法 | 將程式碼包裝成可執行檔</br>處理相依性和 IO 的複雜性 |
| ADF + Azure Databricks 筆記本 | Apache Spark</br>原生 Python 環境 | 可能很昂貴</br>建立叢集一開始需要時間並增加延遲

## <a name="adf-with-azure-functions"></a>ADF 與 Azure 函式

![adf-函式](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions 可讓您執行一小段程式碼（函式），而不需擔心應用程式基礎結構。 在此選項中，會使用包裝在 Azure 函式中的自訂 Python 程式碼來處理資料。 

函式會使用[ADF Azure function 活動](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)叫用。 這種方法是輕量資料轉換的理想選項。 

* 優點：
    * 資料會在無伺服器計算上處理，且延遲較低
    * ADF 管線可以叫用能夠執行精密資料轉換流程的永久性[Azure](/azure/azure-functions/durable/durable-functions-overview)函式 
    * 資料轉換的詳細資料會由 Azure 函式抽象化，此函式可重複使用並從其他位置叫用
* 缺點：
    * 必須先建立 Azure Functions，才能搭配 ADF 使用
    * Azure Functions 僅適用于短期執行的資料處理

## <a name="adf-with-custom-component-activity"></a>具有自訂群組件活動的 ADF

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

在此選項中，會使用包裝在可執行檔中的自訂 Python 程式碼來處理資料。 它是透過[ADF 自訂群組件活動](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)叫用。 這種方法比先前的技術更適合用於大型資料。

* 優點：
    * 資料會在[Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview)集區上處理，以提供大規模的平行和高效能運算
    * 可以用來執行大量的演算法和處理大量的資料
* 缺點：
    * 必須先建立 Azure Batch 集區，才能搭配 ADF 使用
    * 透過將 Python 程式碼包裝至可執行檔的相關工程。 處理相依性和輸入/輸出參數的複雜性

## <a name="adf-with-azure-databricks-python-notebook"></a>具有 Azure Databricks Python 筆記本的 ADF

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/)是 Microsoft 雲端中以 Apache Spark 為基礎的分析平臺。

在這項技術中，資料轉換是由在 Azure Databricks 叢集上執行的[Python 筆記本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)所執行。 這可能是最常見的方法，利用 Azure Databricks 服務的完整功能。 它是針對大規模的分散式資料處理所設計。

* 優點：
    * 資料會轉換成最強大的資料處理 Azure 服務，由 Apache Spark 環境備份
    * Python 的原生支援，以及資料科學架構和程式庫（包括 TensorFlow、PyTorch 和 scikit-learn）-學習
    * 不需要將 Python 程式碼包裝到函式或可執行檔模組中。 程式碼的運作方式如下。
* 缺點：
    * 必須先建立 Azure Databricks 基礎結構，才能搭配 ADF 使用
    * 可能會耗費大量資源，視 Azure Databricks 設定而定
    * 從「冷」模式啟動計算叢集需要一些時間，以將高延遲帶入解決方案 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>使用 Azure Machine Learning 管線中的資料

![aml-資料集](media/how-to-data-ingest-adf/aml-dataset.png)

來自 ADF 管線的已轉換資料會儲存到資料儲存體（例如 Azure Blob）。 Azure Machine Learning 可以使用[資料存放區](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)和[資料集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)來存取此資料。

每次執行 ADF 管線時，資料都會儲存到儲存體中的不同位置。 為了將位置傳遞給 Azure Machine Learning，ADF 管線會呼叫 Azure Machine Learning 管線。 呼叫 ML 管線時，會以參數的形式傳送資料位置和執行識別碼。 然後，ML 管線就可以使用資料位置來建立資料存放區/資料集。 

> [!TIP]
> 資料集[支援版本](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)設定，因此 ML 管線可以註冊新版本的資料集，以指向 ADF 管線中的最新資料。

一旦資料可透過資料存放區或資料集存取，您就可以使用它來定型 ML 模型。 定型程式可能是從 ADF 呼叫之相同 ML 管線的一部分。 或者，它可能是個別的進程，例如 Jupyter 筆記本中的實驗。

由於資料集支援版本設定，而且每次從管線執行都會建立新的版本，因此很容易就能瞭解用來定型模型的資料版本。

## <a name="next-steps"></a>後續步驟

* [在 Azure Data Factory 中執行 Databricks 筆記本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [存取 Azure 儲存體服務中的資料](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [使用 Azure Machine Learning 中的資料集來定型模型](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [資料內嵌管線的 DevOps](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

