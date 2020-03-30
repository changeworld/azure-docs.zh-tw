---
title: 關於&圖像的存儲庫
description: Azure 容器註冊表、存儲庫和容器映射的關鍵概念簡介。
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: ea6e2577d3eee91626dd613617a0b79e4ff3d6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247055"
---
# <a name="about-registries-repositories-and-images"></a>關於註冊表、存儲庫和圖像

本文介紹了容器註冊表、存儲庫、容器映射和相關工件的關鍵概念。 

## <a name="registry"></a>登錄

容器「登錄」** 是一項服務，可儲存及散發容器映像。 Docker Hub 是一個公共容器註冊表，支援開源社區，並用作映射的一般目錄。 Azure 容器註冊表為使用者提供對其映射的直接控制，集成身份驗證、[地理複製](container-registry-geo-replication.md)支援全域分發和可靠性，用於網路關閉部署、[虛擬網路和防火牆配置](container-registry-vnet.md)、[標記鎖定](container-registry-image-lock.md)和許多其他增強功能。 

除了 Docker 容器映射外，Azure 容器註冊表還支援相關[內容專案，](container-registry-image-formats.md)包括開放容器計畫 （OCI） 映射格式。

## <a name="content-addressable-elements-of-an-artifact"></a>專案的內容可定址元素

Azure 容器註冊表中專案的位址包括以下元素。 

`[loginUrl]/[namespace]/[artifact:][tag]`

* **loginUrl** - 註冊表主機的完全限定名稱。 Azure 容器註冊表中的註冊表主機的格式為*myregistry*.azurecr.io（所有小寫）。 使用 Docker 或其他用戶端工具將專案拉取或推送到 Azure 容器註冊表時，必須指定登錄 Url。 
* **命名空間**- 相關圖像或工件的斜線分隔邏輯分組 - 例如，對於工作組或應用
* **專案**- 特定圖像或工件的存儲庫的名稱
* **標記**- 存儲在存儲庫中的圖像或專案的特定版本


例如，Azure 容器註冊表中映射的全名可能類似于：

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

有關這些元素的詳細資訊，請參閱以下部分。

## <a name="repository-name"></a>儲存機制名稱

容器註冊表管理*存儲庫*、容器映射集合或其他具有相同名稱但標記不同的專案。 例如，下列三個映像位於 "acr-helloworld" 存放庫中：


- *阿克雷赫世界：最新*
- *阿克-helloworld：v1*
- *阿克雷赫世界：v2*

存放庫名稱也可以包含[命名空間](container-registry-best-practices.md#repository-namespaces)。 命名空間允許您使用前斜杠分隔的存儲庫名稱對圖像進行分組，例如：

- *市場/市場活動10-18/網路：v2*
- *市場/市場活動10-18/api：v3*
- *市場/市場活動10-18/電子郵件發送者：v2*
- *產品退貨/網路提交：20180604*
- *產品退貨/舊集成商：20180715*

## <a name="image"></a>映像

註冊表中的容器映射或其他專案與一個或多個標記相關聯，具有一個或多個圖層，並由清單標識。 瞭解這些元件如何相互關聯可以説明您有效地管理註冊表。

### <a name="tag"></a>Tag

圖像或其他工件的*標記*指定其版本。 存儲庫中的單個專案可以分配一個或多個標記，也可以"解除標記"。 也就是說，您可以從圖像中刪除所有標記，而圖像的資料（其圖層）保留在註冊表中。

存放庫 (或存放庫和命名空間) 以及標記可定義映像的名稱。 您可以在推送或提取作業中指定映像名稱，以推送和提取映像。

如何標記容器映射由方案指導以開發或部署它們。 例如，建議為維護基本映射而使用穩定標記，並為部署映射提供唯一標記。 有關詳細資訊，請參閱[標記和版本控制容器映射的建議](container-registry-image-tag-version.md)。

### <a name="layer"></a>階層

容器映射由一個或多個圖層組成，每個*圖層*對應于定義圖像的 Dockerfile 中的一行。 登錄中的映像會共用常見層次，以提高儲存效率。 例如，不同存放庫中的數個映像可能會共用相同的 Alpine Linux 基底層次，但是該層次只有一個複本會儲存在登錄中。

層次共用也可將層次分布最佳化，使其分布於有多個映像共用常見層次的節點。 例如，如果節點上現有的映像包含 Alpine Linux 層次作為其基底，則參考同一層的不同映像在後續提取中不會將層移轉到節點。 相反地，它會參考已存在於節點上的層次。

為了提供安全隔離和保護，防止潛在的層操作，層不會在註冊表之間共用。

### <a name="manifest"></a>file:///

推送到容器註冊表的每個容器映射或專案都與*清單*相關聯。 在推送映像時，由登錄所產生的資訊清單，可唯一識別該映像並指定其層次。 您可以使用 Azure CLI 命令 [az acr repository show-manifests][az-acr-repository-show-manifests] 列出存放庫的資訊清單：

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

例如，列出"acr-helloworld"存儲庫的清單：

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>資訊清單摘要

資訊清單是由唯一的 SHA-256 雜湊或「資訊清單摘要」** 識別。 每個圖像或工件（無論是否標記）都由其摘要標識。 即使映像的層次資料與另一個映像的層次資料相同完全，摘要值也會是唯一的。 這個機制可讓您重複將標記相同的映像推送至登錄。 例如，您可以將 `myimage:latest` 重複推送至您的登錄，而不會發生錯誤，因為每個映像都是由其唯一摘要識別。

您可以在推送作業中指定其摘要，以從摘要中提取映像。 有些系統可能會設定為依照摘要提取，因為這可保證所提取的映像版本，即使標記相同的映像隨後推送至登錄亦然。

例如，通過清單摘要從"acr-helloworld"存儲庫中提取圖像：

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> 如果您重複推送具有相同標記且已修改的映像，可以建立孤立映像 (也就是已取消標記的映像)，但仍會耗用您登錄中的空間。 當您依照標記列出或檢視映像時，已取消標記的映像不會顯示在 Azure CLI 或 Azure 入口網站中。 不過，其層次仍然存在，而且會耗用您登錄中的空間。 刪除未標記的圖像將釋放註冊表空間，當清單是唯一的一個或最後一個指向特定圖層的。 有關釋放未標記圖像使用的空間的資訊，請參閱[在 Azure 容器註冊表中刪除容器映射](container-registry-delete.md)。

## <a name="next-steps"></a>後續步驟

在 Azure 容器註冊表中瞭解有關[映射存儲](container-registry-storage.md)和支援[的內容格式](container-registry-image-formats.md)的更多內容格式。

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


