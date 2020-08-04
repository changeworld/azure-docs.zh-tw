---
title: 持續部署 Azure Machine Learning 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何持續使用 Azure Machine Learning DevOps 延伸模組來部署模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: c95e1012decf1e3ae4ee3fbab8aae1a4c1a71be9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544569"
---
# <a name="continuously-deploy-models"></a>持續部署模型

本文說明如何在 Azure DevOps 中使用持續部署，以自動檢查已註冊模型的新版本，並將這些新模型推送至生產環境。

## <a name="prerequisites"></a>先決條件

本文假設您已在 Azure Machine Learning 工作區中註冊模型。 如需定型和註冊 scikit-learn 學習模型的範例，請參閱[本教學](how-to-train-scikit-learn.md)課程。

## <a name="continuously-deploy-models"></a>持續部署模型

您可以使用[Azure DevOps](https://azure.microsoft.com/services/devops/)的 Machine Learning 延伸模組，持續部署模型。 在 Azure Machine Learning 工作區中註冊新的機器學習模型時，您可以使用 Azure DevOps 的 Machine Learning 擴充功能來觸發部署管線。

1. 註冊[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)，讓您的應用程式持續整合並傳遞至任何平臺或雲端。 （請注意，Azure Pipelines 與[Machine Learning 管線](concept-ml-pipelines.md#compare)不同）。

1. [建立 Azure DevOps 專案。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. 安裝[Azure Pipelines 的 Machine Learning 延伸](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)模組。

1. 使用服務連線來設定與您 Azure Machine Learning 工作區的服務主體連線，以便您可以存取您的構件。 移至 [專案設定]，選取 [**服務連接**]，然後選取 [ **Azure Resource Manager**：

    [![選取 Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. 在 [**範圍層級**] 清單中，選取 [ **AzureMLWorkspace**]，然後輸入其餘的值：

    ![選取 AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. 若要使用 Azure Pipelines 持續部署機器學習模型，請在 [管線] 底下，選取 [**發行**]。 新增 [成品]，然後選取 [ **AzureML 模型**成品] 和您稍早建立的服務連接。 選取要觸發部署的模型和版本：

    [![選取 AzureML 模型](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. 在您的模型成品上啟用模型觸發程式。 當您開啟觸發程式時，每次在工作區中註冊該模型的指定版本（也就是最新版本）時，就會觸發 Azure DevOps 發行管線。

    [![啟用模型觸發程式](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>後續步驟

請查看 GitHub 上的下列專案，以取得 ML 模型持續部署的更多範例。

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)