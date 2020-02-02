---
title: 關於 Azure Dev Spaces 的常見問題
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: 尋找一些關於 Azure Dev Spaces 常見問題的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: 964fa9ec4948bf178c310af8e35913fda5f70c0f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934172"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>關於 Azure Dev Spaces 的常見問題

這會解決 Azure Dev Spaces 的常見問題。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 區域目前提供 Azure Dev Spaces？

如需可用區域的完整清單，請參閱[支援的區域][supported-regions]。

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>我可以搭配現有的 Dockerfile 或 Helm 圖表使用 Azure Dev Spaces 嗎？

是，如果您的專案已經有 Dockerfile 或 Helm 圖表，您可以將這些檔案與 Azure Dev Spaces 搭配使用。 當您執行 `azds prep`時，請使用 `--chart` 參數並指定圖表的位置。 Azure Dev Spaces 仍會產生*azds yaml*和*Dockerfile*檔案，但不會取代或修改現有的 Dockerfile 或 Helm 圖表。 您可能需要修改*azds. yaml*和*Dockerfile*檔案，才能在執行 `azds up`時，讓所有專案正常地與現有的應用程式搭配運作。

使用您自己的 Dockerfile 或 Helm 圖表時，有下列限制：
* 如果只使用一個 Dockerfile，它必須包含啟用開發案例所需的所有專案，例如語言 SDK，而不只是執行時間。 如果針對 Azure Dev Spaces 使用個別的 Dockerfile （例如 Dockerfile），則您必須在該 Dockerfile 中包含啟用開發案例所需的所有專案。
* 您的 Helm 圖必須支援將部分或整個影像標記當做*值. yaml*中的值傳遞。
* 如果您要使用輸入來修改任何專案，您也可以更新您的 Helm 圖表，以使用 Azure Dev Spaces 所提供的輸入方案。
* 如果您想要使用[Azure Dev Spaces 所提供的路由功能][dev-spaces-routing]，則個別專案的所有服務都必須納入單一 Kubernetes 命名空間中，而且必須使用簡單的命名來部署，例如*服務-a*。 在標準 Helm 圖中，可以藉由指定*fullnameOverride*屬性的值來進行此具名更新。

若要將您自己的 Dockerfile 或 Helm 圖表與 Azure Dev Spaces 的現有版本進行比較，請參閱[快速入門][quickstart-cli]中所產生的檔案。


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>我可以修改 Azure Dev Spaces 所產生的檔案嗎？

是，您可以在[準備您的專案時][dev-spaces-prep]，修改 Azure Dev Spaces 所產生的*Yaml*檔案、Dockerfile 和 Helm 圖表。 修改這些檔案會變更專案的建立和執行方式。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>我可以在沒有公用 IP 位址的情況下使用 Azure Dev Spaces 嗎？

否，您無法在沒有公用 IP 的 AKS 叢集上布建 Azure Dev Spaces。 [Azure Dev Spaces 路由需要][dev-spaces-routing]公用 IP。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>我可以搭配 Azure Dev Spaces 使用自己的輸入嗎？

是，您可以在輸入 Azure Dev Spaces 建立的位置設定自己的輸入。 例如，您可以使用[traefik][ingress-traefik]或[NGINX][ingress-nginx]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>我可以搭配使用 HTTPS 與 Azure Dev Spaces 嗎？

是，您可以使用[traefik][ingress-https-traefik]或[NGINX][ingress-https-nginx]來設定您自己的輸入與 HTTPS。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>我可以在使用 CNI 而非 kubenet 的叢集上使用 Azure Dev Spaces 嗎？ 

是，您可以在使用 CNI 進行網路功能的 AKS 叢集上使用 Azure Dev Spaces。 例如，您可以使用 AKS 叢集上的 Azure Dev Spaces 搭配[現有的 Windows 容器][windows-containers]，其使用 CNI 的網路功能。 如需使用 CNI 與 Azure Dev Spaces 進行網路功能的詳細資訊，請參閱[這裡](configure-networking.md#using-azure-cni)。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>我可以搭配 Windows 容器使用 Azure Dev Spaces 嗎？

目前，Azure Dev Spaces 僅適用于 Linux pod 和節點，但您可以在具有[現有 Windows 容器][windows-containers]的 AKS 叢集上執行 Azure Dev Spaces。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>我可以在已啟用 API 伺服器授權 IP 位址範圍的 AKS 叢集上使用 Azure Dev Spaces 嗎？

是，您可以在已啟用[API 伺服器授權 IP 位址範圍][aks-auth-range]的 AKS 叢集上使用 Azure Dev Spaces。 如需使用已啟用 Azure Dev Spaces 的 API 伺服器授權 IP 位址範圍之 AKS 叢集的詳細資訊，請參閱[這裡](configure-networking.md#using-api-server-authorized-ip-ranges)。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>我可以在 AKS 叢集上使用具有叢集節點之受限制輸出流量的 Azure Dev Spaces 嗎？

是的，您可以在 AKS 叢集上使用 Azure Dev Spaces，並在允許正確的 Fqdn 之後啟用叢集[節點的受限制輸出流量][aks-restrict-egress-traffic]。 如需有關使用 Azure Dev Spaces 啟用的叢集節點之受限制輸出流量的詳細資訊，請參閱[這裡](configure-networking.md#ingress-and-egress-network-traffic-requirements)。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md