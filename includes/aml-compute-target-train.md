---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: dc6c29793b71525ad64254aa9f57e12e56601adc
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82726209"
---
**計算目標可以從一個定型作業重複使用到下一個**。 例如，將遠端 VM 連結至您的工作區之後，您可以將它重複用於多個作業。  針對機器學習管線，請針對每個計算目標使用適當的[管線步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

|訓練&nbsp;目標|[自動化 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 管線](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning 設計工具](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[本機電腦](../articles/machine-learning/how-to-set-up-training-targets.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure Machine Learning 計算叢集](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| 是 & <br/>超參數&nbsp;微調 | 是 | 是 |
|[Azure Machine Learning 計算執行個體](../articles/machine-learning/concept-compute-instance.md) | 是 & <br/>超參數微調 | 是 | 是 |
|[遠端 VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | 是 & <br/>超參數微調 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| 是（僅限 SDK 原生模式） | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | 是 | &nbsp; |
