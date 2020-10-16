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
ms.date: 09/17/2020
ms.openlocfilehash: cdf2c3d1840f64b267f1aac1f8877d5f0bf76082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841933"
---
**計算目標皆可重複用於多個訓練作業。** 例如，將遠端 VM 連結至您的工作區之後，您可以將其重複用於多個作業。 針對機器學習管線，請針對每個計算目標使用適當的[管線步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true)。

您可以將下列任何資源用於大部分作業的定型計算目標。 並非所有資源都可以用於自動化機器學習、機器學習管線或設計工具。

|訓練 &nbsp;目標|[自動化機器學習](../articles/machine-learning/concept-automated-ml.md) | [機器學習管線](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning 設計工具](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[本機電腦](../articles/machine-learning/how-to-attach-compute-targets.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure Machine Learning 計算叢集](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| 是 | 是 | 是 |
|[Azure Machine Learning 計算執行個體](../articles/machine-learning/how-to-create-manage-compute-instance.md) | 是 (透過 SDK)  | 是 |  |
|[遠端虛擬機器](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | 是  | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| 是 (僅限 SDK 本機模式) | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | 是 | &nbsp; |
