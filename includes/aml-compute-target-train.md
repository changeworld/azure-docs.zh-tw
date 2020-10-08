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
ms.date: 08/19/2020
ms.openlocfilehash: bbd32f7e40e3039a69a95b4458de86ff35f26867
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91643167"
---
**計算目標皆可重複用於多個訓練作業**。 例如，將遠端 VM 連結至您的工作區之後，您可以將它重複用於多個作業。  針對機器學習管線，請針對每個計算目標使用適當的[管線步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

|訓練 &nbsp;目標|[自動化 ML](../articles/machine-learning/concept-automated-ml.md) | [ML 管線](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning 設計工具](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[本機電腦](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure Machine Learning 計算叢集](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| 是與 <br/>超參數和&nbsp;微調 | 是 | 是 |
|[Azure Machine Learning 計算執行個體](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | 是 (透過 SDK) | 是 |  |
|[遠端虛擬機器](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | 是與 <br/>超參數微調 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| 是 (僅限 SDK 本機模式) | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | 是 | &nbsp; |
