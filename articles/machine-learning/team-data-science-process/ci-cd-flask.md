---
title: 使用 Azure Pipelines 建立 CI/CD 管線-小組資料科學流程
description: 使用 Docker 和 Kubernetes 建立人工智慧（AI）應用程式的持續整合與持續傳遞管線。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721824"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>使用 Azure Pipelines、Docker 和 Kubernetes 建立適用于 AI 應用程式的 CI/CD 管線

人工智慧（AI）應用程式是以預先定型機器學習（ML）模型內嵌的應用程式程式碼。 AI 應用程式一律會有兩個工作流：資料科學家會建立 ML 模型，而應用程式開發人員會建立應用程式，並將它公開給使用者使用。 本文說明如何針對將 ML 模型內嵌至應用程式原始程式碼的 AI 應用程式，執行持續整合和持續傳遞（CI/CD）管線。 範例程式碼和教學課程會使用 Python Flask web 應用程式，並從私人 Azure blob 儲存體帳戶提取預先定型模型。 您也可以使用 AWS S3 儲存體帳戶。

> [!NOTE]
> 下列程式是執行 CI/CD 的幾種方式之一。 此工具和必要條件都有替代方案。

## <a name="source-code-tutorial-and-prerequisites"></a>原始程式碼、教學課程和必要條件

您可以從 GitHub 下載[原始程式碼](https://github.com/Azure/DevOps-For-AI-Apps)和[詳細的教學](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)課程。 遵循教學課程步驟，為您自己的應用程式執行 CI/CD 管線。

若要使用已下載的原始程式碼和教學課程，您需要下列必要條件： 

- [原始程式碼存放庫](https://github.com/Azure/DevOps-For-AI-Apps)會分支到您的 GitHub 帳戶
- [Azure DevOps 組織](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Kubernetes （AKS）](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)叢集的 Azure Container Service
- 從 AKS 叢集執行命令和提取設定的[Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
- [Azure Container Registry （ACR）帳戶](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD 管線摘要

每個新的 Git 認可都會啟動組建管線。 組建會從 blob 儲存體帳戶安全地提取最新的 ML 模型，並使用單一容器中的應用程式程式碼來封裝它。 這種分離應用程式開發和資料科學工作，可確保生產應用程式一律以最新的 ML 模型執行最新的程式碼。 如果應用程式通過測試，管線會將組建映射安全地儲存在 ACR 的 Docker 容器中。 然後，發行管線會使用 AKS 來部署容器。 

## <a name="cicd-pipeline-steps"></a>CI/CD 管線步驟

下列圖表和步驟說明 CI/CD 管線架構：

![CI/CD 管線架構](./media/ci-cd-flask/architecture.png)

1. 開發人員在其選擇的 IDE 中處理應用程式程式碼。
2. 開發人員會將程式碼認可至 Azure Repos、GitHub 或其他 Git 原始檔控制提供者。 
3. 資料科學家會分別開發其 ML 模型。
4. 資料科學家會將完成的模型發行至模型存放庫，在此案例中為 blob 儲存體帳戶。 
5. Azure Pipelines 根據 Git 認可來啟動組建。
6. 組建管線會從 blob 儲存體提取最新的 ML 模型，並建立容器。
7. 管線會將組建映射推送至 ACR 中的私用映射存放庫。
8. 發行管線會根據成功的組建來啟動。
9. 管線會從 ACR 提取最新的映射，並將其部署到 AKS 上的 Kubernetes 叢集。
10. 應用程式的使用者要求會經過 DNS 伺服器。
11. DNS 伺服器會將要求傳遞給負載平衡器，並將回應傳回給使用者。

## <a name="see-also"></a>請參閱

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes)
