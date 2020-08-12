---
title: 部署模型的方式和位置
titleSuffix: Azure Machine Learning
description: 瞭解部署 Azure Machine Learning 模型的方式和位置，包括 Azure 容器實例、Azure Kubernetes Service、Azure IoT Edge 和可現場程式化閘道陣列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 439bf51aac4d04ee12968588a4e38c4643b4808a
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120335"
---
# <a name="deploy-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何將您的機器學習模型部署為 Azure 雲端中的 web 服務，或 Azure IoT Edge 裝置。

無論部署模型的位置為何，工作流程都十分類似：

1. 註冊模型。
1. 準備推斷設定
1. 除非使用[無程式碼部署，](how-to-deploy-no-code-deployment.md)否則 (準備專案腳本) 
1. 將模型部署至計算目標。
1. 測試已部署的模型，也稱為 web 服務。

如需部署工作流程中相關概念的詳細資訊，請參閱[使用 Azure Machine Learning 來管理、部署和監視模型](concept-model-management-and-deployment.md)。


::: zone pivot="py-sdk"

[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]

::: zone-end

::: zone pivot="cli"

[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]

::: zone-end

### <a name="understanding-service-state"></a>瞭解服務狀態

在模型部署期間，您可能會在完全部署時看到服務狀態變更。

下表描述不同的服務狀態：

| Webservice 狀態 | 描述 | 最終狀態？
| ----- | ----- | ----- |
| 正在 | 服務正在進行部署。 | 否 |
| Unhealthy | 服務已部署，但目前無法連線。  | 否 |
| 設無法排程 | 因為缺少資源，所以目前無法部署服務。 | 否 |
| 失敗 | 因為發生錯誤或損毀，所以服務無法部署。 | 是 |
| Healthy | 服務狀況良好，且端點可供使用。 | 是 |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>批次推斷
Azure Machine Learning 計算目標是由 Azure Machine Learning 建立和管理。 它們可以用於 Azure Machine Learning 管線的批次預測。

如需使用 Azure Machine Learning 計算進行批次推斷的逐步解說，請參閱[如何執行批次預測](tutorial-pipeline-batch-scoring-classification.md)。

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge 推斷
支援部署至 edge 目前處於預覽階段。 如需詳細資訊，請參閱[將 Azure Machine Learning 部署為 IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)。


## <a name="next-steps"></a>後續步驟

* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [更新 Web 服務](how-to-deploy-update-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)

