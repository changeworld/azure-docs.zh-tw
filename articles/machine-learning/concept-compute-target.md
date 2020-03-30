---
title: 什麼是計算目標
titleSuffix: Azure Machine Learning
description: 使用 Azure 機器學習定義要訓練或部署模型的位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ec2d9152bf8d3d7c60f00e902f155212ee1b81cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270416"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure 機器學習中的計算目標是什麼？ 

**計算目標是**運行訓練腳本或託管服務部署的指定計算資源/環境。 此位置可能是本地電腦或基於雲的計算資源。 使用計算目標可以方便您以後更改計算環境，而無需更改代碼。  

在典型的模型開發生命週期中，您可以：
1. 首先開發和試驗少量資料。 在此階段，我們建議將本地環境（本地電腦或基於雲的 VM）作為計算目標。 
2. 擴展至更大的資料，或使用這些[培訓計算目標](#train)之一進行分散式培訓。  
3. 模型準備就緒後，將其部署到具有這些[部署計算目標](#deploy)之一的 Web 託管環境或 IoT 設備。

用於計算目標的計算資源附加到[工作區](concept-workspace.md)。 本地電腦以外的計算資源由工作區的使用者共用。

## <a name="training-compute-targets"></a><a name="train"></a>訓練計算目標

Azure 機器學習在不同的計算資源中具有不同的支援。  您還可以附加自己的計算資源，儘管對各種方案的支援可能有所不同。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

詳細瞭解[如何設置和使用模型培訓的計算目標](how-to-set-up-training-targets.md)。

## <a name="deployment-targets"></a><a name="deploy"></a>部署目標

以下計算資源可用於託管模型部署。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

瞭解[將模型部署到計算目標的位置以及如何](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure 機器學習計算（託管）

託管計算資源由 Azure 機器學習創建和管理。 此計算針對機器學習工作負載進行了優化。 Azure 機器學習計算群集和[計算實例](concept-compute-instance.md)是唯一的託管計算。 將來可能會添加其他託管計算資源。

您可以在：

| | Azure Machine Learning Studio | Azure 入口網站 | SDK | Resource Manager 範本 | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| 計算執行個體 | 是 | 是 | 是 | 是 |  |
| 計算叢集 | 是 | 是 | 是 | 是 | 是 |

創建時，這些計算資源會自動成為工作區的一部分，而不像其他類型的計算目標。

### <a name="compute-clusters"></a>計算叢集

可以使用 Azure 機器學習計算群集進行培訓和批量推斷（預覽）。  使用此計算資源，您可以：

* 單節點或多節點群集
* 每次提交運行時自動縮放 
* 自動群集管理和作業計畫 
* 同時支援 CPU 和 GPU 資源



## <a name="unmanaged-compute"></a>非託管計算

非託管計算目標*不*由 Azure 機器學習管理。 在 Azure 機器學習之外創建這種類型的計算目標，然後將其附加到工作區。 非託管計算資源可能需要其他步驟來維護或提高機器學習工作負載的性能。

## <a name="next-steps"></a>後續步驟

了解如何：
* [設置計算目標以訓練模型](how-to-set-up-training-targets.md)
* [將模型部署到計算目標](how-to-deploy-and-where.md)