---
title: 資料內嵌選項
titleSuffix: Azure Machine Learning
description: 瞭解用來定型機器學習模型的資料內嵌選項。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 71a02e47db288890d1392f5423da0ef817ecd690
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303115"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Azure Machine Learning 中的資料內嵌

在本文中，您將瞭解 Azure Machine Learning 所提供的下列資料內嵌選項的優缺點。 

1. [Azure Data Factory](#use-azure-data-factory)管線
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

資料內嵌是從一或多個來源解壓縮非結構化資料，然後準備好定型機器學習模型的程式。 這也需要很長的時間，特別是手動完成時，如果您有多個來源的大量資料，則更是如此。 自動化此工作可釋出資源，並確保您的模型使用最新且適用的資料。

我們建議您一開始評估使用 Azure Data Factory （ADF），因為它是特別建立來解壓縮、載入和轉換資料。 如果您無法使用 ADF 來符合您的需求，您可以使用 Python SDK 來開發自訂程式碼解決方案，或使用 ADF 和 Python SDK 來建立整體資料內嵌工作流程，以符合您的需求。

## <a name="use-azure-data-factory"></a>使用 Azure Data Factory

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)為數據內嵌管線的資料來源監視和觸發程式提供原生支援。  

下表摘要說明使用資料內嵌工作流程 Azure Data Factory 的優缺點。

|優點|缺點
---|---
特別建立來解壓縮、載入和轉換資料。|目前提供一組有限的 Azure Data Factory 管線工作 
可讓您建立資料導向的工作流程，以大規模協調資料移動和轉換。|結構和維護的成本高昂。 如需詳細資訊，請參閱 Azure Data Factory 的[定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)。
與[Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)和[Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)等各種 Azure 工具整合 | 不會以原生方式執行腳本，而是依賴個別的計算來執行腳本 
原本就支援資料來源觸發資料內嵌| 
資料準備和模型定型程式是分開的。|
Azure Data Factory 資料流程的內嵌資料歷程功能|
為非腳本方法提供低程式碼體驗[使用者介面](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) |

這些步驟和下圖說明 Azure Data Factory 的資料內嵌工作流程。

1. 從來源提取資料
1. 將資料轉換並儲存至輸出 blob 容器，做為 Azure Machine Learning 的資料儲存體
1. 儲存備妥的資料之後，Azure Data Factory 管線會叫用定型 Machine Learning 管線，以接收準備好用於模型定型的資料。


    ![ADF 資料內嵌](media/concept-data-ingestion/data-ingest-option-one.svg)

## <a name="use-the-python-sdk"></a>使用 Python SDK 

使用[PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml)，您可以將資料內嵌工作納入 Azure Machine Learning 的[管線](how-to-create-your-first-pipeline.md)步驟中。

下表摘要說明使用 SDK 的優缺點，以及用於資料內嵌工作的 ML 管線步驟。

優點| 缺點
---|---
設定您自己的 Python 腳本 | 原本就不支援觸發資料來源變更。 需要邏輯應用程式或 Azure 函數實現
做為每個模型定型執行的一部分的資料準備工作|需要開發技能來建立資料內嵌腳本
支援各種計算目標上的資料準備腳本，包括[Azure Machine Learning 計算](concept-compute-target.md#azure-machine-learning-compute-managed) |未提供用來建立內嵌機制的使用者介面

在下圖中，Azure Machine Learning 管線包含兩個步驟：資料內嵌和模型定型。 資料內嵌步驟包含可使用 Python 程式庫和 Python SDK 完成的工作，例如從本機/web 來源解壓縮資料，以及進行基本資料轉換，像是遺漏值插補。 訓練步驟接著會使用備妥的資料做為定型腳本的輸入，以將您的機器學習模型定型。 

![Azure 管線 + SDK 資料內嵌](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用[Azure Data Factory](how-to-data-ingest-adf.md)建立 Machine Learning 的資料內嵌管線
* 瞭解如何使用[Azure Pipelines](how-to-cicd-data-ingestion.md)來自動化和管理資料內嵌管線的開發週期。