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
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77156030"
---
**計算目標可以從一個培訓作業重用到下一個**。 例如，將遠端 VM 連結至您的工作區之後，您可以將它重複用於多個作業。  對於機器學習管道，對每個計算目標使用相應的[管道步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

|培訓&nbsp;目標|[自動化 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 管道](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning 設計工具](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[本機電腦](../articles/machine-learning/how-to-set-up-training-targets.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure 機器學習計算群集](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| 是的 & <br/>超參數&nbsp;調優 | 是 | 是 |
|[遠端 VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | 是的 & <br/>超參數微調 | 是 | &nbsp; |
|[Azure&nbsp;資料塊](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| 是（僅限 SDK 本地模式） | 是 | &nbsp; |
|[Azure 資料湖分析](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | 是 | &nbsp; |
