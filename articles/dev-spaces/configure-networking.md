---
title: 在不同的網路拓撲中設定 Azure Dev Spaces 的網路功能
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes Services 中執行 Azure Dev Spaces 的網路需求
keywords: Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，CNI，kubenet，SDN，網路
ms.openlocfilehash: 0d9ebbec3e3c07a466acb58e88b67e6a32a20edb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214162"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>在不同的網路拓撲中設定 Azure Dev Spaces 的網路功能

Azure Dev Spaces 會使用預設網路設定，在 Azure Kubernetes Service (AKS) 叢集上執行。 如果您想要變更 AKS 叢集的網路設定（例如將叢集放在防火牆後方、使用網路安全性群組，或使用網路原則），您必須納入執行 Azure Dev Spaces 的其他考慮。

![虛擬網路設定](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>虛擬網路或子網設定

您的 AKS 叢集可能會有不同的虛擬網路或子網設定，以限制 AKS 叢集的輸入或輸出流量。 例如，您的叢集可能位於防火牆後方，例如 Azure 防火牆，或者您可能會使用網路安全性群組或自訂角色來限制網路流量。 您可以在 [GitHub 上的 Azure Dev Spaces 範例存放庫][sample-repo]中找到範例網路設定。

Azure Dev Spaces 具有輸入 *和* 輸出網路流量，以及 *僅* 輸入流量的特定需求。 如果您在 AKS 叢集上使用 Azure Dev Spaces，且該叢集的虛擬網路或子網設定會限制 AKS 叢集的流量，則您必須遵循下列僅輸入和輸出流量需求，Azure Dev Spaces 才能正常運作。

### <a name="ingress-and-egress-network-traffic-requirements"></a>輸入和輸出網路流量需求

Azure Dev Spaces 需要下列 Fqdn 的輸入和輸出流量：

| FQDN                       | 連接埠       | 使用      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS：443 | 提取 docker 映射以進行 Azure Dev Spaces |
| gcr.io                     | HTTPS：443 | 提取 helm 映射以進行 Azure Dev Spaces |
| storage.googleapis.com     | HTTPS：443 | 提取 helm 映射以進行 Azure Dev Spaces |

更新您的防火牆或安全性設定，以允許進出上述所有 Fqdn 和 [Azure Dev Spaces 基礎結構服務][service-tags]的網路流量。 例如，如果您使用防火牆來保護您的網路，則應該將上述 Fqdn 新增至防火牆的應用程式規則，而且 Azure Dev Spaces 服務標籤也必須 [新增至防火牆][firewall-service-tags]。 這兩個防火牆的更新都必須允許進出這些網域的流量。

### <a name="ingress-only-network-traffic-requirements"></a>僅輸入網路流量需求

Azure Dev Spaces 提供 Kubernetes 的命名空間層級路由，以及使用自己的 FQDN 來公開存取服務。 若要讓這兩項功能都能正常執行，請更新防火牆或網路設定，以允許公用輸入至叢集上 Azure Dev Spaces 輸入控制器的外部 IP 位址。 或者，您可以建立 [內部負載平衡器][aks-internal-lb] ，並在防火牆中新增 NAT 規則，將防火牆的公用 ip 轉譯為內部負載平衡器的 ip。 您也可以使用 [traefik][traefik-ingress] 或 [NGINX][nginx-ingress] 來建立自訂輸入控制器。

## <a name="aks-cluster-network-requirements"></a>AKS 叢集網路需求

AKS 可讓您使用 [網路原則][aks-network-policies] 來控制叢集中 pod 之間的輸入和輸出流量，以及來自 pod 的輸出流量。 Azure Dev Spaces 具有輸入 *和* 輸出網路流量，以及 *僅* 輸入流量的特定需求。 如果您在具有 AKS 網路原則的 AKS 叢集上使用 Azure Dev Spaces，則必須遵循下列僅輸入和輸出流量需求，Azure Dev Spaces 才能正常運作。

### <a name="ingress-and-egress-network-traffic-requirements"></a>輸入和輸出網路流量需求

Azure Dev Spaces 可讓您直接與叢集中的開發人員空間中的 pod 進行通訊，以進行偵錯工具。 若要讓這項功能能夠正常執行，請新增網路原則，允許輸入和輸出通訊到 Azure Dev Spaces 基礎結構的 IP 位址，這 [會因區域而異][service-tags]。

### <a name="ingress-only-network-traffic-requirements"></a>僅輸入網路流量需求

Azure Dev Spaces 提供跨命名空間的 pod 之間的路由。 例如，啟用 Azure Dev Spaces 的命名空間可以具有父系/子系關聯性，這可讓跨父系和子命名空間的 pod 之間路由傳送網路流量。 Azure Dev Spaces 也會使用自己的 FQDN 公開服務端點。 若要設定公開服務的不同方式，以及它如何影響命名空間層級路由，請參閱 [使用不同的端點選項][endpoint-options]。

## <a name="using-azure-cni"></a>使用 Azure CNI

根據預設，AKS 叢集會設定為使用 [kubenet][aks-kubenet] 的網路功能，可與 Azure Dev Spaces 搭配運作。 您也可以將 AKS 叢集設定為使用 [Azure Container 網路介面， (CNI) ][aks-cni]。 若要在您的 AKS 叢集上使用 Azure Dev Spaces 搭配 Azure CNI，請針對 Azure Dev Spaces 所部署的 pod，允許您的虛擬網路和子網位址空間最多10個私人 IP 位址。 如需允許私人 IP 位址的詳細資訊，請 [參閱 AKS AZURE CNI 檔][aks-cni-ip-planning]。

## <a name="using-api-server-authorized-ip-ranges"></a>使用 API 伺服器授權的 IP 範圍

AKS 叢集可讓您設定額外的安全性，以限制哪些 IP 位址可以與您的叢集互動，例如使用自訂虛擬網路，或 [使用授權的 IP 範圍來保護 API 伺服器的存取權][aks-ip-auth-ranges]。 若要在 [建立][aks-ip-auth-range-create] 叢集時使用這種額外的安全性 Azure Dev Spaces，您必須根據 [您的區域允許額外的範圍][service-tags]。 您也可以 [更新][aks-ip-auth-range-update] 現有的叢集，以允許這些額外的範圍。 您也需要允許連線到 AKS 叢集的任何開發電腦的 IP 位址，以進行偵錯工具以連線到您的 API 伺服器。

## <a name="using-aks-private-clusters"></a>使用 AKS 私人叢集

目前， [AKS 私人][aks-private-clusters]叢集不支援 Azure Dev Spaces。

## <a name="using-different-endpoint-options"></a>使用不同的端點選項

Azure Dev Spaces 可以選擇為您在 AKS 上執行的服務公開端點。 在叢集上啟用 Azure Dev Spaces 時，您可以使用下列選項來設定叢集的端點類型：

* *公用*端點（這是預設值）會部署具有公用 IP 位址的輸入控制器。 公用 IP 位址會在叢集的 DNS 上註冊，允許使用 URL 對您的服務進行公用存取。 您可以使用來查看此 URL `azds list-uris` 。
* *私*用端點會部署具有私人 IP 位址的輸入控制器。 使用私人 IP 位址時，您的叢集的負載平衡器只能從叢集的虛擬網路中存取。 負載平衡器的私人 IP 位址會在叢集的 DNS 上註冊，因此可以使用 URL 來存取叢集虛擬網路內的服務。 您可以使用來查看此 URL `azds list-uris` 。
* 針對 [端點] 選項設定 [ *無* ] 會導致不部署輸入控制器。 若未部署輸入控制器， [Azure Dev Spaces 路由功能][dev-spaces-routing] 將無法使用。 （選擇性）您可以使用 [traefik][traefik-ingress] 或 [NGINX][nginx-ingress]來執行您自己的輸入控制器解決方案，這將可讓路由功能再次工作。

若要設定您的端點選項，請在您的叢集上啟用 Azure Dev Spaces 時，使用 *-e* 或 *--endpoint* 。 例如：

> [!NOTE]
> [端點] 選項需要您執行 Azure CLI 版2.2.0 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>用戶端需求

Azure Dev Spaces 使用用戶端工具（例如 Azure Dev Spaces CLI 擴充功能、Visual Studio Code 延伸模組，以及 Visual Studio 擴充功能）與您的 AKS 叢集進行通訊，以進行偵錯工具。 若要使用 Azure Dev Spaces 的用戶端工具，請允許從開發電腦到 [Azure Dev Spaces 基礎結構][dev-spaces-allow-infrastructure]的流量。 如果使用 [API 伺服器授權的 IP 範圍][auth-range-section]，您也需要允許連線到您的 AKS 叢集的任何開發電腦的 ip 位址，以進行偵錯工具以連線到您的 API 伺服器。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Dev Spaces 的運作方式。

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