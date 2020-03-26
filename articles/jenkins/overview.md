---
title: Jenkins 與 Azure 的概觀
description: 在 Azure 中裝載 Jenkins 組建和部署自動化伺服器，以及使用 Azure 計算和儲存體資源來擴充您的持續整合和部署 (CI/CD) 管線。
keywords: jenkins, azure, devops, 概觀
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: a9297ebc116d75cfe1d4f37d4e9ada7d5198beae
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77620174"
---
# <a name="azure-and-jenkins"></a>Azure 與 Jenkins

[Jenkins](https://jenkins.io/) 是熱門的開放原始碼自動化伺服器，用來設定軟體專案的持續整合與傳遞 (CI/CD)。 您可以將 Jenkins 部署裝載在 Azure 中，或是使用 Azure 資源來延伸現有的 Jenkins 組態。 Jenkins 外掛程式也可用來簡化您的應用程式對 Azure 的 CI/CD。

本文介紹如何使用 Azure 搭配 Jenkins，並詳述可供 Jenkins 使用者使用的核心 Azure 功能。 如需如何開始在 Azure 中自行使用 Jenkins 伺服器的詳細資訊，請參閱[在 Azure 上建立 Jenkins 伺服器](install-jenkins-solution-template.md)。

## <a name="host-your-jenkins-servers-in-azure"></a>在 Azure 中裝載 Jenkins 伺服器

在 Azure 中裝載 Jenkins，可依照軟體專案的成長需求，集中管理您的組建自動化及調整您的部署。 您可以使用下列各項，在 Azure 中部署 Jenkins：
 
- Azure Marketplace 中的 [Jenkins 方案範本](install-jenkins-solution-template.md)。
- [Azure 虛擬機器](/azure/virtual-machines/linux/overview)。 請參閱我們的[教學課程](tutorial-jenkins-github-docker-cicd.md)以在 VM 上建立 Jenkins 執行個體。
- 在 [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough) 中執行的 Kubernetes 叢集上，請參閱我們的[作法](/azure/container-service/kubernetes/container-service-kubernetes-jenkins)。

使用 [Azure 監視器記錄](/azure/log-analytics/log-analytics-overview)和 [Azure CLI](/cli/azure) 來監視和管理 Azure Jenkins 部署。

## <a name="scale-your-build-automation-on-demand"></a>依照需求調整您的組建自動化

將組建代理程式新增至現有的 Jenkins 部署，以隨著組建數目以及您的作業和管線複雜度增加，調整您的 Jenkins 組建容量。 您可以使用 [Azure VM 代理程式外掛程式](https://plugins.jenkins.io/azure-vm-agents)，在 Azure 虛擬機器上執行這些組建代理程式。 如需詳細資訊，請參閱我們的[教學課程](/azure/jenkins/jenkins-azure-vm-agents)。

一旦使用 [Azure 服務主體](/azure/azure-resource-manager/resource-group-overview)進行設定，Jenkins 作業和管線即可使用此認證來：

- 使用 [Azure 儲存體外掛程式](/azure/storage/common/storage-introduction)，在 [Azure 儲存體](https://plugins.jenkins.io/windows-azure-storage)中安全地儲存和封存組建成品。 檢閱 [Jenkins 儲存體作法](storage-java-jenkins-continuous-integration-solution.md)，以了解詳細資訊。
- 使用 [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline) 來管理和設定 Azure 資源。

## <a name="deploy-your-code-into-azure-services"></a>將您的程式碼部署至 Azure 服務

使用 Jenkins 外掛程式，將您的應用程式部署至 Azure，成為 Jenkins CI/CD 管線的一部分。 部署至 [Azure App Service](/azure/app-service/) 和 [Azure Container Service](/azure/container-service/kubernetes/)，可讓您預備、測試和發行您應用程式的更新，而不需管理基礎結構。

 外掛程式可供部署到下列服務和環境：

- [Linux 上的 Azure App Service](/azure/app-service/containers/app-service-linux-intro)。 請參閱[教學課程](java-deploy-webapp-tutorial.md)以便開始使用。
- [Azure App Service](/azure/app-service/overview)。 請參閱[作法](deploy-Jenkins-app-service-plugin.md)以便開始使用。
