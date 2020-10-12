---
title: 針對不同網路拓撲中的 Azure Dev Spaces 設定網路功能
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes Services 中執行 Azure Dev Spaces 的網路需求
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器、CNI、kubenet、SDN、network
ms.openlocfilehash: 0d9ebbec3e3c07a466acb58e88b67e6a32a20edb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88214162"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>針對不同網路拓撲中的 Azure Dev Spaces 設定網路功能

Azure Dev Spaces 在具有預設網路設定的 Azure Kubernetes Service (AKS) 叢集上執行。 如果您想要變更 AKS 叢集的網路設定，例如將叢集放在防火牆後方、使用網路安全性群組或使用網路原則，您必須併入執行 Azure Dev Spaces 的其他考慮。

![虛擬網路設定](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>虛擬網路或子網設定

您的 AKS 叢集可能會有不同的虛擬網路或子網設定，以限制 AKS 叢集的輸入或輸出流量。 例如，您的叢集可能位於防火牆後方，例如 Azure 防火牆，您也可以使用網路安全性群組或自訂角色來限制網路流量。 您可以在 [GitHub 上的 Azure Dev Spaces 範例儲存][sample-repo]機制中找到範例網路設定。

Azure Dev Spaces 具有輸入 *和* 輸出網路流量以及 *僅* 輸入流量的特定需求。 如果您要在 AKS 叢集上使用 Azure Dev Spaces，且虛擬網路或子網設定會限制 AKS 叢集的流量，您必須遵循下列僅輸入和輸入和輸出流量需求，才能讓 Azure Dev Spaces 正常運作。

### <a name="ingress-and-egress-network-traffic-requirements"></a>輸入和輸出網路流量需求

Azure Dev Spaces 需要下列 Fqdn 的輸入和輸出流量：

| FQDN                       | 連接埠       | 使用      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS：443 | 提取 docker 映射以進行 Azure Dev Spaces |
| gcr.io                     | HTTPS：443 | 提取 Azure Dev Spaces 的 helm 映射 |
| storage.googleapis.com     | HTTPS：443 | 提取 Azure Dev Spaces 的 helm 映射 |

更新您的防火牆或安全性設定，以允許與上述所有 Fqdn 和 [Azure Dev Spaces 基礎結構服務][service-tags]之間的網路流量。 例如，如果您使用防火牆來保護您的網路，則應該將上述 Fqdn 新增至防火牆的應用程式規則，而且也必須將 Azure Dev Spaces 服務標籤 [新增至防火牆][firewall-service-tags]。 防火牆的這兩個更新都必須允許這些網域的流量。

### <a name="ingress-only-network-traffic-requirements"></a>只輸入網路流量需求

Azure Dev Spaces 提供 Kubernetes 命名空間層級的路由，以及使用本身的 FQDN 對服務的公用存取。 這兩項功能都能運作，請更新您的防火牆或網路設定，以允許公用輸入至叢集上 Azure Dev Spaces 輸入控制器的外部 IP 位址。 或者，您可以建立 [內部負載平衡器][aks-internal-lb] ，並在防火牆中新增 NAT 規則，以將防火牆的公用 ip 轉譯為內部負載平衡器的 ip。 您也可以使用 [traefik][traefik-ingress] 或 [NGINX][nginx-ingress] 來建立自訂輸入控制器。

## <a name="aks-cluster-network-requirements"></a>AKS 叢集網路需求

AKS 可讓您使用 [網路原則][aks-network-policies] 來控制叢集中 pod 之間的輸入和輸出流量，以及來自 pod 的輸出流量。 Azure Dev Spaces 具有輸入 *和* 輸出網路流量以及 *僅* 輸入流量的特定需求。 如果您在具有 AKS 網路原則的 AKS 叢集上使用 Azure Dev Spaces，您必須遵循下列僅輸入和輸入和輸出流量需求，才能讓 Azure Dev Spaces 正常運作。

### <a name="ingress-and-egress-network-traffic-requirements"></a>輸入和輸出網路流量需求

Azure Dev Spaces 可讓您直接與叢集上開發人員空間中的 pod 進行通訊，以進行偵錯工具。 若要讓這項功能正常運作，請新增網路原則，以允許對 Azure Dev Spaces 基礎結構的 IP 位址進行輸入和輸出通訊，這 [會因區域而異][service-tags]。

### <a name="ingress-only-network-traffic-requirements"></a>只輸入網路流量需求

Azure Dev Spaces 在各個命名空間之間提供 pod 之間的路由。 例如，啟用 Azure Dev Spaces 的命名空間可以有父/子關聯性，這可讓您在整個父系和子命名空間之間的 pod 之間路由傳送網路流量。 Azure Dev Spaces 也會使用自己的 FQDN 來公開服務端點。 若要設定公開服務的不同方式，以及它如何影響命名空間層級路由，請參閱 [使用不同的端點選項][endpoint-options]。

## <a name="using-azure-cni"></a>使用 Azure CNI

根據預設，AKS 叢集會設定為使用 [kubenet][aks-kubenet] 的網路功能，可搭配 Azure Dev Spaces 使用。 您也可以將 AKS 叢集設定為使用 [Azure 容器網路介面 (CNI) ][aks-cni]。 若要在您的 AKS 叢集上搭配使用 Azure Dev Spaces 與 Azure CNI，可讓您的虛擬網路和子網位址空間最多10個私人 IP 位址，以供 Azure Dev Spaces 所部署的 pod 使用。 如需有關允許私人 IP 位址的詳細資訊，請 [參閱 AKS AZURE CNI 檔][aks-cni-ip-planning]。

## <a name="using-api-server-authorized-ip-ranges"></a>使用 API 伺服器授權的 IP 範圍

AKS 叢集可讓您設定額外的安全性，以限制哪些 IP 位址可以與您的叢集互動，例如使用自訂虛擬網路，或 [使用授權的 IP 範圍來保護對 API 伺服器的存取][aks-ip-auth-ranges]。 若要在 [建立][aks-ip-auth-range-create] 叢集時使用此額外安全性時使用 Azure Dev Spaces，您必須 [根據您的區域允許其他範圍][service-tags]。 您也可以 [更新][aks-ip-auth-range-update] 現有的叢集，以允許這些額外的範圍。 您也需要允許任何連線至 AKS 叢集的開發電腦 IP 位址進行偵錯工具，以連線到您的 API 伺服器。

## <a name="using-aks-private-clusters"></a>使用 AKS 私人叢集

[AKS 私人][aks-private-clusters]叢集目前不支援 Azure Dev Spaces。

## <a name="using-different-endpoint-options"></a>使用不同的端點選項

Azure Dev Spaces 可以選擇為在 AKS 上執行的服務公開端點。 當您在叢集上啟用 Azure Dev Spaces 時，您可以使用下列選項來設定叢集的端點類型：

* *公用*端點（預設值）會部署具有公用 IP 位址的輸入控制器。 公用 IP 位址會在叢集的 DNS 上註冊，讓您能夠使用 URL 來公開存取您的服務。 您可以使用來查看此 URL `azds list-uris` 。
* *私人*端點會部署具有私人 IP 位址的輸入控制器。 使用私人 IP 位址時，您只能從叢集的虛擬網路記憶體取叢集的負載平衡器。 負載平衡器的私人 IP 位址會在叢集的 DNS 上註冊，因此可以使用 URL 來存取叢集虛擬網路內的服務。 您可以使用來查看此 URL `azds list-uris` 。
* 針對 [端點] 選項設定 [ *無* ]，將不會部署任何輸入控制器。 若未部署任何輸入控制器， [Azure Dev Spaces 路由功能][dev-spaces-routing] 將無法運作。 （選擇性）您可以使用 [traefik][traefik-ingress] 或 [NGINX][nginx-ingress]來執行您自己的輸入控制器解決方案，這可讓路由功能再次運作。

若要設定您的端點選項，請在您的叢集上啟用 Azure Dev Spaces 時，使用 *-e* 或 *--端點* 。 例如：

> [!NOTE]
> Endpoint 選項需要您執行 Azure CLI 2.2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>用戶端需求

Azure Dev Spaces 使用用戶端工具（例如 Azure Dev Spaces CLI 延伸模組、Visual Studio Code 擴充功能和 Visual Studio 延伸模組）來與您的 AKS 叢集進行通訊，以進行偵錯工具。 若要使用 Azure Dev Spaces 用戶端工具，請允許從開發電腦到 [Azure Dev Spaces 基礎結構][dev-spaces-allow-infrastructure]的流量。 如果使用 [API 伺服器授權的 IP 範圍][auth-range-section]，您也必須允許任何連線至 AKS 叢集的開發電腦 IP 位址進行偵錯工具，以連線到您的 API 伺服器。

## <a name="next-steps"></a>後續步驟

深入了解 Azure Dev Spaces 的運作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 如何運作](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags