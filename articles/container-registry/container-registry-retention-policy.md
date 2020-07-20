---
title: 保留未標記之資訊清單的原則
description: 了解如何在您的 Azure 容器登錄中啟用保留原則，以便在定義的期間後自動刪除未標記的資訊清單。
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683450"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>針對未標記的資訊清單設定保留原則

Azure Container Registry 可讓您選擇為沒有任何關聯標記的預存影像資訊清單 (*未標記的資訊清單*) 設定*保留原則*。 啟用保留原則時，系統會在您設定的天數之後，自動刪除登錄中未標記的資訊清單。 此功能可防止登錄填滿不需要的成品，並協助您節省儲存成本。 如果未標記之資訊清單的 `delete-enabled` 屬性設定為 `false`，則無法刪除資訊清單，且不會套用保留原則。

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來執行此文章中的命令範例。 如果您想要在本機使用，需使用 2.0.74 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

保留原則是**進階**容器登錄的一個功能。 如需有關登錄服務層級的資訊，請參閱 [Azure Container Registry 服務層級](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

> [!WARNING]
> 設定保留原則時請務必小心，已刪除的映像資料「無法復原」。 如果您的系統會依照資訊清單摘要 (相對於映像名稱) 提取映像，則不應該針對未標記的資訊清單設定保留原則。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 請考慮採用*唯一標記*配置 (這是[建議的最佳做法](container-registry-image-tag-version.md))，而不是依照資訊清單提取。

## <a name="preview-limitations"></a>預覽限制

* 您只能針對未標記的資訊清單設定保留原則。
* 保留原則目前僅適用於啟用原則*之後*未標記的資訊清單。 登錄中現有的未標記資訊清單不受限於原則。 若要刪除現有的未標記資訊清單，請參閱[刪除 Azure Container Registry 中的容器映像](container-registry-delete.md)中的範例。

## <a name="about-the-retention-policy"></a>關於保留原則

Azure Container Registry 會針對登錄中的資訊清單參考計數。 當資訊清單未標記時，會檢查保留原則。 如果已啟用保留原則，則會根據原則中設定的天數，將資訊清單刪除作業排入具有特定日期的佇列。

個別的佇列管理作業會持續處理訊息，並視需要進行調整。 例如，假設您在保留原則為 30 天的登錄中，將兩個相隔 1 小時的資訊清單取消標記。 兩則訊息會排入佇列。 接著在 30 天後，大約相隔 1 小時，假設原則仍然有效，系統會從佇列中擷取訊息並加以處理。

## <a name="set-a-retention-policy---cli"></a>設定保留原則 - CLI

下列範例示範如何使用 Azure CLI 設定登錄中未標記之資訊清單的保留原則。

### <a name="enable-a-retention-policy"></a>啟用保留原則

根據預設，容器登錄中不會設定任何保留原則。 若要設定或更新保留原則，請在 Azure CLI 中執行 [az acr config retention update][az-acr-config-retention-update] 命令。 您可以指定 0 到 365 之間的天數，以保留未標記的資訊清單。 如果您未指定天數，此命令會設定為預設值 7 天。 在保留期間之後，登錄中所有未標記的資訊清單都會自動遭到刪除。

下列範例會針對登錄 *myregistry* 中未標記的資訊清單，設定保留原則為 30 天：

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

下列範例會設定在取消標記後立即刪除登錄中的任何資訊清單的原則。 藉由將保留期間設定為 0 天來建立此原則。 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>驗證保留原則

如果您啟用先前保留期間為 0 天的原則，則可以快速確認是否已刪除未標記的資訊清單：

1. 將測試映像 `hello-world:latest` 映像推送至您的登錄，或替換成您選擇的另一個測試映像。
1. 例如，使用 [az acr repository untag][az-acr-repository-untag] 命令取消 `hello-world:latest` 映像標記。 未標記的資訊清單會保留在登錄中。
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. 在幾秒內，未標記的資訊清單會遭到刪除。 您可以列出存放庫中的資訊清單來確認刪除，例如，使用 [az acr repository show-manifests][az-acr-repository-show-manifests] 命令。 如果測試映像是存放庫中的唯一一個映像，則會刪除存放庫本身。

### <a name="disable-a-retention-policy"></a>停用保留原則

若要查看登錄中設定的保留原則，請執行 [az acr config retention show][az-acr-config-retention-show] 命令：

```azurecli
az acr config retention show --registry myregistry
```

若要停用登錄中的保留原則，執行 [az acr config retention update][az-acr-config-retention-update] 命令並設定 `--status disabled`：

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>設定保留原則 - 入口網站

您也可以在 [Azure 入口網站](https://portal.azure.com)中設定登錄的保留原則。 下列範例示範如何使用入口網站設定登錄中未標記之資訊清單的保留原則。

### <a name="enable-a-retention-policy"></a>啟用保留原則

1. 瀏覽至您的 Azure Container Registry。 在 [原則] 底下，選取 [保留] (預覽)。
1. 在 [狀態] 中，選取 [啟用]。
1. 選取 0 到 365 之間的天數，以保留未標記的資訊清單。 選取 [儲存]。

![在 Azure 入口網站中啟用保留原則](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>停用保留原則

1. 瀏覽至您的 Azure Container Registry。 在 [原則] 底下，選取 [保留] (預覽)。
1. 在 [狀態] 中，選取 [停用]。 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

* 深入了解在 Azure Container Registry 中[刪除映像和存放庫](container-registry-delete.md)的選項

* 了解如何從登錄中[自動清除](container-registry-auto-purge.md)選取的映像和資訊清單

* 深入了解在登錄中[鎖定映像和資訊清單](container-registry-image-lock.md)的選項

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
