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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122573"
---
| 計算目標 | 用於 | GPU 支援 | FPGA 支援 | 描述 |
| ----- | ----- | ----- | ----- | ----- |
| [本地&nbsp;&nbsp;Web 服務](../articles/machine-learning/how-to-deploy-and-where.md#local) | 測試/調試 | &nbsp; | &nbsp; | 用於有限的測試和故障排除。 硬體加速取決於在本地系統中使用庫。
| [Azure 機器學習計算實例&nbsp;&nbsp;Web 服務](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | 測試/調試 | &nbsp; | &nbsp; | 用於有限的測試和故障排除。
| [Azure 庫伯奈斯服務 （AKS）](../articles/machine-learning/how-to-deploy-and-where.md#aks) | 即時推理 |  [是](../articles/machine-learning/how-to-deploy-inferencing-gpus.md)（Web 服務部署） | [是](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |用於大規模生產部署。 提供快速回應時間和部署服務自動縮放。 Azure 機器學習 SDK 不支援群集自動縮放。 要更改 AKS 群集中的節點，請使用 Azure 門戶中的 AKS 群集的 UI。 AKS 是設計人員唯一可用的選項。 |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | 測試或開發 | &nbsp;  | &nbsp; | 用於需要少於 48 GB RAM 的低規模基於 CPU 的工作負載。 |
| [Azure Machine Learning 計算叢集](../articles/machine-learning/how-to-use-parallel-run-step.md) | （預覽版）批次處理&nbsp;推理 | [是](../articles/machine-learning/how-to-use-parallel-run-step.md)（機器學習管道） | &nbsp;  | 在無伺服器計算上運行批次處理評分。 支援正常和低優先順序 VM。 |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | （預覽版）即時推理 | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | （預覽版）物聯網&nbsp;模組 |  &nbsp; | &nbsp; | 在 IoT 設備上部署和提供 ML 模型。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | 通過 IoT 邊緣 |  &nbsp; | 是 | 在 IoT 設備上部署和提供 ML 模型。 |

> [!NOTE]
> 儘管本地、Azure 機器學習計算實例和 Azure 機器學習計算群集等計算目標支援 GPU 進行培訓和實驗，但僅在 Azure Kubernetes 服務上支援在__部署為 Web 服務時__使用 GPU 進行推理。
>
> 僅當__使用機器學習管道進行評分時__，才支援使用 GPU 進行推理。