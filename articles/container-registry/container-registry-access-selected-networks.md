---
title: 設定公用登錄存取
description: 設定 IP 規則，以允許從選取的公用 IP 位址或位址範圍存取 Azure 容器登錄。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 4e4ee817e2534bcca09cba89daafd379ff3f03f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488757"
---
# <a name="configure-public-ip-network-rules"></a>設定公用 IP 網路規則

根據預設，Azure 容器登錄會接受任何網路上的主機透過網際網路的連線。 本文說明如何將您的容器登錄設定為僅允許來自特定公用 IP 位址或位址範圍的存取。 文中提供使用 Azure CLI 和 Azure 入口網站的設定步驟。

IP 網路規則是在公用登錄端點上設定。 IP 網路規則不適用於以[私人連結](container-registry-private-link.md)設定的私人端點。

您可以在 **Premium** 容器登錄服務層級中設定 IP 存取規則。 如需登錄服務層級和限制的相關資訊，請參閱 [Azure 容器登錄層級](container-registry-skus.md)。

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>從選取的公用網路存取 - CLI

### <a name="change-default-network-access-to-registry"></a>變更登錄的預設網路存取

若要限制對所選公用網路的存取，請先將預設動作變更為拒絕存取。 在下列 [az acr update][az-acr-update] 命令中，以您的登錄名稱取代名稱：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>將網路規則新增至登錄

使用 [az acr network-rule add][az-acr-network-rule-add] 命令，將網路規則新增至您的登錄，以允許來自公用 IP 位址或範圍的存取。 例如，取代虛擬網路中 VM 的容器登錄的名稱和公用 IP 位址。

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> 新增規則之後，需要幾分鐘的時間，規則才會生效。

## <a name="access-from-selected-public-network---portal"></a>從選取的公用網路存取 - 入口網站

1. 在入口網站中，瀏覽到您的容器登錄。
1. 在 [設定] 底下，選取 [網路]。
1. 在 [公用存取權] 索引標籤上，選取允許從 [選取的網路] 公用存取。
1. 在 [防火牆] 下，輸入公用 IP 位址，例如虛擬網路中 VM 的公用 IP 位址。 或者，以 CIDR 標記法輸入包含 VM IP 位址的位址範圍。
1. 選取 [儲存]。

![設定容器登錄的防火牆規則][acr-access-selected-networks]

> [!NOTE]
> 新增規則之後，需要幾分鐘的時間，規則才會生效。

> [!TIP]
> 您可選擇啟用從本機用戶端電腦或 IP 位址範圍的登錄存取。 為了允許此存取，您需要電腦的公用 IPv4 位址。 您可以在網際網路瀏覽器中搜尋「我的 IP 位址是什麼」來尋找此位址。 當您在入口網站的 [網路] 頁面上設定防火牆設定時，目前的用戶端 IPv4 位址也會自動出現。

## <a name="disable-public-network-access"></a>停用公用網路存取

您可選擇停用登錄上的公用端點。 停用公用端點會覆寫所有防火牆設定。 例如，您可能會想要使用[私人連結](container-registry-private-link.md)來停用虛擬網路中受保護登錄的公用存取。

> [!NOTE]
> 如果登錄是在具有 [服務端點](container-registry-vnet.md)的虛擬網路中設定，則停用登錄的公用端點的存取權也會停用虛擬網路內登錄的存取。

### <a name="disable-public-access---cli"></a>停用公用存取 - CLI

若要使用 Azure CLI 停用公用存取，請執行 [az acr update][az-acr-update]，並將 `--public-network-enabled` 設定為 `false`。 `public-network-enabled` 引數需要 Azure CLI 2.6.0 或更新版本。 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>停用公用存取 - 入口網站

1. 在入口網站中，瀏覽至您的容器登錄，然後選取 [設定] > [網路]。
1. 在 [公用存取] 索引標籤的 [允許公用網路存取] 中，選取 [停用]。 然後選取 [儲存]。

![停用公用存取][acr-access-disabled]


## <a name="restore-public-network-access"></a>還原公用網路存取

若要重新啟用公用端點，請更新網路設定以允許公用存取。 啟用公用端點會覆寫所有防火牆設定。 

### <a name="restore-public-access---cli"></a>還原公用存取 - CLI

執行 [az acr update][az-acr-update]，並將 `--public-network-enabled` 設定為 `true`。 

> [!NOTE]
> `public-network-enabled` 引數需要 Azure CLI 2.6.0 或更新版本。 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>還原公用存取 - 入口網站

1. 在入口網站中，瀏覽至您的容器登錄，然後選取 [設定] > [網路]。
1. 在 [公用存取] 索引標籤的 [允許公用網路存取] 中，選取 [所有網路]。 然後選取 [儲存]。

![從所有網路公用存取][acr-access-all-networks]

## <a name="troubleshoot"></a>疑難排解

如果設定了公用網路規則，或拒絕對登錄的公用存取，則嘗試從不允許的公用網路登入登錄將會失敗。 如果未設定 proxy 的存取規則，從 HTTPS proxy 後方的用戶端存取也會失敗。 您會看到類似于或的錯誤 `Error response from daemon: login attempt failed with status: 403 Forbidden` 訊息 `Looks like you don't have access to registry` 。

如果您使用網路存取規則所允許的 HTTPS proxy，但未在用戶端環境中正確設定 proxy，也可能會發生這些錯誤。 確認您的 Docker 用戶端和 Docker daemon 都已針對 proxy 行為進行設定。 如需詳細資訊，請參閱 Docker 檔中的 [HTTP/HTTPS proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) 。


## <a name="next-steps"></a>後續步驟

* 若要使用虛擬網路中的私人端點來限制對登錄的存取，請參閱[設定 Azure 容器登錄的 Azure 私人連結](container-registry-private-link.md)。
* 如果您需要設定用戶端防火牆後方的登錄存取規則，請參閱[設定可以從防火牆後方存取 Azure Container Registry 的規則](container-registry-firewall-access-rules.md)。

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
