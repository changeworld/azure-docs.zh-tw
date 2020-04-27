---
title: 更新容器群組
description: 了解如何在 Azure 容器執行個體的容器群組中更新執行中的容器。
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: d64590c553f4ae4ef462d4468fade68861db31c3
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160097"
---
# <a name="update-containers-in-azure-container-instances"></a>在 Azure 容器執行個體中更新容器

在容器實例的正常作業期間，您可能會發現必須更新[容器群組](container-instances-container-groups.md)中的執行中容器。 例如，您可能想要更新屬性，例如映射版本、DNS 名稱或環境變數，或重新整理應用程式已損毀之容器中的屬性。

藉由使用至少一個已修改的屬性來重新部署現有的群組，以更新執行中容器群組中的容器。 當您更新容器群組時，群組中所有執行中的容器都會就地重新開機，通常是在相同的基礎容器主機上。

> [!NOTE]
> 無法更新已終止或已刪除的容器群組。 一旦容器群組終止（處於成功或失敗狀態）或已刪除，群組就必須部署為新的。 請參閱其他[限制](#limitations)。

## <a name="update-a-container-group"></a>更新容器群組

若要更新現有的容器群組：

* 發出 create 命令（或使用 Azure 入口網站）並指定現有群組的名稱。 
* 重新部署時，修改或新增至少一個支援更新之群組的屬性。 某些屬性[不支援更新](#properties-that-require-container-delete)。
* 使用您先前提供的值來設定其他屬性。 如果您未設定屬性的值，它會還原為其預設值。

> [!TIP]
> [YAML](/container-instances-container-groups.md#deployment)檔案可協助維護容器群組的部署設定，並提供部署已更新群組的起點。 如果您使用不同的方法來建立群組，您可以使用[az container export][az-container-export]將設定匯出至 YAML， 

### <a name="example"></a>範例

下列 Azure CLI 範例會使用新的 DNS 名稱標籤建立一個容器群組。 因為群組的 DNS 名稱標籤屬性可以更新，所以會重新部署容器群組，且其容器會重新開機。

使用 DNS 名稱標籤 *myapplication-staging* 進行初始部署：

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

使用新的 DNS 名稱標籤*myapplication*來更新容器群組，並使用先前使用的值來設定其餘屬性：

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>更新優點

更新現有容器群組的主要優點是加快部署。 當您重新部署現有的容器群組時，會從先前部署所快取的映像層中提取其容器映像層。 只會提取已修改的映像層 (如果有的話)，而不會像新的部署一樣，從登錄中全新提取所有的映像層。

當您更新 (而非刪除或部署新的) 應用程式時，可以看到以較大容器映像為基礎的應用程式 (例如 Windows Server Core) 部署速度大幅提升。

## <a name="limitations"></a>限制

* 並非容器群組的所有屬性都支援更新。 若要變更容器群組的某些屬性，您必須先刪除該群組，然後予以重新部署。 請參閱[需要刪除容器的屬性](#properties-that-require-container-delete)。
* 當您更新容器群組時，容器群組中的所有容器都會重新啟動。 您無法在多容器群組中執行特定容器的更新或就地重新啟動。
* 容器群組的 IP 位址通常會保留在更新之間，但不保證會維持不變。 只要容器群組部署至相同的基礎主機，容器群組就會保留其 IP 位址。 雖然很罕見，但有一些 Azure 內部事件可能會導致重新部署到不同的主機。 若要解決此問題，建議您為容器實例使用 DNS 名稱標籤。
* 無法更新已終止或已刪除的容器群組。 一旦容器群組停止（處於*終止*狀態）或遭到刪除，群組就會部署為新的。

## <a name="properties-that-require-container-delete"></a>需要刪除容器的屬性

並非所有的容器群組屬性都可以更新。 例如，若要變更容器的重新開機原則，您必須先刪除容器群組，然後再重新建立。

在重新部署之前，這些屬性的變更需要刪除容器群組：

* OS 類型
* CPU、記憶體或 GPU 資源
* 重新啟動原則
* 網路設定檔

當您刪除容器群組並加以重新建立時，它並非「重新部署」，而是新建立的。 所有映像層都會從登錄中全新提取，而不是從先前部署所快取的映像層提取。 容器的 IP 位址也可能會因為部署到不同的基礎主機而變更。

## <a name="next-steps"></a>後續步驟

本文中多次提及**容器群組**。 Azure 容器執行個體中的每個容器都會部署在容器群組中，而容器群組可以包含多個容器。

[Azure Container Instances 中的容器群組](container-instances-container-groups.md)

[部署多容器群組](container-instances-multi-container-group.md)

[手動停止或啟動 Azure 容器實例中的容器](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
