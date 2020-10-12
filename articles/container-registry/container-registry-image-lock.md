---
title: 鎖定映像
description: 設定容器映射或存放庫的屬性，使其無法在 Azure container registry 中刪除或覆寫。
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77659691"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>鎖定 Azure container registry 中的容器映射

在 Azure container registry 中，您可以鎖定映射版本或存放庫，使其無法被刪除或更新。 若要鎖定映射或存放庫，請使用 Azure CLI 命令 [az acr repository update][az-acr-repository-update]來更新它的屬性。 

本文要求您在 Azure Cloud Shell 或本地 (使用2.0.55 版版或更新版本的建議) 中執行 Azure CLI。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

> [!IMPORTANT]
> 本文不適用於鎖定整個登錄，例如，在 Azure 入口網站中使用 **設定 > 鎖定** ，或 `az lock` Azure CLI 中的命令。 鎖定登錄資源並不會讓您無法建立、更新或刪除存放庫中的資料。 鎖定登錄只會影響管理作業，例如新增或刪除複寫，或刪除登錄本身。 鎖定資源中的詳細資訊 [，以防止非預期的變更](../azure-resource-manager/management/lock-resources.md)。

## <a name="scenarios"></a>案例

根據預設，Azure Container Registry 中的標記 *映射是可變動的，* 因此使用適當的許可權，您可以重複更新並將具有相同標記的映射推送至登錄。 您也可以視需要 [刪除](container-registry-delete.md) 容器映射。 當您開發映射時，這項行為相當有用，而且需要維護登錄的大小。

不過，當您將容器映射部署到生產環境時，您可能需要 *不可變* 的容器映射。 不可變的映射是您不小心刪除或覆寫的映射。

請參閱標記 [和版本控制容器映射的建議](container-registry-image-tag-version.md) ，以取得在登錄中標記和版本映射的策略。

使用 [az acr repository update][az-acr-repository-update] 命令設定存放庫屬性，讓您可以：

* 鎖定映射版本或整個存放庫

* 防止刪除映射版本或存放庫，但允許更新

* 防止在映射版本或整個存放庫上讀取 (提取) 作業

如需範例，請參閱下列各節。 

## <a name="lock-an-image-or-repository"></a>鎖定映射或存放庫 

### <a name="show-the-current-repository-attributes"></a>顯示目前的存放庫屬性
若要查看存放庫的目前屬性，請執行下列 [az acr repository show][az-acr-repository-show] 命令：

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>顯示目前的影像屬性
若要查看標記的目前屬性，請執行下列 [az acr repository show][az-acr-repository-show] 命令：

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>依標記鎖定影像

若要鎖定*myregistry*中的*myrepo/myimage：標記*映射，請執行下列[az acr repository update][az-acr-repository-update]命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>依資訊清單摘要鎖定映射

若要鎖定資訊清單摘要所識別的 *myrepo/myimage* 映射 (256 sha-1 雜湊（以 `sha256:...`) 表示），請執行下列命令。  (若要尋找與一或多個映射標籤相關聯的資訊清單摘要，請執行 [az acr repository show-資訊清單][az-acr-repository-show-manifests] 命令。 ) 

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>鎖定儲存機制

若要鎖定 *myrepo/myimage* 存放庫和其中的所有映射，請執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>保護映射或存放庫免于刪除

### <a name="protect-an-image-from-deletion"></a>防止刪除映射

若要允許更新 *myrepo/myimage：標記* 映射，但不加以刪除，請執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>保護存放庫免于刪除

下列命令會設定 *myrepo/myimage* 存放庫，使其無法刪除。 您仍然可以更新或刪除個別映射。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>防止映射或存放庫的讀取作業

若要避免在 *myrepo/myimage：標記* 映射上讀取 (提取) 作業，請執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

若要防止 *myrepo/myimage* 存放庫中所有映射的讀取作業，請執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>解除鎖定映射或存放庫

若要還原 *myrepo/myimage：標記* 映射的預設行為，使其可以刪除和更新，請執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

若要還原 *myrepo/myimage* 存放庫和所有映射的預設行為，使其可供刪除和更新，請執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 [az acr repository update][az-acr-repository-update] 命令來防止刪除或更新存放庫中的映射版本。 若要設定其他屬性，請參閱 [az acr repository update][az-acr-repository-update] 命令參考。

若要查看為映射版本或存放庫設定的屬性，請使用 [az acr repository show][az-acr-repository-show] 命令。

如需刪除作業的詳細資訊，請參閱 [Azure Container Registry 中的刪除容器映射][container-registry-delete]。

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

