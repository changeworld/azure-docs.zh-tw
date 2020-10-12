---
title: 網路資源的最佳作法
titleSuffix: Azure Kubernetes Service
description: 了解叢集操作員在 Azure Kubernetes Service (AKS) 中使用虛擬網路資源和進行連線時的最佳做法
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 9ec6423a853aacbc8a03cc5472bf1a95a5623b1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89482720"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中的網路連線和安全性最佳做法

當您在 Azure Kubernetes Service (AKS) 中建立及管理叢集時，您會為節點和應用程式提供網路連線。 這些網路資源包括 IP 位址範圍、負載平衡器和輸入控制器。 若要維持高品質的應用程式服務，您需要規劃並設定這些資源。

此最佳做法文章著重於叢集操作員的網路連線能力和安全性。 在本文中，您將學會如何：

> [!div class="checklist"]
> * 比較 AKS 中的 Kubenet 和 Azure CNI 網路模式
> * 規劃所需的 IP 位址和連線能力
> * 使用負載平衡器、輸入控制器或 web 應用程式防火牆來散發流量 (WAF) 
> * 安全地連線到叢集節點

## <a name="choose-the-appropriate-network-model"></a>選擇適當的網路模型

**最佳做法指引** - 若要整合現有虛擬網路或內部部署網路，請使用 AKS 中的 Azure CNI 網路功能。 此網路模型也可讓企業環境中的資源和控制項達到更有效的區隔。

虛擬網路會提供存取您應用程式的基本連線能力給 AKS 節點和客戶。 將 AKS 叢集部署到虛擬網路有兩種不同的方式：

* **Kubenet 網路功能** - Azure 會在叢集部署好之後管理虛擬網路資源，並使用 [kubenet][kubenet] Kubernetes 外掛程式。
* **AZURE CNI 網路** 功能-部署至虛擬網路，並使用 [Azure 容器網路介面 (CNI) ][cni-networking] Kubernetes 外掛程式。 Pod 會收到可路由到其他網路服務或內部部署資源的個別 IP。

容器網路介面 (CNI) 是一個「廠商中立」通訊協定，可讓容器執行階段對網路提供者提出要求。 Azure CNI 會將 IP 位址指派給 Pod 和節點，並在您連線至現有 Azure 虛擬網路時提供 IP 位址管理 (IPAM) 功能。 每個節點和 Pod 資源都會收到 Azure 虛擬網路中的 IP 位址，並且不需要使用額外路由來與其他資源或服務通訊。

![此圖表顯示兩個節點，且各有橋接器將其連線至單一 Azure VNet](media/operator-best-practices-network/advanced-networking-diagram.png)

針對生產部署，kubenet 和 Azure CNI 都是有效的選項。

適用于生產環境的 Azure CNI 網路功能有一個值得注意的優點，就是網路模型可分隔資源的控制和管理。 從安全性觀點來看，您通常會想讓不同小組來管理及保護這些資源。 Azure CNI 網路功能可讓您透過指派給每個 Pod 的 IP 位址，直接連線到現有的 Azure 資源、內部部署資源或其他服務。

當您使用 Azure CNI 網路功能時，虛擬網路資源會在 AKS 叢集的不同資源群組中。 您可以將存取和管理這些資源的權限委派給 AKS 服務主體。 AKS 叢集所使用的服務主體在您虛擬網路內的子網路上必須至少具有[網路參與者](../role-based-access-control/built-in-roles.md#network-contributor)權限。 如果您想要定義[自訂角色](../role-based-access-control/custom-roles.md)，而不使用內建的網路參與者角色，則需要下列權限：
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

如需有關 AKS 服務主體委派的詳細資訊，請參閱[委派其他 Azure 資源的存取權][sp-delegation]。 您也可以使用系統指派的受控識別來取得權限，取代服務主體。 如需詳細資訊，請參閱[使用受控識別](use-managed-identity.md)。

當每個節點和 Pod 收到自己的 IP 位址時，請規劃 AKS 子網路的位址範圍。 子網路必須夠大，才能為您部署的每個節點、Pod 和網路資源提供 IP 位址。 每個 AKS 叢集必須放在自己的子網路中。 若要在 Azure 中允許對內部部署或對等互連網路進行連線，請不要使用與現有網路資源重疊的 IP 位址範圍。 Kubenet 和 Azure CNI 網路功能都有預設每個節點可執行的 Pod 數目限制。 若要處理 scale out 事件或叢集升級，您也需要可在指派的子網中使用的其他 IP 位址。 如果您使用 Windows Server 容器，此額外的位址空間特別重要，因為這些節點集區需要升級才能套用最新的安全性修補程式。 如需 Windows Server 節點的詳細資訊，請參閱 [在 AKS 中升級節點集][nodepool-upgrade]區。

若要計算所需的 IP 位址，請參閱[在 AKS 中設定 Azure CNI 網路功能][advanced-networking]。

### <a name="kubenet-networking"></a>Kubenet 網路功能

雖然 Kubenet 不需要在部署叢集之前先設定虛擬網路，但也有些缺點：

* 節點和 Pod 放置於不同的 IP 子網路。 使用者定義路由 (UDR) 和 IP 轉送會用來路由 Pod 和節點之間的流量。 這個額外的路由會降低網路效能。
* 連線至現有內部部署網路或與其他 Azure 虛擬網路對等互連可能會很複雜。

Kubenet 適用於小型的開發或測試工作負載，因為您不需要從 AKS 叢集分別建立虛擬網路和子網路。 使用 Kubenet 網路功能部署的簡單 AKS 叢集適用於流量低的簡易網站，或用於將工作負載隨即轉移到容器中。 您應對大部分的生產環境部署規劃並使用 Azure CNI 網路功能。 您也可以[使用 Kubenet 設定自己的 IP 位址範圍和虛擬網路][aks-configure-kubenet-networking]。

## <a name="distribute-ingress-traffic"></a>分配輸入流量

**最佳做法指引** - 若要將 HTTP 或 HTTPS 流量分散到應用程式，請使用輸入資源和控制器。 輸入控制器會透過一般的 Azure 負載平衡器來提供額外功能，並可作為原生 Kubernetes 資源來加以管理。

Azure 負載平衡器可以將客戶流量分散到 AKS 叢集中的應用程式，但會受限於應用程式對該流量的了解程度。 負載平衡器資源會在第 4 層上運作，並根據通訊協定或連接埠分散流量。 大部分使用 HTTP 或 HTTPS 的 web 應用程式都應該使用 Kubernetes 輸入資源和控制器，其會在第7層運作。 輸入可以根據應用程式的 URL 將流量分散到應用程式，並處理 TLS/SSL 終止。 這項功能也會減少您公開並對應的 IP 位址數目。 若使用負載平衡器，每個應用程式通常需要指派並對應至 AKS 叢集中服務的公用 IP 位址。 若使用輸入資源，單一 IP 位址可以將流量分散到多個應用程式。

![此圖顯示 AKS 叢集中的輸入流量](media/operator-best-practices-network/aks-ingress.png)

 輸入有兩個元件：

 * 輸入「資源」**，以及
 * 輸入 *控制器*

輸入資源是 `kind: Ingress` 的 YAML資訊清單，用來定義主機、憑證及規則，以將流量路由至在您 AKS 叢集中執行的服務。 下列 YAML 資訊清單範例會將 myapp.com** 的流量分散到以下兩個服務的其中一個：blogservice** 或 storeservice**。 系統會根據客戶存取的 URL，將他們導向其中一個服務。

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

輸入控制器是在 AKS 節點上執行的精靈，可監控傳入要求。 接著，流量會根據輸入資源中所定義的規則來分配。 最常見的輸入控制器是以 [NGINX] 為基礎。 AKS 不會限制您使用特定控制器，因此您可以使用 [Contour][contour]、[HAProxy][haproxy] 或 [Traefik][traefik] 等其他控制器。

輸入控制器必須在 Linux 節點上排程。 Windows Server 節點不應執行輸入控制器。 在您的 YAML 資訊清單或 Helm 圖表部署中使用節點選取器，以指出資源應該在以 Linux 為基礎的節點上執行。 如需詳細資訊，請參閱 [使用節點選取器來控制在 AKS 中排程][concepts-node-selectors]pod 的位置。

有許多適用輸入的案例，包括下列的使用說明指南：

* [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
* [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
* [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
* 建立輸入控制器，其使用 Let's Encrypt 自動產生[具有動態公用 IP][aks-ingress-tls] 或[具有靜態公用 IP 位址][aks-ingress-static-tls]的 TLS 憑證

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>使用 Web 應用程式防火牆 (WAF) 來保護流量

**最佳做法指引** - 若要掃描傳入的流量以查看是否有潛在攻擊，請使用 Web 應用程式防火牆 (WAF)，例如，[適用於 Azure 的 Barracuda WAF][barracuda-waf] 或 Azure 應用程式閘道。 這些更先進的網路資源也可以將流量路由傳送到 HTTP 和 HTTPS 連線以外的流量，或只路由基本 TLS 終止。

將流量分散到服務和應用程式的輸入控制器通常是您 AKS 叢集中的 Kubernetes 資源。 控制器會以精靈形式執行於 AKS 節點上，並取用一些節點的資源，例如 CPU、記憶體和網路頻寬。 在較大型的環境中，您通常會想要卸載一些此流量路由或 TLS 終止至 AKS 叢集外部的網路資源。 並且想掃描傳入的流量以查看是否有潛在攻擊。

![Azure 應用程式閘道等 Web 應用程式防火牆 (WAF) 可以保護並分散您 AKS 叢集的流量](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Web 應用程式防火牆 (WAF) 會藉由篩選傳入流量來提供額外一層安全性。 Open Web Application Security Project (OWASP) 會提供一組規則來監看是否有跨網站指令碼或 Cookie 篡改等攻擊。 [Azure 應用程式閘道][app-gateway] (目前在 AKS 中處於預覽狀態) 是一種 WAF，可以與 AKS 叢集整合以提供這些安全性功能，然後再抵達您的 AKS 叢集和應用程式。 其他第三方解決方案也會執行這些功能，因此您可以在指定產品中繼續使用現有的投資或專業技術。

負載平衡器或輸入資源會繼續在您的 AKS 叢集執行，以進一步精簡流量分配。 您可以使用資源定義，將應用程式閘道當作輸入控制器來集中管理。 若要開始使用，[請建立應用程式閘道輸入控制器][app-gateway-ingress]。

## <a name="control-traffic-flow-with-network-policies"></a>使用網路原則控制流量流程

**最佳作法指引** - 使用網路原則允許或拒絕 Pod 的流量。 根據預設，叢集中的 Pod 之間允許所有流量。 為了提升安全性，請定義限制 Pod 通訊的規則。

網路原則是一種 Kubernetes 功能，可讓您控制 Pod 之間的流量。 您可以根據指派的標籤、命名空間或流量連接埠等設定，選擇允許或拒絕流量。 使用網路原則提供了一種雲端原生方法來控制流量的流程。 由於 Pod 是在 AKS 叢集內以動態方式建立的，因此可以自動套用所需的網路原則。 請勿使用 Azure 網路安全性群組來控制 pod-to-pod 流量，請使用網路原則。

若要使用網路原則，必須在建立 AKS 叢集時啟用該功能。 您無法在現有的 AKS 叢集上啟用網路原則。 事先規劃以確保在叢集上啟用網路原則，並可以視需要使用它們。 網路原則只應用於 AKS 中的 Linux 型節點和 Pod。

使用 YAML 資訊清單將網路原則建立為 Kubernetes 資源。 原則會套用至已定義的 Pod，然後輸入或輸出規則可定義流量的流動方式。 下列範例將網路原則套用至已套用 *app: backend* 標籤的 Pod。 然後，輸入規則僅允許來自具有 *app: frontend* 標籤的 Pod 的流量：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

若要開始使用原則，請參閱[使用 Azure Kubernetes Service (AKS) 中的網路原則保護 Pod 之間的流量][use-network-policies]。

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>透過防禦主機安全地連線到節點

**最佳做法指引** - 不要將遠端連線公開給 AKS 節點。 在管理虛擬網路中建立防禦主機或跳躍箱 (jump box)。 您可以使用防禦主機安全地將流量路由到 AKS 叢集，以完成遠端管理工作。

AKS 中的大部分作業都可使用 Azure 管理工具或透過 Kubernetes API 伺服器來完成。 AKS 節點不會連線至公用網際網路，並僅在私人網路上提供。 若要連線至節點並執行維護工作，或針對問題進行疑難排解，請透過防禦主機或跳躍箱 (jump box) 來路由您的連線。 此主機應位於與 AKS 叢集虛擬網路安全對等互連的個別管理虛擬網路。

![使用防禦主機或跳躍箱 (jump box) 連線到 AKS 節點](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

防禦主機的管理網路也應該受到保護。 使用 [Azure ExpressRoute][expressroute] 或 [VPN 閘道][vpn-gateway]來連線到內部部署網路，並使用網路安全性群組來控制存取。

## <a name="next-steps"></a>後續步驟

本文著重於網路連線和安全性。 若要深入了解 Kubernetes 中的網路基本概念，請參閱 [Azure Kubernetes Service (AKS) 中應用程式的網路概念][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool