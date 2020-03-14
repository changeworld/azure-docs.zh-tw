---
title: 在不同的網路拓撲中設定 Azure Dev Spaces 的網路功能
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: 描述在 Azure Kubernetes Services 中執行 Azure Dev Spaces 的網路需求
keywords: Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，CNI，kubenet，SDN，網路
ms.openlocfilehash: 9e32e3b65451dceefaeeaf7faed7c8337797e0b8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265346"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>在不同的網路拓撲中設定 Azure Dev Spaces 的網路功能

Azure Dev Spaces 在具有預設網路設定的 Azure Kubernetes Service （AKS）叢集上執行。 如果您想要變更 AKS 叢集的網路設定（例如將叢集放在防火牆後方、使用網路安全性群組，或使用網路原則），您必須納入執行 Azure Dev Spaces 的其他考慮。

![虛擬網路設定](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>虛擬網路或子網設定

您的 AKS 叢集可能會有不同的虛擬網路或子網設定，以限制 AKS 叢集的輸入或輸出流量。 例如，您的叢集可能位於防火牆後方，例如 Azure 防火牆，或者您可能會使用網路安全性群組或自訂角色來限制網路流量。

Azure Dev Spaces 具有輸入*和*輸出網路流量，以及*僅*輸入流量的特定需求。 如果您在 AKS 叢集上使用 Azure Dev Spaces，而該叢集的虛擬網路或子網設定會限制 AKS 叢集的流量，則您必須遵循下列僅輸入和輸出流量需求，才能 Azure Dev Spaces正常運作。

### <a name="ingress-and-egress-network-traffic-requirements"></a>輸入和輸出網路流量需求

Azure Dev Spaces 需要下列 Fqdn 的輸入和輸出流量：

| FQDN                       | 連接埠       | 使用      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS：443 | 提取 docker 映射以進行 Azure Dev Spaces |
| gcr.io                     | HTTPS：443 | 提取 helm 映射以進行 Azure Dev Spaces |
| storage.googleapis.com     | HTTPS：443 | 提取 helm 映射以進行 Azure Dev Spaces |
| azds-*. azds. io             | HTTPS：443 | 與 Azure Dev Spaces 控制器的 Azure Dev Spaces 後端服務進行通訊。 您可以在*dataplaneFqdn*的中找到確切的 FQDN `USERPROFILE\.azds\settings.json` |

更新您的防火牆或安全性設定，以允許進出上述所有 Fqdn 的網路流量。 例如，如果您使用防火牆來保護您的網路，則應該將上述 Fqdn 新增至防火牆的應用程式規則，以允許進出這些網域的流量。

### <a name="ingress-only-network-traffic-requirements"></a>僅輸入網路流量需求

Azure Dev Spaces 提供 Kubernetes 的命名空間層級路由，以及使用自己的 FQDN 來公開存取服務。 若要讓這兩項功能都能正常執行，請更新防火牆或網路設定，以允許公用輸入至叢集上 Azure Dev Spaces 輸入控制器的外部 IP 位址。 或者，您可以建立[內部負載平衡器][aks-internal-lb]，並在防火牆中新增 NAT 規則，將防火牆的公用 ip 轉譯為內部負載平衡器的 ip。 您也可以使用[traefik][traefik-ingress]或[NGINX][nginx-ingress]來建立自訂輸入控制器。

## <a name="aks-cluster-network-requirements"></a>AKS 叢集網路需求

AKS 可讓您使用[網路原則][aks-network-policies]來控制叢集中 pod 之間的輸入和輸出流量，以及來自 pod 的輸出流量。 Azure Dev Spaces 具有輸入*和*輸出網路流量，以及*僅*輸入流量的特定需求。 如果您在具有 AKS 網路原則的 AKS 叢集上使用 Azure Dev Spaces，則必須遵循下列僅輸入和輸出流量需求，Azure Dev Spaces 才能正常運作。

### <a name="ingress-and-egress-network-traffic-requirements"></a>輸入和輸出網路流量需求

Azure Dev Spaces 可讓您直接與叢集中的開發人員空間中的 pod 進行通訊，以進行偵錯工具。 若要讓這項功能能夠正常執行，請新增網路原則，允許輸入和輸出通訊到 Azure Dev Spaces 基礎結構的 IP 位址，這[會因區域而異][dev-spaces-ip-auth-range-regions]。

### <a name="ingress-only-network-traffic-requirements"></a>僅輸入網路流量需求

Azure Dev Spaces 提供跨命名空間的 pod 之間的路由。 例如，啟用 Azure Dev Spaces 的命名空間可以具有父系/子系關聯性，這可讓跨父系和子命名空間的 pod 之間路由傳送網路流量。 若要讓這項功能能夠正常執行，請新增網路原則，以允許在路由傳送網路流量的命名空間（例如，父系/子命名空間）之間的流量。 此外，如果輸入控制器已部署至*azds*命名空間，則輸入控制器必須與不同命名空間中的 Azure 開發人員空間所檢測的 pod 進行通訊。 若要讓輸入控制器正常運作，必須允許從*azds*命名空間到已檢測 pod 執行所在的命名空間的網路流量。

## <a name="using-azure-cni"></a>使用 Azure CNI

根據預設，AKS 叢集會設定為使用[kubenet][aks-kubenet]的網路功能，可與 Azure Dev Spaces 搭配運作。 您也可以將 AKS 叢集設定為使用[Azure 容器網路介面（CNI）][aks-cni]。 若要在您的 AKS 叢集上使用 Azure Dev Spaces 搭配 Azure CNI，請針對 Azure Dev Spaces 所部署的 pod，允許您的虛擬網路和子網位址空間最多10個私人 IP 位址。 如需允許私人 IP 位址的詳細資訊，請[參閱 AKS AZURE CNI 檔][aks-cni-ip-planning]。

## <a name="using-api-server-authorized-ip-ranges"></a>使用 API 伺服器授權的 IP 範圍

AKS 叢集可讓您設定額外的安全性，以限制哪些 IP 位址可以與您的叢集互動，例如使用自訂虛擬網路，或[使用授權的 IP 範圍來保護 API 伺服器的存取權][aks-ip-auth-ranges]。 若要在[建立][aks-ip-auth-range-create]叢集時使用這種額外的安全性 Azure Dev Spaces，您必須根據[您的區域允許額外的範圍][dev-spaces-ip-auth-range-regions]。 您也可以[更新][aks-ip-auth-range-update]現有的叢集，以允許這些額外的範圍。 您也需要允許連線到 AKS 叢集的任何開發電腦的 IP 位址，以進行偵錯工具以連線到您的 API 伺服器。

## <a name="using-aks-private-clusters"></a>使用 AKS 私人叢集

目前， [AKS 私人][aks-private-clusters]叢集不支援 Azure Dev Spaces。

## <a name="client-requirements"></a>用戶端需求

Azure Dev Spaces 使用用戶端工具（例如 Azure Dev Spaces CLI 擴充功能、Visual Studio Code 延伸模組，以及 Visual Studio 擴充功能）與您的 AKS 叢集進行通訊，以進行偵錯工具。 若要使用 Azure Dev Spaces 的用戶端工具，請允許從開發電腦到*azds-\*. azds.io*網域的流量。 如需確切的 FQDN，請參閱 `USERPROFILE\.azds\settings.json` 中的*dataplaneFqdn* 。 如果使用[API 伺服器授權的 IP 範圍][auth-range-section]，您也需要允許連線到您的 AKS 叢集的任何開發電腦的 ip 位址，以進行偵錯工具以連線到您的 API 伺服器。

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-quickstart]

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
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md