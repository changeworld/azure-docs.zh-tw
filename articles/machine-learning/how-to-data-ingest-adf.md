---
title: 使用 Azure 資料工廠引入資料
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 資料工廠構建資料引入管道。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274784"
---
# <a name="data-ingestion-with-azure-data-factory"></a>使用 Azure 資料工廠引入資料

在本文中，您將瞭解如何使用 Azure 資料工廠 （ADF） 構建資料引入管道。 此管道用於引入資料以與 Azure 機器學習一起使用。 Azure 資料工廠允許您輕鬆擷取、轉換和下載 （ETL） 資料。 資料轉換並載入到存儲中後，可用於訓練機器學習模型。

簡單的資料轉換可以通過本機 ADF 活動和[資料流程](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity)等工具進行處理。 當涉及到更複雜的方案時，可以使用一些自訂代碼處理資料。 例如，Python 或 R 代碼。

有幾個常用技術使用 Azure 資料工廠在引入期間轉換資料。 每種技術都有優缺點，可以確定它是否適合特定的用例：

| 技巧 | 優點 | 缺點 |
| ----- | ----- | ----- |
| ADF + Azure 功能 | 低延遲、無伺服器計算</br>有狀態函數</br>可重用函數 | 僅適用于短運行處理 |
| ADF + 自訂群組件 | 大規模平行計算</br>適合重型演算法 | 將代碼包裝到可執行檔中</br>處理依賴項和 IO 的複雜性 |
| ADF = Azure 資料磚筆記本 | Apache Spark</br>本機 Python 環境 | 可能很貴</br>創建群集最初需要時間並增加延遲

## <a name="adf-with-azure-functions"></a>具有 Azure 功能的 ADF

![adf 功能](media/how-to-data-ingest-adf/adf-function.png)

Azure 函數允許您運行小塊代碼（函數），而不必擔心應用程式基礎結構。 在此選項中，資料使用自訂 Python 代碼打包到 Azure 函數中處理。 

函數使用[ADF Azure 函式活動](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)調用。 此方法是羽量級資料轉換的一個好選項。 

* 優點：
    * 資料在具有相對較低延遲的無伺服器計算上進行處理
    * ADF 管道可以調用[持久 Azure 函數](/azure/azure-functions/durable/durable-functions-overview)，該函數可以實現複雜的資料轉換流 
    * 資料轉換的詳細資訊由 Azure 函數抽象為，該函數可以從其他地方重用和調用
* 缺點：
    * 在與 ADF 使用之前，必須創建 Azure 函數
    * Azure 函數僅適用于短期運行的資料處理

## <a name="adf-with-custom-component-activity"></a>具有自訂群組件活動的 ADF

![adf-自訂群組件](media/how-to-data-ingest-adf/adf-customcomponent.png)

在此選項中，資料使用自訂 Python 代碼打包到可執行檔中進行處理。 它使用[ADF 自訂群組件活動](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)調用。 與以前的技術相比，此方法更適合大型資料。

* 優點：
    * 資料在[Azure 批次處理](https://docs.microsoft.com/azure/batch/batch-technical-overview)池上處理，該池提供大規模並行和高性能計算
    * 可用於運行大量演算法和處理大量資料
* 缺點：
    * 在與 ADF 使用之前，必須創建 Azure 批次處理池
    * 與將 Python 代碼包裝到可執行檔相關的工程。 處理依賴項和輸入/輸出參數的複雜性

## <a name="adf-with-azure-databricks-python-notebook"></a>帶有 Azure 資料磚 Python 筆記本的 ADF

![adf-資料磚](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure 資料磚塊](https://azure.microsoft.com/services/databricks/)是 Microsoft 雲中基於 Apache Spark 的分析平臺。

在此技術中，資料轉換由在 Azure 資料磚塊群集上運行的[Python 筆記本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)執行。 這可能是利用 Azure 資料磚塊服務的全部功能的最常見方法。 它專為大規模分散式資料處理而設計。

* 優點：
    * 資料在最強大的資料處理 Azure 服務上轉換，該服務由 Apache Spark 環境備份
    * Python 的本機支援以及資料科學框架和庫，包括 TensorFlow、PyTorch 和 scikit 學習
    * 無需將 Python 代碼包裝到函數或可執行模組中。 代碼的工作方式相同。
* 缺點：
    * 在與 ADF 使用之前，必須創建 Azure 資料磚塊基礎結構
    * 根據 Azure 資料磚塊配置，可能非常昂貴
    * 從"冷"模式旋轉計算群集需要一些時間，為解決方案帶來高延遲 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>在 Azure 機器學習管道中使用資料

![aml 資料集](media/how-to-data-ingest-adf/aml-dataset.png)

ADF 管道中轉換後的資料將保存到資料存儲（如 Azure Blob）。 Azure 機器學習可以使用[資料存儲](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)和[資料集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)訪問此資料。

每次 ADF 管道運行時，資料都會保存到存儲中的不同位置。 要將位置傳遞給 Azure 機器學習，ADF 管道將調用 Azure 機器學習管道。 調用 ML 管道時，資料位置和運行 ID 將作為參數發送。 然後，ML 管道可以使用資料位置創建資料存儲/資料集。 

> [!TIP]
> 資料集[支援版本控制](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)，因此 ML 管道可以註冊指向 ADF 管道的最新資料的新版本資料集。

通過資料存儲或資料集訪問資料後，可以使用它訓練 ML 模型。 訓練過程可能是從 ADF 調用的同一 ML 管道的一部分。 或者，它可能是一個單獨的過程，如在Jupyter筆記本中的實驗。

由於資料集支援版本控制，並且每個從管道運行都會創建一個新版本，因此很容易理解資料的哪個版本用於訓練模型。

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料工廠中運行資料磚塊筆記本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [訪問 Azure 存儲服務中的資料](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [在 Azure 機器學習中使用資料集訓練模型](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [資料引入管道的 DevOps](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

