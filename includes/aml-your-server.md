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
ms.date: 03/05/2020
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558710"
---
1. 使用位於 [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?view=azure-ml-py) \(英文\) 的指示來安裝適用於 Python 的 Azure Machine Learning SDK

1. 建立 [Azure Machine Learning 工作區](../articles/machine-learning/how-to-manage-workspace.md)。

1. 撰寫 [組態檔](../articles/machine-learning/how-to-configure-environment.md#workspace)檔案 (**aml_config/config.json**)。

1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 從複製的目錄中啟動 Notebook 伺服器。

    ```bash
    jupyter notebook
    ```