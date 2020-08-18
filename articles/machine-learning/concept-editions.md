---
title: Azure Machine Learning Enterprise 和 Basic 版本
description: 瞭解 Azure Machine Learning 版本之間的差異。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: fbe3f0109ca3af2cf9ff44061c7882e6bd72c8a6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508597"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>Enterprise (preview) 和基本版本的 Azure Machine Learning 

Azure Machine Learning 針對您的機器學習服務需求提供兩種版本。 這些版本會決定開發人員和資料科學家可在其工作區中使用哪些機器學習工具。

## <a name="choose-an-edition"></a>選擇版本

您會在建立工作區時指派此版本。 客戶須負責支付計算和其他 Azure 資源在這段期間所產生的成本。 瞭解如何 [管理 Azure Machine Learning 的成本](concept-plan-manage-cost.md)。

瞭解如何將 [基本工作區升級為 Enterprise (preview) edition](how-to-manage-workspace.md#upgrade)。 

## <a name="whats-in-each-edition"></a>每個版本的內容

### <a name="data-for-machine-learning-capabilities"></a>Machine Learning 功能的資料  

| 功能                     | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| 標記：在 studio (Web) 中[建立和管理標記專案](tutorial-labeling.md)                                                | 全部                     |
| 標記：在 studio 中標籤人員 (Web)                                     | 全部                     |
| 標籤：使用私用員工                               | 全部                     |
| 標記： [ML 輔助影像分類和物件偵測](how-to-label-images.md)                  | 僅限企業版 |
| 資料集 + 資料存放區：在 Python 中建立和管理                       | 全部                     |
| 資料集 + 資料存放區：在 studio (Web) 中建立和管理                         | 全部                     |
| 漂移：在 Python 中查看和管理資料集監視                           | 全部                     |
| 漂移：在 studio (Web) 中查看和管理資料集監視器                            | 僅限企業版 |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a> (AutoML) 的自動化訓練功能

| 功能    | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| [在筆記本中](how-to-configure-auto-train.md)建立和執行 AutoML 實驗               | 全部                     |
| [在 studio (web) 中](how-to-use-automated-ml-for-ml-models.md)建立和執行 AutoML 實驗   | 僅限企業版 |
| 領先業界的 AutoML 預測功能             | 僅限企業版 |
| 深度學習和其他先進學習的支援 | 僅限企業版 |
| 大型資料支援分類和回歸工作 (高達 100 GB)                      | 僅限企業版 |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>負責任的 Machine Learning

| 功能    | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| [模型說明特徵](how-to-machine-learning-interpretability-automl.md)                                              | 全部                     |
| [差異隱私權](how-to-differential-privacy.md)                          | 全部                     |
| 用來執行資料表的自訂標記    | 全部                     |
| 公平 AzureML 整合                                      | 全部                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>組建和定型功能

| 功能    | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| Visual Studio Code 整合                                                     | 全部                     |
| 增強式學習                                                             | 全部                     |
| 實驗 UI                                                                 | 全部                     |
| Jupyter，JupyterLab 整合                                                    | 全部                     |
| Python SDK 支援                                                                 | 全部                     |
| R SDK 支援                                                                      | 全部                     |
| ML 管線：在 Python 中建立、執行及發佈                           | 全部                     |
| ML 管線：在 Python 中建立、編輯和刪除已排程的管線執行| 全部                     |
| ML 管線：在 Python SDK 中建立管線端點                                   | 全部                     |
| ML 管線：在 studio (web) 中查看執行詳細資料                                              | 全部                     |
| ML 管線：在設計工具中建立、執行、視覺化及發佈                  | 僅限企業版 |
| ML 管線：在設計工具中建立管線端點 | 僅限企業版 |
| 整合式筆記本的受控計算實例                                 | 全部                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>部署和模型管理功能

| 功能                            | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| Machine Learning 和 Azure ML CLI 的 Azure DevOps 延伸模組                 | 全部                     |
| [事件方格整合](how-to-use-event-grid.md)                                                             | 全部                     |
| 整合 Azure 串流分析與 Azure Machine Learning                       | 全部                     |
| 在 SDK 中建立 ML 管線                                                         | 全部                     |
| 批次推斷                                                                  | 全部                     |
| 以 FPGA 為基礎的硬體加速模型                                             | 全部                     |
| 模型分析                                                                    | 全部                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>安全性、治理及控制功能

| 功能     | 版本                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Azure 角色型存取控制 (AZURE RBAC) ](how-to-assign-roles.md) 支援                                           | 全部                     |
| [虛擬網路 (VNet) ](how-to-enable-virtual-network.md) 支援計算                                         | 全部                     |
| 評分端點驗證                                                    | 全部                     |
| [工作區私用連結](how-to-configure-private-link.md)                                                            | 全部                     |
| 跨工作區的[配額管理](how-to-manage-quotas.md)                                                 | 僅限企業版 |

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Machine Learning [edition 總覽和定價頁面](https://azure.microsoft.com/pricing/details/machine-learning/)中的可用功能。 

了解如何[將基本工作區升級為 Enterprise 版](how-to-manage-workspace.md#upgrade)。 
