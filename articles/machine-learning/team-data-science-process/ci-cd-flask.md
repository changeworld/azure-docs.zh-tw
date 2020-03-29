---
title: 使用 Azure 管道創建 CI/CD 管道 - 團隊資料科學流程
description: 使用 Docker 和 Kubernetes 為人工智慧 （AI） 應用程式創建持續集成和連續交付管道。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721824"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>使用 Azure 管道、Docker 和庫伯奈斯為 AI 應用創建 CI/CD 管道

人工智慧 （AI） 應用程式是嵌入預訓練的機器學習 （ML） 模型的應用程式代碼。 AI 應用程式總是有兩個工作流：資料科學家構建 ML 模型，應用開發人員構建應用並將其公開給最終使用者使用。 本文介紹如何為將 ML 模型嵌入到應用原始程式碼中的 AI 應用程式實現持續集成和持續交付 （CI/CD） 管道。 示例代碼和教程使用 Python Flask Web 應用程式，並從專用 Azure Blob 存儲帳戶獲取預訓練的模型。 您還可以使用 AWS S3 存儲帳戶。

> [!NOTE]
> 以下過程是執行 CI/CD 的幾種方法之一。 此工具和先決條件有替代方法。

## <a name="source-code-tutorial-and-prerequisites"></a>原始程式碼、教程和先決條件

您可以從 GitHub 下載[原始程式碼](https://github.com/Azure/DevOps-For-AI-Apps)和[詳細教程](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)。 按照教程步驟為您自己的應用程式實現 CI/CD 管道。

要使用下載的原始程式碼和教程，您需要以下先決條件： 

- 已分到 GitHub 帳戶的[原始程式碼存儲庫](https://github.com/Azure/DevOps-For-AI-Apps)
- [Azure DevOps 組織](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [庫伯奈斯 （AKS） 群集的 Azure 容器服務](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [庫布特爾](https://kubernetes.io/docs/tasks/tools/install-kubectl/)運行命令並從 AKS 群集獲取配置 
- [Azure 容器註冊表 （ACR） 帳戶](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD 管道摘要

每個新的 Git 提交都會啟動生成管道。 生成安全地從 Blob 存儲帳戶中提取最新的 ML 模型，並將其與應用代碼打包到單個容器中。 應用程式開發和資料科學工作流的這種分離可確保生產應用始終使用最新的 ML 模型運行最新的代碼。 如果應用通過測試，管道將生成映射安全地存儲在 ACR 中的 Docker 容器中。 然後，發佈管道使用 AKS 部署容器。 

## <a name="cicd-pipeline-steps"></a>CI/CD 管道步驟

下圖和步驟描述了 CI/CD 管道體系結構：

![CI/CD 管道架構](./media/ci-cd-flask/architecture.png)

1. 開發人員在其選擇的 IDE 中處理應用程式代碼。
2. 開發人員將代碼提交到 Azure 存儲庫、GitHub 或其他 Git 原始程式碼管理提供程式。 
3. 另外，資料科學家致力於開發他們的ML模型。
4. 資料科學家將已完成的模型發佈到模型存儲庫，在這種情況下，是 blob 存儲帳戶。 
5. Azure 管道啟動基於 Git 提交構建。
6. 生成管道從 Blob 存儲中提取最新的 ML 模型，並創建一個容器。
7. 管道將生成映射推送到 ACR 中的私有映射存儲庫。
8. 發佈管道根據成功的生成開始。
9. 管道從 ACR 提取最新映射，並將其部署在 AKS 上的 Kubernetes 群集中。
10. 應用的使用者請求通過 DNS 伺服器。
11. DNS 伺服器將請求傳遞給負載等化器，並將回應發送回使用者。

## <a name="see-also"></a>另請參閱

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes)
