---
title: 為不同網路拓撲中的 Azure 開發空間設定網路
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: 描述在 Azure 庫伯奈斯服務中執行 Azure 開發空間的網路要求
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器、CNI、kubenet、SDN、網路
ms.openlocfilehash: 3e344576caf276ae7cb5fe00395c84810a4e7d32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262038"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>為不同網路拓撲中的 Azure 開發空間設定網路

Azure 開發空間在具有預設網路配置的 Azure 庫伯奈斯服務 (AKS) 群集上運行。 如果要更改 AKS 群集的網路配置(例如將群集置於防火牆後、使用網路安全組或使用網路策略),則必須合併運行 Azure Dev Space 的其他注意事項。

![虛擬網路設定](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>虛擬網路或子網路設定

AKS 群集可能具有不同的虛擬網路或子網配置,以限制 AKS 群集的入口或出口流量。 例如,群集可能落後於防火牆(如 Azure 防火牆),或者您可以使用網路安全組或自定義角色來限制網路流量。 您可以在[GitHub 上的 Azure 開發人員空間範例儲存庫][sample-repo]中找到一個範例網路配置。

Azure 開發空間對*入口和出口*網路流量以及*僅入口*流量有某些要求。 如果在 AKS 群集上使用 Azure 開發人員空間,其虛擬網路或子網配置限制 AKS 叢集的流量,則必須僅遵循以下入口和入口流量要求,以便 Azure 開發空間正常運行。

### <a name="ingress-and-egress-network-traffic-requirements"></a>入口和出口網路流量要求

Azure 開發空間需要以下 FQDN 的入口和出口流量:

| FQDN                       | 連接埠       | 使用      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS：443 | 為 Azure 開發空間擷取 Docker 映像 |
| gcr.io                     | HTTPS：443 | 為 Azure 開發空間拉舵映射 |
| storage.googleapis.com     | HTTPS：443 | 為 Azure 開發空間拉舵映射 |
| 阿茲茲-*.azds.io             | HTTPS：443 | 與 Azure 開發人員空間控制器的 Azure 開發空間後端服務進行通信。 確切的 FQDN 可在*數據平面Fqdn*中找到。`USERPROFILE\.azds\settings.json` |

更新防火牆或安全配置,以允許網路流量與上述所有 FQDN 進行傳輸。 例如,如果您使用防火牆來保護網路,則應將上述 FQDN 添加到防火牆的應用程式規則中,以允許流量來自這些域。

### <a name="ingress-only-network-traffic-requirements"></a>只入口網路流量要求

Azure 開發空間提供 Kubernetes 命名空間級路由以及使用其自己的 FQDN 對服務的公共存取。 要使這兩個功能都正常工作,請更新防火牆或網路配置,以允許公共進入群集上的 Azure Dev Space 入口控制器的外部 IP 位址。 或者,您可以創建[內部負載均衡器][aks-internal-lb]並在防火牆中添加 NAT 規則,將防火牆的公共 IP 轉換為內部負載均衡器的 IP。 您還可以使用[traefik][traefik-ingress]或[NGINX][nginx-ingress]創建自訂入口控制器。

## <a name="aks-cluster-network-requirements"></a>AKS 叢集網路要求

AKS 允許您使用[網路策略][aks-network-policies]來控制群集上的 Pod 之間的入口和出口流量以及來自 Pod 的出入口流量。 Azure 開發空間對*入口和出口*網路流量以及*僅入口*流量有某些要求。 如果在 AKS 群集上使用 Azure 開發人員空間,並且使用 AKS 網路策略,則必須僅遵循以下入口以及入口和出口流量要求,以便 Azure 開發空間正常運行。

### <a name="ingress-and-egress-network-traffic-requirements"></a>入口和出口網路流量要求

Azure 開發人員空間允許您直接與群集上的開發空間中的窗格進行通信以進行調試。 要此功能正常工作,新增網路政策,允許入口和傳出通訊到 Azure 開發人員空間基礎結構的 IP 位址,[該位址因區域而異][dev-spaces-ip-auth-range-regions]。

### <a name="ingress-only-network-traffic-requirements"></a>只入口網路流量要求

Azure 開發空間提供跨命名空間的窗格之間的路由。 例如,啟用 Azure 開發人員空間的命名空間可以具有父/子關係,這允許在父和子命名空間的 pod 之間路由網路流量。 Azure 開發空間還使用其自己的 FQDN 公開服務終結點。 要設定不同的公開服務的方式及其如何影響命名空間等級路由,請參閱[使用不同的終結點選項][endpoint-options]。

## <a name="using-azure-cni"></a>使用 Azure CNI

默認情況下,AKS 群集配置為將[kubenet][aks-kubenet]用於網路,該群集適用於 Azure 開發空間。 還可以將 AKS 群集配置為使用[Azure 容器網路介面 (CNI)。][aks-cni] 要在 AKS 群集上使用 Azure 開發人員空間與 Azure CNI 一起使用,請允許虛擬網路和子網位址空間最多 10 個專用 IP 位址,用於 Azure 開發空間部署的 Pod。 有關允許專用 IP 位址的更多詳細資訊,請參閱[AKS Azure CNI 文件][aks-cni-ip-planning]。

## <a name="using-api-server-authorized-ip-ranges"></a>使用 API 伺服器授權的 IP 範圍

AKS 叢集允許您設定其他安全性,以限制哪些 IP 位址可以與叢集互動,例如使用自訂虛擬網路或使用[授權的 IP 範圍保護對 API 伺服器的存取][aks-ip-auth-ranges]。 使用[此][aks-ip-auth-range-create]附加安全性時使用 Azure 開發人員空間,必須[允許基於區域的其他範圍][dev-spaces-ip-auth-range-regions]。 您還可以[更新][aks-ip-auth-range-update]現有群集以允許這些其他範圍。 您還需要允許連接到 AKS 群集的任何開發電腦的 IP 位址進行除錯以連接到 API 伺服器。

## <a name="using-aks-private-clusters"></a>使用 AKS 專用叢集

此時[,AKS 專用群集][aks-private-clusters]不支援 Azure 開發空間。

## <a name="using-different-endpoint-options"></a>使用不同的終結點選項

Azure 開發空間可以選擇公開在 AKS 上運行的服務的終結點。 在群組上啟用 Azure 開發空間時,有以下選項用於設定叢集的終結點類型:

* *公共*終結點(預設值為預設終結點)部署具有公共 IP 位址的入口控制器。 公共 IP 位址在群集的 DNS 上註冊,允許使用 URL 公開存取您的服務。 您可以使用 查看此`azds list-uris`URL。
* *專用*終結點部署具有專用 IP 位址的入口控制器。 使用專用 IP 位址時,只能透過群集的虛擬網路內部存取群集的負載均衡器。 負載均衡器的專用 IP 位址在群集的 DNS 上註冊,以便可以使用 URL 訪問群集虛擬網路中的服務。 您可以使用 查看此`azds list-uris`URL。
* 為終結點選項設置 *「無」* 會導致不部署入口控制器。 未部署入口控制器[,Azure 開發空間路由功能][dev-spaces-routing]將不起作用。 或者,您可以使用[traefik][traefik-ingress]或[NGINX][nginx-ingress]實現您自己的入口控制器解決方案,這將允許路由功能再次工作。

要設定終結點選項,在叢集上啟用 Azure 開發空間時,請使用 *-e*或 *--終結點*。 例如：

> [!NOTE]
> 終結點選項要求您運行 Azure CLI 版本 2.2.0 或更高版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>用戶端需求

Azure 開發空間使用用戶端工具(如 Azure 開發空間 CLI 擴展、可視化工作室代碼擴展和可視化工作室擴展)與 AKS 群集進行調試。 要使用 Azure 開發人員空間用戶端工具,請允許從開發電腦到*azds-.azds.io\** 網域的流量。 有關確切的`USERPROFILE\.azds\settings.json`*FQDN,請參閱數據平面Fqdn。* 如果使用[API 伺服器授權的 IP 範圍][auth-range-section],還需要允許連接到 AKS 叢集的任何開發電腦的 IP 位址進行除錯以連接到 API 伺服器。

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
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[team-quickstart]: quickstart-team-development.md