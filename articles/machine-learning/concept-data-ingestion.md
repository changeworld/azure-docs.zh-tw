---
title: '& 自動化的資料內嵌'
titleSuffix: Azure Machine Learning
description: 瞭解可用資料內嵌選項的優缺點，以定型您的機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: a096375e32e3d8a6760da88fe5ec86a70d364aff
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872090"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Azure Machine Learning 工作流程的資料內嵌選項

在本文中，您將瞭解 Azure Machine Learning 所提供資料內嵌選項的優缺點。 

從下列選項進行選擇：
+ [Azure Data Factory](#azure-data-factory) 管線，特別是為了將資料解壓縮、載入及轉換而建立

+ [Azure Machine Learning PYTHON SDK](#azure-machine-learning-python-sdk)，提供適用于資料內嵌工作的自訂程式碼解決方案。

+ 兩者的組合

資料內嵌是從一或多個來源中解壓縮非結構化資料，然後準備用來定型機器學習模型的進程。 它也需要大量時間，特別是在手動完成時，以及如果您有多個來源的大量資料。 將這種工作自動化可釋出資源，並確保您的模型使用最新且適用的資料。

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) 為數據內嵌管線提供資料來源監視和觸發程式的原生支援。  

下表摘要說明針對資料內嵌工作流程使用 Azure Data Factory 的優缺點。

|優點|缺點
---|---
專為解壓縮、載入和轉換資料所建立。|目前提供一組有限的 Azure Data Factory 管線工作 
可讓您建立資料驅動的工作流程，以大規模協調資料移動和轉換。|建立和維護成本高昂。 如需詳細資訊，請參閱 Azure Data Factory 的 [定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 。
與各種 Azure 工具整合，例如 [Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) 和 [Azure Functions](../data-factory/control-flow-azure-function-activity.md) | 不會以原生方式執行腳本，而是依賴個別的腳本執行計算 
以原生方式支援資料來源觸發的資料內嵌| 
資料準備和模型定型流程是分開的。|
Azure Data Factory 資料流程的內嵌資料歷程功能|
為非腳本方法提供低程式碼體驗[使用者介面](../data-factory/quickstart-create-data-factory-portal.md) |

這些步驟和下圖說明 Azure Data Factory 的資料內嵌工作流程。

1. 從資料來源提取資料
1. 轉換資料並將其儲存至輸出 blob 容器，以作為 Azure Machine Learning 的資料儲存體
1. 儲存備妥的資料之後，Azure Data Factory 管線會叫用定型 Machine Learning 管線，以接收準備的資料以進行模型定型


    ![ADF 資料內嵌](media/concept-data-ingestion/data-ingest-option-one.svg)
    
瞭解如何使用 [Azure Data Factory](how-to-data-ingest-adf.md)建立 Machine Learning 的資料內嵌管線。

## <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning Python SDK 

使用 [PYTHON SDK](/python/api/overview/azure/ml)，您可以將資料內嵌工作併入 [Azure Machine Learning 管線](./how-to-create-machine-learning-pipelines.md) 步驟中。

下表摘要說明使用 SDK 的優缺點，以及用於資料內嵌工作的 ML 管線步驟。

優點| 缺點
---|---
設定您自己的 Python 腳本 | 原本不支援資料來源變更觸發。 需要邏輯應用程式或 Azure 函數實現
每個模型定型執行過程中的資料準備|需要開發技能才能建立資料內嵌腳本
支援各種計算目標上的資料準備腳本，包括 [Azure Machine Learning 計算](concept-compute-target.md#azure-machine-learning-compute-managed) |未提供用來建立內嵌機制的使用者介面

在下圖中，Azure Machine Learning 管線是由兩個步驟所組成：資料內嵌和模型定型。 資料內嵌步驟包含可使用 Python 程式庫和 Python SDK 完成的工作，例如從本機/web 來源解壓縮資料，以及資料轉換，例如遺漏值插補。 定型步驟接著會使用備妥的資料做為定型腳本的輸入，以定型您的機器學習模型。 

![Azure 管線 + SDK 資料內嵌](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>後續步驟

遵循下列操作說明文章：
* [使用 Azure Data Factory 建立資料內嵌管線](how-to-data-ingest-adf.md)

* [使用 Azure Pipelines 自動化和管理資料內嵌管線](how-to-cicd-data-ingestion.md)。