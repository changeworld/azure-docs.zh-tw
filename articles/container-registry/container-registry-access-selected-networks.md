---
title: 設定服務防火牆規則
description: 設定 IP 規則，以允許從選取的公用 IP 位址或位址範圍存取 Azure container registry。
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984612"
---
# <a name="configure-public-ip-network-rules"></a>設定公用 IP 網路規則

根據預設，Azure container registry 會接受從任何網路上的主機透過網際網路連接。 本文說明如何將您的容器登錄設定為僅允許來自特定公用 IP 位址或位址範圍的存取。 系統會提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

系統會在公用登錄端點上設定 IP 網路規則。 IP 網路規則不適用於以[私人連結](container-registry-private-link.md)設定的私人端點

設定 IP 存取規則**可在高階 container registry**服務層級中取得。 如需登錄服務層和限制的相關資訊，請參閱[Azure Container Registry 層](container-registry-skus.md)。

## <a name="access-from-selected-public-network---cli"></a>從選取的公用網路存取-CLI

### <a name="change-default-network-access-to-registry"></a>變更登錄的預設網路存取

若要限制對所選公用網路的存取，請先將預設動作變更為 [拒絕存取]。 在下列[az acr update][az-acr-update]命令中，以您的登錄名稱取代：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>將網路規則新增至登錄

使用[az acr network-rule add][az-acr-network-rule-add]命令，將網路規則新增至您的登錄，以允許來自公用 IP 位址或範圍的存取。 例如，以虛擬網路中 VM 的容器登錄名稱和公用 IP 位址取代。

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> 新增規則之後，需要幾分鐘的時間，規則才會生效。

## <a name="access-from-selected-public-network---portal"></a>從選取的公用網路存取-入口網站

1. 在入口網站中，流覽至您的 container registry。
1. 在 [**設定**] 底下，選取 [**網路**]。
1. 在 [**公用存取**] 索引標籤上，選取允許從**選取的網路**進行公用存取。
1. 在 [**防火牆**] 底下，輸入公用 ip 位址，例如虛擬網路中 VM 的公用 ip 位址。 或者，以 CIDR 標記法輸入包含 VM IP 位址的位址範圍。
1. 選取 [儲存]  。

![設定 container registry 的防火牆規則][acr-access-selected-networks]

> [!NOTE]
> 新增規則之後，需要幾分鐘的時間，規則才會生效。

> [!TIP]
> （選擇性）從本機用戶端電腦或 IP 位址範圍啟用登錄存取。 若要允許此存取，您需要電腦的公用 IPv4 位址。 您可以在網際網路瀏覽器中搜尋「我的 IP 位址是什麼」來尋找此位址。 當您在入口網站的 [**網路**] 頁面上設定防火牆設定時，目前的用戶端 IPv4 位址也會自動出現。

## <a name="disable-public-network-access"></a>停用公用網路存取

若要使用[私人連結](container-registry-private-link.md)限制對虛擬網路的流量，請停用登錄上的公用端點。 停用公用端點會覆寫所有防火牆設定。

### <a name="disable-public-access---portal"></a>停用公用存取-入口網站

1. 在入口網站中，流覽至您的容器登錄，然後選取 [設定] [ **> 網路**]。
1. 在 [**公用存取**] 索引標籤的 [**允許公用存取**] 中，選取 [**停用**]。 然後選取 [儲存]  。

![停用公用存取][acr-access-disabled]

## <a name="restore-default-registry-access"></a>還原預設登錄存取

若要將登錄還原為預設允許存取，請更新預設動作。 

### <a name="restore-default-registry-access---portal"></a>還原預設登錄存取-入口網站

1. 在入口網站中，流覽至您的容器登錄，然後選取 [設定] [ **> 網路**]。
1. 在 [**防火牆**] 底下，選取每個位址範圍，然後選取 [刪除] 圖示。
1. 在 [**公用存取**] 索引標籤的 [**允許公用存取**] 中，選取 [**所有網路**]。 然後選取 [儲存]  。

![所有網路的公用存取][acr-access-all-networks]

## <a name="next-steps"></a>後續步驟

* 若要使用虛擬網路中的私人端點來限制對登錄的存取，請參閱[設定 azure container registry 的 Azure 私人連結](container-registry-private-link.md)。
* 如果您需要從用戶端防火牆後方設定登錄存取規則，請參閱設定[規則以存取防火牆後方的 Azure container registry](container-registry-firewall-access-rules.md)。

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
