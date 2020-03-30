---
title: 資料引入選項
titleSuffix: Azure Machine Learning
description: 瞭解用於訓練機器學習模型的資料引入選項。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086885"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Azure 機器學習中的資料引入

在本文中，您將瞭解 Azure 機器學習中可用的以下資料引入選項的優缺點。 

1. [Azure 資料工廠](#use-azure-data-factory)管道
2. [Azure 機器學習 Python SDK](#use-the-python-sdk)

資料引入是從一個或多個來源提取非結構化資料，然後為訓練機器學習模型做好準備的過程。 這還非常耗時，尤其是在手動完成時，以及如果您有來自多個源的大量資料。 自動化此工作可釋放資源，並確保模型使用最新和適用的資料。

Azure 資料工廠 （ADF） 是專門為提取、載入和轉換資料而構建的，但是 Python SDK 允許您為基本資料引入任務開發自訂代碼解決方案。 如果這兩者都不是您需要的，您也可以一起使用 ADF 和 Python SDK 來創建滿足您需求的整體資料引入工作流。 

## <a name="use-azure-data-factory"></a>使用 Azure Data Factory

[Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/introduction)為數據引入管道的資料來源監視和觸發器提供本機支援。  

下表總結了使用 Azure 資料工廠進行資料引入工作流的優缺點。

|優點|缺點
---|---
專為提取、載入和轉換資料而構建。|當前提供有限的 Azure 資料工廠管道任務集 
允許您創建資料驅動的工作流，以便大規模協調資料移動和轉換。|建造和維護費用高昂。 有關詳細資訊，請參閱 Azure 資料工廠的[定價頁](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)。
與[Azure 資料塊](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)和 Azure[函數](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)等各種 Azure 工具集成 | 不本機運行腳本，而是依賴于腳本運行的單獨計算 
本機支援資料來源觸發的資料引入| 
資料準備和模型培訓過程是分開的。|
Azure 資料工廠資料流程的嵌入式資料沿線功能|
[為非](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)腳本方法提供低代碼體驗使用者介面 |

這些步驟和下圖說明瞭 Azure 資料工廠的資料引入工作流。

1. 從源中提取資料
1. 將資料轉換並保存到輸出 Blob 容器，該容器用作 Azure 機器學習的資料存儲
1. 存儲準備好的資料後，Azure 資料工廠管道將調用一個培訓機器學習管道，該管道接收用於模型訓練的已準備好資料


    ![ADF 資料引入](media/concept-data-ingestion/data-ingest-option-one.svg)
    
瞭解如何使用[Azure 資料工廠](how-to-data-ingest-adf.md)為機器學習構建資料引入管道。

## <a name="use-the-python-sdk"></a>使用 Python SDK 

使用[Python SDK，](https://docs.microsoft.com/python/api/overview/azure/ml)可以將資料引入任務合併到 Azure[機器學習管道](how-to-create-your-first-pipeline.md)步驟中。

下表總結了使用 SDK 和 ML 管道步驟執行資料引入任務的優點和缺點。

優點| 缺點
---|---
配置您自己的 Python 腳本 | 本機不支援資料來源更改觸發。 需要邏輯應用或 Azure 函數實現
作為每個模型培訓執行的一部分的資料準備|創建資料引入腳本需要開發技能
支援各種計算目標的資料準備腳本，包括[Azure 機器學習計算](concept-compute-target.md#azure-machine-learning-compute-managed) |不提供用於創建引入機制的使用者介面

在下圖中，Azure 機器學習管道由兩個步驟組成：資料引入和模型培訓。 資料引入步驟包含可以使用 Python 庫和 Python SDK 完成的任務，例如從本地/Web 源中提取資料以及基本資料轉換（如缺少值歸因）。 然後，培訓步驟使用準備好的資料作為訓練腳本的輸入來訓練機器學習模型。 

![Azure 管道 + SDK 資料引入](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用[Azure 資料工廠](how-to-data-ingest-adf.md)構建機器學習的資料引入管道
* 瞭解如何使用[Azure 管道](how-to-cicd-data-ingestion.md)自動管理資料引入管道的開發生命週期。
