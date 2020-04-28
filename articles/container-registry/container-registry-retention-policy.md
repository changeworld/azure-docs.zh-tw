---
title: 保留未標記之資訊清單的原則
description: 瞭解如何在您的 Azure container registry 中啟用保留原則，以便在定義的期間內自動刪除未標記的資訊清單。
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74454823"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>為未標記的資訊清單設定保留原則

Azure Container Registry 可讓您選擇為沒有任何相關聯標記（未標記的*資訊清單*）的預存映射資訊清單設定*保留原則*。 啟用保留原則時，會在您設定的天數之後自動刪除登錄中未標記的資訊清單。 這項功能可防止登錄填滿不需要的成品，並協助您節省儲存成本。 如果未`delete-enabled`標記的資訊清單的屬性設定為`false`，則無法刪除資訊清單，且不會套用保留原則。

您可以使用 Azure Cloud Shell 或本機安裝的 Azure CLI 來執行本文中的命令範例。 如果您想要在本機使用，則需要2.0.74 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

> [!WARNING]
> 設定具有護理的保留原則--已刪除的映射資料無法復原。 如果您有依資訊清單摘要提取映射的系統（相對於映射名稱），則不應該為未標記的資訊清單設定保留原則。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 請考慮採用「唯一標記」** 配置(這是[建議的最佳做法](container-registry-image-tag-version.md))，而不是依照資訊清單提取。

## <a name="preview-limitations"></a>預覽限制

* 只有**Premium**容器登錄可以設定保留原則。 如需登錄服務層的詳細資訊，請參閱[Azure Container Registry sku](container-registry-skus.md)。
* 您只能為未標記的資訊清單設定保留原則。
* 保留原則目前僅適用于啟用原則*之後*未標記的資訊清單。 登錄中現有未標記的資訊清單不受限於原則。 若要刪除現有未標記的資訊清單，請參閱在[Azure Container Registry 中刪除容器映射](container-registry-delete.md)中的範例。

## <a name="about-the-retention-policy"></a>關於保留原則

Azure Container Registry 會針對登錄中的資訊清單參考計數。 當資訊清單未標記時，它會檢查保留原則。 如果已啟用保留原則，則會根據原則中設定的天數，將資訊清單刪除作業排入佇列，並指定特定的日期。

個別的佇列管理作業會持續處理訊息，並視需要進行調整。 例如，假設您在保留原則為30天的登錄中，將兩個資訊清單（共1小時）解除標記。 兩則訊息會排入佇列。 然後，30天后大約1小時，訊息會從佇列中取出並加以處理，前提是原則仍然有效。

## <a name="set-a-retention-policy---cli"></a>設定保留原則-CLI

下列範例示範如何使用 Azure CLI 來設定登錄中未標記之資訊清單的保留原則。

### <a name="enable-a-retention-policy"></a>啟用保留原則

根據預設，容器登錄中不會設定保留原則。 若要設定或更新保留原則，請在 Azure CLI 中執行[az acr config 保留更新][az-acr-config-retention-update]命令。 您可以指定0到365之間的天數，以保留未標記的資訊清單。 如果您未指定天數，此命令會將預設值設為7天。 在保留期限之後，登錄中所有未標記的資訊清單都會自動刪除。

下列範例會針對登錄*myregistry*中未標記的資訊清單，設定30天的保留原則：

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

下列範例會設定原則，以在登錄時立即刪除其中的任何資訊清單。 藉由設定0天的保留期限來建立此原則。 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>驗證保留原則

如果您啟用先前的原則，保留期為0天，您可以快速確認刪除未標記的資訊清單：

1. 將測試映射`hello-world:latest`映射推送至您的登錄，或替換成您選擇的另一個測試映射。
1. Untag `hello-world:latest`映射，例如，使用[az acr repository Untag][az-acr-repository-untag]命令。 未標記的資訊清單會保留在登錄中。
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. 在幾秒內，會刪除未標記的資訊清單。 您可以藉由在存放庫中列出資訊清單來確認刪除，例如，使用[az acr repository show-資訊清單][az-acr-repository-show-manifests]命令。 如果測試映射是存放庫中的唯一一個，則會刪除存放庫本身。

### <a name="disable-a-retention-policy"></a>停用保留原則

若要查看登錄中設定的保留原則，請執行[az acr config 保留顯示][az-acr-config-retention-show]命令：

```azurecli
az acr config retention show --registry myregistry
```

若要在登錄中停用保留原則，請執行[az acr config 保留更新][az-acr-config-retention-update]命令， `--status disabled`並設定：

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>設定保留原則-入口網站

您也可以在[Azure 入口網站](https://portal.azure.com)中設定登錄的保留原則。 下列範例示範如何使用入口網站，在登錄中設定未標記的資訊清單保留原則。

### <a name="enable-a-retention-policy"></a>啟用保留原則

1. 流覽至您的 Azure container registry。 在 [**原則**] 底下，選取 [**保留期**（預覽）]。
1. 在 [**狀態**] 中，選取 [**已啟用**]。
1. 選取0到365之間的天數，以保留未標記的資訊清單。 選取 [儲存]  。

![在 Azure 入口網站中啟用保留原則](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>停用保留原則

1. 流覽至您的 Azure container registry。 在 [**原則**] 底下，選取 [**保留期**（預覽）]。
1. 在 [**狀態**] 中，選取 [**停用**]。 選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

* 深入瞭解在 Azure Container Registry 中[刪除映射和存放庫](container-registry-delete.md)的選項

* 瞭解如何從登錄[自動清除](container-registry-auto-purge.md)選取的映射和資訊清單

* 深入瞭解在登錄中[鎖定映射和資訊清單](container-registry-image-lock.md)的選項

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
