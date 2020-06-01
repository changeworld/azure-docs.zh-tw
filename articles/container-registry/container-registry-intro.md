---
title: 受控容器登錄
description: Azure 容器登錄庫服務的簡介，此服務提供雲端式管理的私人 Docker Registry。
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 40a1d75ff90efafff14cd27ab439df8ab3729c50
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674312"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure 中的私人 Docker 容器登錄的簡介

Azure Container Registry 是受控的私人 Docker 登錄服務，架構於開放原始碼的 Docker Registry 2.0。 建立及維護 Azure 容器登錄庫，以儲存和管理您的私人 Docker 容器映像和相關成品。

使用 Azure 容器登錄與現有容器的開發與部署管線，或使用 Azure Container Registry 工作來建置 Azure 中的容器映像。 視需求建置，或是使用原始程式碼認可和基礎映像更新等觸發程序來完全自動化地進行建置。

如需 Docker 和登錄概念的詳細資訊，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/)和[關於登錄、存放庫和映像](container-registry-concepts.md)。

## <a name="use-cases"></a>使用案例

從 Azure 容器登錄庫將映像提取到不同部署目標︰

* **可調整的協調流程系統**，它會管理整個主機叢集上的容器化應用程式，包括 [Kubernetes](https://kubernetes.io/docs/)、[DC/OS](https://docs.mesosphere.com/) 與 [Docker Swarm](https://docs.docker.com/swarm/)。
* **Azure 服務**，會支援依規模建置和執行的應用程式，包括 [Azure Kubernetes Service (AKS)](../aks/index.yml)[App Service](../app-service/index.yml)[Batch](../batch/index.yml)[Service Fabric](/azure/service-fabric/) 和其他應用程式。

開發人員也可以將推送到容器登錄庫，當做容器開發工作流程的一部分。 例如，從 [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) 或 [Jenkins](https://jenkins.io/) 等持續整合與傳遞工具中，將容器登錄設定為目標。

設定 ACR 工作，在應用程式基礎映像更新時，自動重建應用程式映像，或在您的小組將程式碼認可至 Git 存放庫時，自動建置映像。 建立多步驟工作，在雲端中平行地自動建置、測試及修補多個容器映像。

Azure 會提供工具 (例如 Azure 命令列介面、Azure 入口網站和 API 支援) 來管理您的 Azure 容器登錄。 您可以選擇性地安裝[適用於 Visual Studio Code 的 Docker 擴充功能](https://code.visualstudio.com/docs/azure/docker)和 [Azure 帳戶](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)擴充功能，來搭配 Azure 容器登錄使用。 向 Azure 容器登錄提取及推送映像，或是執行 ACR 工作，都可以在 Visual Studio Code 內完成。

## <a name="key-features"></a>主要功能

* **登錄服務層** - 在您的 Azure 訂用帳戶中建立一或多個容器登錄。 登錄可以三個層次提供：[基本、標準和進階](container-registry-skus.md)，每個 SKU 都支援 Webhook 整合、使用 Azure Active Directory 的登錄驗證，以及刪除功能。 在與您的部署相同的 Azure 位置建立登錄，以利用容器映像接近網路的本機儲存體。 將進階登錄庫的[異地複寫](container-registry-geo-replication.md)功能用於進階複寫和容器映像散發案例。 

* **安全性和存取權** - 使用 Azure CLI 或標準 `docker login` 命令登入登錄。 Azure Container Registry 會透過 HTTPS 傳輸容器映像並支援 TLS 以保護用戶端連線。 

  > [!IMPORTANT]
  > 從 2020 年 1 月 13 日開始，Azure Container Registry 將要求所有安全連線來自伺服器和應用程式，才能使用 TLS 1.2。 使用任何最近的 docker 用戶端 (18.03.0 版或更新版本) 來啟用 TLS 1.2。 TLS 1.0 和 1.1 的支援將會淘汰。 

  您可以使用採用 Azure Active Directory 的[服務主體](../active-directory/develop/app-objects-and-service-principals.md)或提供的管理員帳戶，對容器登錄進行[存取控制](container-registry-authentication.md)。 使用角色型存取控制 (RBAC)，將使用者或系統的細微權限指派給登錄。

  進階服務層的安全性功能包括可供映像標籤簽署的[內容信任](container-registry-content-trust.md)，以及[防火牆和虛擬網路 (預覽)](container-registry-vnet.md)可限制登錄的存取權。 Azure 資訊安全中心可以選擇性地與 Azure Container Registry 整合，以便在每次將映像推送至登錄時[掃描映像](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)。

* **支援的映像和成品** - 群組在存放庫中，每個映像是 Docker 相容容器的唯讀快照集。 Azure 容器登錄庫可以包含 Windows 和 Linux 映像。 您可以控制您的所有容器部署的映像名稱。 使用標準 [Docker 命令](https://docs.docker.com/engine/reference/commandline/) 將映像推送到儲存機制，或從儲存機制提取映像。 除了 Docker 容器映像外，Azure Container Registry 還會將[相關的內容格式](container-registry-image-formats.md) (例如所建置的 [Helm 圖表](container-registry-helm-repos.md)和映像) 儲存到 [Open Container Initiative (OCI) 映像格式規格](https://github.com/opencontainers/image-spec/blob/master/spec.md)。

* **自動化映像組建** - 使用 [Azure Container Registry 工作](container-registry-tasks-overview.md) (ACR 工作) 來簡化 Azure 中的映像建置、測試、推送和部署。 例如，使用 ACR 工作將 `docker build` 作業卸載至 Azure，讓您的開發內部迴圈延伸到雲端。 設定建置工作以自動化您的容器作業系統與架構修補管線，並在您的小組將程式碼認可至來源控制項時自動建置影像。

  [多步驟工作](container-registry-tasks-overview.md#multi-step-tasks)提供適用於在雲端建置、測試及修補容器映像的步驟型工作定義與執行。 工作步驟會定義個別的容器映像建置和推送作業。 它們也可以定義一或多個容器的執行，其中每個步驟都使用容器作為其執行環境。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 入口網站建立容器登錄庫](container-registry-get-started-portal.md)
* [使用 Azure CLI 建立容器登錄庫](container-registry-get-started-azure-cli.md)
* [使用 ACR 工作來自動建置和維護容器](container-registry-tasks-overview.md)