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
ms.date: 08/23/2019
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122573"
---
| 計算目標 | 用途 | GPU 支援 | FPGA 支援 | 說明 |
| ----- | ----- | ----- | ----- | ----- |
| [本機&nbsp;web&nbsp;服務](../articles/machine-learning/how-to-deploy-and-where.md#local) | 測試/調試 | &nbsp; | &nbsp; | 用於有限的測試和疑難排解。 硬體加速取決於使用本機系統中的程式庫。
| [Azure Machine Learning 計算實例&nbsp;web&nbsp;服務](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | 測試/調試 | &nbsp; | &nbsp; | 用於有限的測試和疑難排解。
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | 即時推斷 |  [是](../articles/machine-learning/how-to-deploy-inferencing-gpus.md)（web 服務部署） | [是](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |用於大規模生產環境部署。 提供已部署服務的快速回應時間和自動調整。 不支援透過 Azure Machine Learning SDK 進行叢集自動調整。 若要變更 AKS 叢集中的節點，請在 Azure 入口網站中使用 AKS 叢集的 UI。 AKS 是唯一適用于設計工具的選項。 |
| [Azure 容器執行個體](../articles/machine-learning/how-to-deploy-and-where.md#aci) | 測試或開發 | &nbsp;  | &nbsp; | 針對需要少於 48 GB RAM 的低規模 CPU 型工作負載，使用。 |
| [Azure Machine Learning 計算叢集](../articles/machine-learning/how-to-use-parallel-run-step.md) | 預覽批&nbsp;次推斷 | [是](../articles/machine-learning/how-to-use-parallel-run-step.md)（機器學習管線） | &nbsp;  | 在無伺服器計算上執行批次評分。 支援一般和低優先順序的 Vm。 |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | 預覽即時推斷 | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | 預覽IoT&nbsp;模組 |  &nbsp; | &nbsp; | 在 IoT 裝置上部署和提供 ML 模型。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | 是 | 在 IoT 裝置上部署和提供 ML 模型。 |

> [!NOTE]
> 雖然計算目標（例如本機、Azure Machine Learning 計算實例和 Azure Machine Learning 計算叢集）支援用於定型和實驗的 GPU，但__在部署為 web 服務時__，只有在 Azure Kubernetes Service 上才支援使用 gpu 進行推斷。
>
> 只有在 Azure Machine Learning 計算上才支援使用__機器學習管線進行評分時__，使用 GPU 進行推斷。