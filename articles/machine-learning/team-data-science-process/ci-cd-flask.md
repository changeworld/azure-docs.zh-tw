---
title: 使用 Azure Pipelines 建立 CI/CD 管線-Team Data 科學流程
description: 使用 Docker 和 Kubernetes 為人工智慧 (AI) 應用程式建立持續整合和持續傳遞管線。
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
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309607"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>使用 Azure Pipelines、Docker 和 Kubernetes 建立 AI 應用程式的 CI/CD 管線

人工智慧 (AI) 應用程式是以預先定型機器學習 (ML) 模型內嵌的應用程式程式碼。 AI 應用程式一律會有兩個工作流：資料科學家會建立 ML 模型，而應用程式開發人員會建立應用程式，並將其公開給終端使用者使用。 本文說明如何針對將 ML 模型內嵌至應用程式原始程式碼的 AI 應用程式，執行持續整合和持續傳遞 (CI/CD) 管線。 範例程式碼和教學課程使用 Python Flask web 應用程式，並從私人 Azure blob 儲存體帳戶提取預先定型模型。 您也可以使用 AWS S3 儲存體帳戶。

> [!NOTE]
> 下列程式是執行 CI/CD 的數種方式之一。 這項工具和必要條件有一些替代方案。

## <a name="source-code-tutorial-and-prerequisites"></a>原始程式碼、教學課程和必要條件

您可以從 GitHub 下載 [原始程式碼](https://github.com/Azure/DevOps-For-AI-Apps) 和 [詳細教學](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) 課程。 遵循教學課程步驟，為您自己的應用程式執行 CI/CD 管線。

若要使用下載的原始程式碼和教學課程，您需要下列必要條件： 

- [原始程式碼存放庫](https://github.com/Azure/DevOps-For-AI-Apps)派生至您的 GitHub 帳戶
- [Azure DevOps 的組織](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Kubernetes (AKS) ](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)叢集的 Azure Container Service
- 從 AKS 叢集中執行命令及提取設定的[Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
- [Azure Container Registry (ACR) 帳戶](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>CI/CD 管線摘要

每個新的 Git 認可都會啟動組建管線。 組建會從 blob 儲存體帳戶安全地提取最新的 ML 模型，並將其封裝在單一容器中的應用程式程式碼。 這項應用程式開發和資料科學工作流的分離，可確保生產應用程式一律以最新的 ML 模型執行最新的程式碼。 如果應用程式通過測試，管線會將組建映射安全地儲存在 ACR 的 Docker 容器中。 然後，發行管線會使用 AKS 來部署容器。 

## <a name="cicd-pipeline-steps"></a>CI/CD 管線步驟

下圖和步驟說明 CI/CD 管線架構：

![CI/CD 管線架構](./media/ci-cd-flask/architecture.png)

1. 開發人員會在其選擇的 IDE 中處理應用程式程式碼。
2. 開發人員會將程式碼認可至 Azure Repos、GitHub 或其他 Git 原始檔控制提供者。 
3. 資料科學家會分開開發其 ML 模型。
4. 資料科學家將完成的模型發佈至模型存放庫，在此案例中為 blob 儲存體帳戶。 
5. Azure Pipelines 根據 Git 認可啟動組建。
6. 組建管線會從 blob 儲存體提取最新的 ML 模型，並建立容器。
7. 管線會將組建映射推送至 ACR 中的私用映射存放庫。
8. 發行管線會根據成功的組建開始。
9. 管線會從 ACR 提取最新的映射，並將其部署到 AKS 上的 Kubernetes 叢集中。
10. 應用程式的使用者要求會經過 DNS 伺服器。
11. DNS 伺服器會將要求傳遞至負載平衡器，並將回應傳回給使用者。

## <a name="see-also"></a>另請參閱

- [Team Data Science Process (TDSP)](./index.yml)
- [Azure Machine Learning (AML)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)