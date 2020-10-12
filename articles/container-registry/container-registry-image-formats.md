---
title: 支援的內容格式
description: 瞭解 Azure Container Registry 所支援的內容格式，包括與 Docker 相容的容器映射、Helm 圖表、OCI 影像和 OCI 成品。
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84695261"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry 中支援的內容格式

使用 Azure Container Registry 中的私人存放庫，可管理其下列中一種內容格式。 

## <a name="docker-compatible-container-images"></a>與 Docker 相容的容器映像

支援下列 Docker 容器映射格式：

* [Docker 映像資訊清單 V2，結構描述 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 映像資訊清單 V2，結構描述 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -包含允許登錄將多平台映像儲存在單一 "image:tag" 參考下的資訊清單

## <a name="oci-images"></a>OCI 映射

Azure Container Registry 支援符合 [Open 容器方案的映射 (OCI) 映射格式規格](https://github.com/opencontainers/image-spec/blob/master/spec.md)。 封裝格式包括 [Singularity 映射格式 (SIF) ](https://github.com/sylabs/sif)。

## <a name="oci-artifacts"></a>OCI 構件

Azure Container Registry 支援 [OCI 散發規格](https://github.com/opencontainers/distribution-spec)，此為廠商中立、雲端中立的規格，可儲存、共用、保護和部署容器映射和其他內容類型 (成品) 。 此規格可讓登錄區除了容器映射之外，還能儲存各種不同的構件。 您可以使用適用于構件的工具來推送和提取成品。 如需範例，請參閱 [使用 Azure container Registry 推送和提取 OCI](container-registry-oci-artifacts.md)成品。

若要深入瞭解 OCI 成品，請參閱 [OCI Registry 作為儲存體 (ORAS) 存放 ](https://github.com/deislabs/oras) 庫和 GitHub 上的 [OCI 構件](https://github.com/opencontainers/artifacts) 存放庫。

## <a name="helm-charts"></a>Helm 圖表

Azure Container Registry 可以裝載 [Helm 圖](https://helm.sh/)的存放庫，這是用來快速管理及部署 Kubernetes 應用程式的封裝格式。 支援[Helm 用戶端](https://docs.helm.sh/using_helm/#installing-helm)第2版 (2.11.0 版或更新版本的) 。

## <a name="next-steps"></a>接下來的步驟

* 了解如何使用 Azure Container Registry 來[推送和提取](container-registry-get-started-docker-cli.md)映像。

* 使用 [ACR 工作](container-registry-tasks-overview.md)建置和測試容器映像。 

* 使用 [Moby BuildKit](https://github.com/moby/buildkit) 建置和封裝 OCI 格式的容器。

* 設定裝載在 Azure Container Registry 中的 [Helm 存放庫](container-registry-helm-repos.md)。 


