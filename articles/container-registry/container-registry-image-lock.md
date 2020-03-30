---
title: 鎖定映像
description: 設置容器映射或存儲庫的屬性，以便在 Azure 容器註冊表中無法刪除或覆蓋它。
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659691"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>在 Azure 容器註冊表中鎖定容器映射

在 Azure 容器註冊表中，可以鎖定映射版本或存儲庫，以便無法刪除或更新映射版本。 要鎖定映射或存儲庫，請使用 Azure CLI 命令 az [acr 存儲庫更新][az-acr-repository-update]其屬性。 

本文要求您在 Azure 雲外殼或本地（建議版本 2.0.55 或更高版本）中運行 Azure CLI。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

> [!IMPORTANT]
> 本文不適用於鎖定整個註冊表，例如，在 Azure 門戶中使用 **"設置>鎖定**"或`az lock`Azure CLI 中的命令。 鎖定註冊表資源不會阻止您在存儲庫中創建、更新或刪除資料。 鎖定註冊表只會影響管理操作，例如添加或刪除複製或刪除註冊表本身。 [有關詳細資訊，在鎖定資源，以防止意外更改](../azure-resource-manager/management/lock-resources.md)。

## <a name="scenarios"></a>案例

預設情況下，Azure 容器註冊表中標記的圖像是*可變的*，因此，具有適當的許可權，您可以重複更新具有相同標記的圖像並將其推送到註冊表。 也可以根據需要[刪除](container-registry-delete.md)容器映射。 當您開發映射並且需要維護註冊表的大小時，此行為非常有用。

但是，當您將容器映射部署到生產時，可能需要一個*不可變*的容器映射。 不可改變的圖像是不能意外刪除或覆蓋的圖像。

有關在註冊表中標記和版本映射的策略，請參閱[有關標記和版本控制容器映射的建議](container-registry-image-tag-version.md)。

使用[az acr 存儲庫更新][az-acr-repository-update]命令設置存儲庫屬性，以便您可以：

* 鎖定映射版本或整個存儲庫

* 保護映射版本或存儲庫不刪除，但允許更新

* 防止映射版本或整個存儲庫的讀取（拉取）操作

有關示例，請參閱以下各節。 

## <a name="lock-an-image-or-repository"></a>鎖定映射或存儲庫 

### <a name="show-the-current-repository-attributes"></a>顯示當前存儲庫屬性
要查看存儲庫的當前屬性，運行以下[az acr 存儲庫顯示][az-acr-repository-show]命令：

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>顯示當前圖像屬性
要查看標記的當前屬性，運行以下[az acr 存儲庫顯示][az-acr-repository-show]命令：

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>按標記鎖定圖像

要鎖定*myrepo/myimage：標記**圖像在我的註冊表*中，請運行以下[az acr 存儲庫更新][az-acr-repository-update]命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>通過清單摘要鎖定圖像

要鎖定由清單摘要（SHA-256 雜湊，表示為`sha256:...`）標識的*myrepo/myimage 圖像*，運行以下命令。 （要查找與一個或多個圖像標記關聯的清單摘要，運行 az [acr 存儲庫顯示清單][az-acr-repository-show-manifests]命令。

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>鎖定存儲庫

要鎖定*myrepo/myimage*存儲庫及其中的所有映射，運行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>保護映射或存儲庫不刪除

### <a name="protect-an-image-from-deletion"></a>保護圖像不刪除

要允許*更新但未刪除的 myrepo/myimage：標記*圖像，請運行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>保護存儲庫不刪除

以下命令設置*myrepo/myimage*存儲庫，使其無法刪除。 單個圖像仍可更新或刪除。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>防止對映射或存儲庫執行讀取操作

要防止*在 myrepo/myimage：標記*圖像上讀取（拉取）操作，請運行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

要防止*對 myrepo/myimage*存儲庫中的所有映射執行讀取操作，請運行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>解鎖圖像或存儲庫

要還原*myrepo/myimage：標記*圖像的預設行為，以便可以刪除和更新它，請運行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

要還原*myrepo/myimage*存儲庫和所有映射的預設行為，以便可以刪除和更新它們，請運行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>後續步驟

在本文中，您瞭解了使用[az acr 存儲庫更新][az-acr-repository-update]命令來防止刪除或更新存儲庫中的圖像版本。 要設置其他屬性，請參閱[az acr 存儲庫更新][az-acr-repository-update]命令引用。

要查看為映射版本或存儲庫設置的屬性，請使用[az acr 存儲庫顯示][az-acr-repository-show]命令。

有關刪除操作的詳細資訊，請參閱[在 Azure 容器註冊表中刪除容器映射][container-registry-delete]。

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

