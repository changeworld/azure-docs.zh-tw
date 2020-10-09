---
title: 關於存放庫 & 映射
description: Azure container registry、存放庫及容器映射的重要概念簡介。
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: f3a3e2a00b4fb35f9e9dd1415d5c197aef0d39b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85390443"
---
# <a name="about-registries-repositories-and-images"></a>關於登錄、存放庫和映射

本文介紹容器登錄、存放庫和容器映射以及相關構件的重要概念。 

## <a name="registry"></a>登錄

容器「登錄」** 是一項服務，可儲存及散發容器映像。 Docker Hub 是支援開放原始碼社區的公用容器登錄，可作為影像的一般目錄。 Azure Container Registry 可讓使用者直接控制其映射，並提供整合式驗證、支援全域散發的 [異地](container-registry-geo-replication.md) 複寫、網路關閉部署的可靠性、 [虛擬網路和防火牆](container-registry-vnet.md)設定、 [標記鎖定](container-registry-image-lock.md)，以及許多其他增強功能。 

除了 Docker 容器映射以外，Azure Container Registry 也支援相關的 [內容](container-registry-image-formats.md) 成品，包括 Open container 方案 (OCI) 映射格式。

## <a name="content-addressable-elements-of-an-artifact"></a>成品的內容可定址元素

Azure container registry 中的成品位址包含下列元素。 

`[loginUrl]/[repository:][tag]`

* **loginUrl** -登錄主機的完整名稱。 Azure container registry 中的登錄主機格式為 *myregistry*. azurecr.io (全部小寫) 。 使用 Docker 或其他用戶端工具時，您必須指定 loginUrl，以將構件提取或推送至 Azure container registry。 
* 存放**庫**-一或多個相關映射或構件的邏輯群組名稱，例如，應用程式或基礎作業系統的影像。 可能包含 *命名空間* 路徑。 
* **標記** -儲存在儲存機制中之影像或成品特定版本的識別碼。

例如，Azure container registry 中的映射完整名稱可能如下所示：

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

如需這些元素的詳細資訊，請參閱下列各節。

## <a name="repository-name"></a>儲存機制名稱

存放 *庫* 是容器映射或其他具有相同名稱但不同標記的構件集合。 例如，下列三個映像位於 "acr-helloworld" 存放庫中：


- *acr-helloworld：最新*
- *acr-helloworld： v1*
- *acr-helloworld： v2*

存放庫名稱也可以包含[命名空間](container-registry-best-practices.md#repository-namespaces)。 命名空間可讓您使用正斜線分隔名稱，來識別您組織中的相關存放庫和成品擁有權。 但是，登錄會獨立管理所有存放庫，而不是階層。 例如：

- *marketing/campaign10-18/web： v2*
- *marketing/campaign10-18/api： v3*
- *marketing/campaign10-18/email-寄件者： v2*
- *產品-退貨/web 提交：20180604*
- *產品-退貨/舊版-整合者：20180715*

存放庫名稱只能包含小寫英數位元、句號、連字號、底線和正斜線。 

如需完整的存放庫命名規則，請參閱 [Open Container 方案散發規格](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)。

## <a name="image"></a>映像

登錄中的容器映射或其他成品會與一個或多個標記相關聯，其中有一或多個層級，並由資訊清單識別。 瞭解這些元件彼此之間的關聯性，可協助您有效地管理您的登錄。

### <a name="tag"></a>Tag

影像或其他成品的 *標記* 會指定其版本。 您可以將一個或多個標記指派給存放庫內的單一成品，也可以「未標記」。 也就是說，您可以從映射中刪除所有標記，而映射的資料 (其層級) 保留在登錄中。

存放庫 (或存放庫和命名空間) 以及標記可定義映像的名稱。 您可以在推送或提取作業中指定映像名稱，以推送和提取映像。 `latest`如果您未在 Docker 命令中提供標籤，預設會使用此標記。

您如何標記容器映射，以開發或部署這些映射。 例如，建議使用穩定標籤來維護您的基底映射，以及用來部署映射的唯一標記。 如需詳細資訊，請參閱 [標記和版本設定容器映射的建議](container-registry-image-tag-version.md)。

如需標記命名規則，請參閱 [Docker 檔](https://docs.docker.com/engine/reference/commandline/tag/)。

### <a name="layer"></a>層

容器映射是由一或多個圖層所組成，每個 *圖層*都對應到 Dockerfile 中定義影像的一行。 登錄中的映像會共用常見層次，以提高儲存效率。 例如，不同存放庫中的數個映像可能會共用相同的 Alpine Linux 基底層次，但是該層次只有一個複本會儲存在登錄中。

層次共用也可將層次分布最佳化，使其分布於有多個映像共用常見層次的節點。 例如，如果節點上現有的映像包含 Alpine Linux 層次作為其基底，則參考同一層的不同映像在後續提取中不會將層移轉到節點。 相反地，它會參考已存在於節點上的層次。

為了提供安全的隔離和保護以防止潛在的分層操作，系統不會跨登錄共用層。

### <a name="manifest"></a>file:///

每個推送至容器登錄的容器映射或成品都會與 *資訊清單*相關聯。 在推送映像時，由登錄所產生的資訊清單，可唯一識別該映像並指定其層次。 您可以使用 Azure CLI 命令 [az acr repository show-manifests][az-acr-repository-show-manifests] 列出存放庫的資訊清單：

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

例如，列出 "acr-helloworld" 存放庫的資訊清單：

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

資訊清單是由唯一的 SHA-256 雜湊或「資訊清單摘要」** 識別。 每個影像或成品（不論是否已標記）都會以其摘要識別。 即使映像的層次資料與另一個映像的層次資料相同完全，摘要值也會是唯一的。 這個機制可讓您重複將標記相同的映像推送至登錄。 例如，您可以將 `myimage:latest` 重複推送至您的登錄，而不會發生錯誤，因為每個映像都是由其唯一摘要識別。

您可以在推送作業中指定其摘要，以從摘要中提取映像。 有些系統可能會設定為依照摘要提取，因為這可保證所提取的映像版本，即使標記相同的映像隨後推送至登錄亦然。

例如，依照資訊清單摘要從 "acr helloworld" 存放庫提取映射：

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> 如果您重複推送具有相同標記且已修改的映像，可以建立孤立映像 (也就是已取消標記的映像)，但仍會耗用您登錄中的空間。 當您依照標記列出或檢視映像時，已取消標記的映像不會顯示在 Azure CLI 或 Azure 入口網站中。 不過，其層次仍然存在，而且會耗用您登錄中的空間。 刪除未標記的影像會在資訊清單是唯一的資訊清單，或最後一個指向特定圖層時，釋出登錄空間。 如需釋放未標記映射所使用之空間的相關資訊，請參閱 [Azure Container Registry 中的刪除容器映射](container-registry-delete.md)。

## <a name="next-steps"></a>後續步驟

在 Azure Container Registry 中深入瞭解 [影像儲存體](container-registry-storage.md) 和 [支援的內容格式](container-registry-image-formats.md) 。

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


