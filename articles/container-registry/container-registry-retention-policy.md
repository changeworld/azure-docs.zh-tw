---
title: 保留未標記清單的策略
description: 瞭解如何在 Azure 容器註冊表中啟用保留原則，以便在定義期間後自動刪除未標記的清單。
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454823"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>為未標記的清單設置保留原則

Azure 容器註冊表為您提供了為沒有任何關聯標記（*未標記清單*）的存儲映射清單設置*保留原則*的選項。 啟用保留原則後，註冊表中的未標記清單會在您設置的天數後自動刪除。 此功能可防止註冊表填滿不需要的專案，並説明您節省存儲成本。 如果未`delete-enabled`標記的清單的屬性設置為`false`，無法刪除清單，並且保留原則不適用。

可以使用 Azure 雲外殼或 Azure CLI 的本地安裝來運行本文中的命令示例。 如果您想在本地使用它，則需要版本 2.0.74 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

> [!WARNING]
> 使用謹慎設置保留原則-刪除的圖像資料是"無法恢復"。 如果系統通過清單摘要（而不是圖像名稱）提取圖像，則不應為未標記的清單設置保留原則。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 請考慮採用「唯一標記」** 配置(這是[建議的最佳做法](container-registry-image-tag-version.md))，而不是依照資訊清單提取。

## <a name="preview-limitations"></a>預覽限制

* 只能使用保留原則配置**高級**容器註冊表。 有關註冊表服務層的資訊，請參閱[Azure 容器註冊表 SKU](container-registry-skus.md)。
* 您只能為未標記的清單設置保留原則。
* 保留原則當前僅適用于啟用策略*後*未標記的清單。 註冊表中現有的未標記清單不受策略約束。 要刪除現有的未標記清單，請參閱 Azure[容器註冊表 中的"刪除容器映射"中](container-registry-delete.md)的示例。

## <a name="about-the-retention-policy"></a>關於保留原則

Azure 容器註冊表對註冊表中的清單進行引用計數。 當未標記清單時，它將檢查保留原則。 如果啟用了保留原則，則根據策略中設置的天數，對清單刪除操作進行排隊，並特定日期。

單獨的佇列管理作業會不斷處理消息，根據需要進行縮放。 例如，假設您在保留原則為 30 天的註冊表中未標記兩個清單（相隔 1 小時）。 將排隊兩條消息。 然後，30 天后，大約 1 小時，消息將從佇列中檢索並處理，前提是策略仍然有效。

## <a name="set-a-retention-policy---cli"></a>設置保留原則 - CLI

下面的示例演示如何使用 Azure CLI 為註冊表中未標記的清單設置保留原則。

### <a name="enable-a-retention-policy"></a>啟用保留原則

預設情況下，容器註冊表中不設置保留原則。 要設置或更新保留原則，請運行 Azure CLI 中的[az acr 配置保留更新][az-acr-config-retention-update]命令。 您可以指定介於 0 和 365 之間的天數來保留未標記的清單。 如果未指定天數，該命令將預設為 7 天。 保留期後，註冊表中的所有未標記清單將自動刪除。

以下示例為註冊表*註冊簿*中未標記的清單設置 30 天的保留原則：

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

下面的示例設置一個策略，一旦未標記註冊表，立即刪除註冊表中的任何清單。 通過設置 0 天的保留期來創建此策略。 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>驗證保留原則

如果啟用保留期為 0 天的上述策略，則可以快速驗證未標記的清單是否被刪除：

1. 將測試映射`hello-world:latest`推送到註冊表，或替換您選擇的其他測試映射。
1. 解除標記`hello-world:latest`映射，例如，使用 az [acr 存儲庫解除標記][az-acr-repository-untag]命令。 未標記的清單將保留在註冊表中。
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. 在幾秒鐘內，未標記的清單將被刪除。 您可以通過在存儲庫中列出清單來驗證刪除，例如，使用[az acr 存儲庫顯示清單][az-acr-repository-show-manifests]命令。 如果測試映射是存儲庫中唯一的映射，則存儲庫本身將被刪除。

### <a name="disable-a-retention-policy"></a>禁用保留原則

要查看註冊表中的保留原則設置，運行[az acr 配置保留顯示][az-acr-config-retention-show]命令：

```azurecli
az acr config retention show --registry myregistry
```

要禁用註冊表中的保留原則，請運行[az acr 配置保留更新][az-acr-config-retention-update]命令並設置`--status disabled`：

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>設置保留原則 - 門戶

您還可以在[Azure 門戶](https://portal.azure.com)中設置註冊表的保留原則。 下面的示例演示如何使用門戶為註冊表中未標記的清單設置保留原則。

### <a name="enable-a-retention-policy"></a>啟用保留原則

1. 導航到 Azure 容器註冊表。 在 **"策略"** 下，選擇**保留**（預覽）。
1. 在 **"狀態****"中，選擇"已啟用**"。
1. 選擇 0 和 365 之間的天數以保留未標記的清單。 選取 [儲存]****。

![在 Azure 門戶中啟用保留原則](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>禁用保留原則

1. 導航到 Azure 容器註冊表。 在 **"策略"** 下，選擇**保留**（預覽）。
1. 在 **"狀態**"中，選擇 **"禁用**"。 選取 [儲存]****。

## <a name="next-steps"></a>後續步驟

* 瞭解有關在 Azure 容器註冊表中刪除[圖像和存儲庫](container-registry-delete.md)的選項的詳細資訊

* 瞭解如何從註冊表[中自動清除](container-registry-auto-purge.md)選定的圖像和清單

* 瞭解有關在註冊表中[鎖定圖像和清單](container-registry-image-lock.md)的選項的詳細資訊

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
