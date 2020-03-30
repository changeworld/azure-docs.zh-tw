---
title: 支援的內容格式
description: 瞭解 Azure 容器註冊表支援的內容格式，包括與 Docker 相容的容器映射、Helm 圖表、OCI 圖像和 OCI 專案。
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247003"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry 中支援的內容格式

使用 Azure Container Registry 中的私人存放庫，可管理其下列中一種內容格式。 

## <a name="docker-compatible-container-images"></a>與 Docker 相容的容器映像

支援以下 Docker 容器映射格式：

* [Docker 映像資訊清單 V2，結構描述 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 映像資訊清單 V2，結構描述 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -包含允許登錄將多平台映像儲存在單一 "image:tag" 參考下的資訊清單

## <a name="oci-images"></a>OCI 圖像

Azure 容器註冊表支援滿足[開放容器計畫 （OCI） 映射格式規範的圖像](https://github.com/opencontainers/image-spec/blob/master/spec.md)。 包裝格式包括[奇點圖像格式 （SIF）。](https://github.com/sylabs/sif)

## <a name="oci-artifacts"></a>OCI 文物

Azure 容器註冊表支援[OCI 分發規範](https://github.com/opencontainers/distribution-spec)，這是一個供應商中立的、與雲無關的規範，用於存儲、共用、保護和部署容器映射和其他內容類型（工件）。 該規範允許註冊表存儲除容器映射之外的各種工件。 您可以使用適合工件的工具來推送和拉取工件。 例如，請參閱使用[Azure 容器註冊表推送和拉取 OCI 專案](container-registry-oci-artifacts.md)。

要瞭解有關 OCI 工件的更多，請參閱[OCI 註冊表作為存儲 （ORAS）](https://github.com/deislabs/oras)存儲庫和 GitHub 上的[OCI 工件](https://github.com/opencontainers/artifacts)存儲庫。

## <a name="helm-charts"></a>Helm 圖表

Azure 容器註冊表可以承載[Helm 圖表](https://helm.sh/)的存儲庫，這是一種打包格式，用於快速管理和部署 Kubernetes 的應用程式。 [支援 Helm 用戶端](https://docs.helm.sh/using_helm/#installing-helm)版本 2（2.11.0 或更高版本）。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 Azure Container Registry 來[推送和提取](container-registry-get-started-docker-cli.md)映像。

* 使用 [ACR 工作](container-registry-tasks-overview.md)建置和測試容器映像。 

* 使用 [Moby BuildKit](https://github.com/moby/buildkit) 建置和封裝 OCI 格式的容器。

* 設定裝載在 Azure Container Registry 中的 [Helm 存放庫](container-registry-helm-repos.md)。 


