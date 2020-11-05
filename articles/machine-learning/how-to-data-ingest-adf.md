---
title: 使用 Azure Data Factory 進行資料擷取
titleSuffix: Azure Machine Learning
description: 瞭解可用來建立資料內嵌管線的可用選項，並提供 Azure Data Factory 和各項優點。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 73850d8022618dd6544d19564e425288aff09771
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360627"
---
# <a name="data-ingestion-with-azure-data-factory"></a>使用 Azure Data Factory 進行資料擷取

在本文中，您將瞭解使用 Azure Data Factory (ADF) 建立資料內嵌管線的可用選項。 此管線是用來內嵌資料以用於 Azure Machine Learning。 Azure Data Factory 可讓您輕鬆地將 (ETL) 資料解壓縮、轉換和載入。 一旦資料轉換並載入至儲存體之後，就可以用來定型您的機器學習模型。

您可以使用原生 ADF 活動和檢測（例如 [資料流程](../data-factory/control-flow-execute-data-flow-activity.md)）來處理簡單的資料轉換。 在更複雜的案例中，可以使用一些自訂程式碼來處理資料。 例如，Python 或 R 程式碼。

使用 Azure Data Factory 在內嵌期間轉換資料有幾個常見的技巧。 每項技術都有優缺點，可判斷是否適合特定的使用案例：

| 技巧 | 優點 | 缺點 |
| ----- | ----- | ----- |
| ADF + Azure Functions | 低延遲、無伺服器計算</br>具狀態函式</br>可重複使用的函式 | 僅適用于短時間執行的處理 |
| ADF + 自訂群組件 | 大規模平行計算</br>適用于繁重的演算法 | 將程式碼包裝到可執行檔</br>處理相依性和 IO 的複雜度 |
| ADF + Azure Databricks 筆記本 | Apache Spark</br>原生 Python 環境 | 可能很昂貴</br>建立叢集一開始需要時間，並增加延遲

## <a name="adf-with-azure-functions"></a>ADF 與 Azure 函式

![下圖顯示 Azure Data Factory 管線、Azure 函式和執行 M L 管線，以及包含定型模型的 Azure Machine Learning 管線，以及它們如何與原始資料和已備妥的資料互動。](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions 可讓您在不需擔心應用程式基礎結構的情況下，執行一小段程式碼 () 函數。 在此選項中，會使用包裝在 Azure 函式中的自訂 Python 程式碼來處理資料。 

函式是使用 [ADF Azure function 活動](../data-factory/control-flow-azure-function-activity.md)來叫用。 這種方法是輕量資料轉換的絕佳選項。 

* 優點：
    * 在無伺服器計算上處理資料的延遲相對較低
    * ADF 管線可以叫用可執行複雜資料轉換流程的永久性[Azure 函數](../azure-functions/durable/durable-functions-overview.md) 
    * 資料轉換的詳細資料會由 Azure 函式所抽離，可重複使用並從其他位置叫用
* 缺點：
    * 必須先建立 Azure Functions，才能搭配 ADF 使用
    * Azure Functions 只適用于短時間執行的資料處理

## <a name="adf-with-custom-component-activity"></a>具有自訂群組件活動的 ADF

![圖表顯示 Azure Data Factory 管線、具有自訂群組件和執行 M L 管線，以及具有定型模型的 Azure Machine Learning 管線，以及它們如何與原始資料和已備妥的資料互動。](media/how-to-data-ingest-adf/adf-customcomponent.png)

在此選項中，會使用包裝在可執行檔中的自訂 Python 程式碼來處理資料。 它是以 [ADF 自訂群組件活動](../data-factory/transform-data-using-dotnet-custom-activity.md)來叫用。 這種方法比先前的技巧更適合大型資料。

* 優點：
    * 資料會在 [Azure Batch](../batch/batch-technical-overview.md) 集區上處理，以提供大規模的平行和高效能計算
    * 可以用來執行大量的演算法和處理大量的資料
* 缺點：
    * 必須先建立 Azure Batch 集區，才能搭配 ADF 使用
    * 超越將 Python 程式碼包裝到可執行檔的相關工程。 處理相依性和輸入/輸出參數的複雜度

## <a name="adf-with-azure-databricks-python-notebook"></a>使用 Python 筆記本 Azure Databricks ADF

![下圖顯示 Azure Data Factory 管線，其中包含 Azure Databricks Python 和執行 M L 管線，以及具有定型模型的 Azure Machine Learning 管線，以及它們如何與原始資料和已備妥的資料互動。](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 是 Microsoft 雲端中以 Apache Spark 為基礎的分析平臺。

在這項技術中，資料轉換是由在 Azure Databricks 叢集上執行的 [Python 筆記本](../data-factory/transform-data-using-databricks-notebook.md)所執行。 這可能是最常見的方法，充分利用 Azure Databricks 服務的完整功能。 它是專為大規模分散式資料處理而設計。

* 優點：
    * 資料會在資料處理最強大的 Azure 服務上進行轉換，Apache Spark 環境中進行備份
    * 原生支援 Python 以及資料科學架構和程式庫，包括 TensorFlow、PyTorch 和 scikit-learn-學習
    * 不需要將 Python 程式碼包裝到函式或可執行檔模組。 程式碼的運作方式相同。
* 缺點：
    * 必須先建立 Azure Databricks 基礎結構，才能搭配 ADF 使用
    * 取決於 Azure Databricks 設定，可能會很昂貴
    * 從「冷」模式啟動計算叢集需要一些時間，讓解決方案有高延遲 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>使用 Azure Machine Learning 管線中的資料

![圖表顯示 Azure Data Factory 管線和 Azure Machine Learning 管線，以及它們如何與原始資料和已備妥的資料互動。 Data Factory 管線會將資料摘要至備妥的資料資料庫，此資料庫會饋送資料存放區，以將資料集饋送至 Machine Learning 工作區。](media/how-to-data-ingest-adf/aml-dataset.png)

ADF 管線中的已轉換資料會儲存至資料儲存體 (，例如 Azure Blob) 。 Azure Machine Learning 可以使用 [資料存放區](./how-to-access-data.md#create-and-register-datastores) 和 [資料集](./how-to-create-register-datasets.md)來存取此資料。

每次執行 ADF 管線時，資料都會儲存到儲存體中的不同位置。 為了將位置傳遞給 Azure Machine Learning，ADF 管線會呼叫 Azure Machine Learning 管線。 呼叫 ML 管線時，資料位置和執行識別碼會以參數形式傳送。 ML 管線接著可以使用資料位置來建立資料存放區/資料集。 

> [!TIP]
> 資料集 [支援版本](./how-to-version-track-datasets.md)設定，因此 ML 管線可以註冊新版本的資料集，以指向 ADF 管線中最新的資料。

一旦資料可透過資料存放區或資料集存取，您就可以使用它來定型 ML 模型。 定型程式可能是從 ADF 呼叫之相同 ML 管線的一部分。 或者，它可能是個別的進程，例如 Jupyter 筆記本中的實驗。

由於資料集支援版本設定，而且每次從管線執行都會建立新版本，因此很容易就能瞭解用來定型模型的資料版本。

## <a name="next-steps"></a>後續步驟

* [在 Azure Data Factory 中執行 Databricks 筆記本](../data-factory/transform-data-using-databricks-notebook.md)
* [存取 Azure 儲存體服務中的資料](./how-to-access-data.md#create-and-register-datastores)
* [使用 Azure Machine Learning 中的資料集來定型模型](./how-to-train-with-datasets.md)
* [資料擷取管線的 DevOps](./how-to-cicd-data-ingestion.md)