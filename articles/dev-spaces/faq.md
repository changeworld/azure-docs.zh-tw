---
title: 關於 Azure Dev Spaces 的常見問題
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: 尋找一些關於 Azure Dev Spaces 常見問題的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: 739cfdb4f930d384e180ffd0f3ce2311cd41c70a
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207983"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>關於 Azure Dev Spaces 的常見問題

這會解決 Azure Dev Spaces 的常見問題。

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Azure Dev Spaces 支援哪些版本的 Kubernetes？

Azure Dev Spaces [在 AKS 中支援所有目前支援的一般可用性 (GA) 版本的 Kubernetes][aks-supported-k8s]。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 區域目前提供 Azure Dev Spaces？

如需可用區域的完整清單，請參閱 [支援的區域][supported-regions] 。

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>我可以將 AKS 叢集與 Azure Dev Spaces 遷移至另一個區域嗎？

是，如果您想要將 AKS 叢集與 Azure Dev Spaces 移至另一個 [支援的區域][supported-regions]，建議您在其他區域中建立新的叢集，然後安裝和設定 Azure Dev Spaces，並將您的資源和應用程式部署到新的叢集。 如需有關遷移 AKS 的詳細資訊，請參閱 [ (AKS) 遷移至 Azure Kubernetes Service ][aks-migration]。

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>我可以搭配現有的 Dockerfile 或 Helm 圖表使用 Azure Dev Spaces 嗎？

是，如果您的專案已經有 Dockerfile 或 Helm 圖表，您可以將這些檔案與 Azure Dev Spaces 搭配使用。 當您執行時 `azds prep` ，請使用 `--chart` 參數並指定圖表的位置。 Azure Dev Spaces 仍會產生 *azds yaml* 和 *Dockerfile* 檔案，但不會取代或修改現有的 Dockerfile 或 Helm 圖表。 您可能需要修改 *azds. yaml* 和 *Dockerfile* 檔案，才能在執行時，讓所有專案正常地與現有的應用程式搭配運作 `azds up` 。

使用您自己的 Dockerfile 或 Helm 圖表時，有下列限制：
* 如果只使用一個 Dockerfile，它必須包含啟用開發案例所需的所有專案，例如語言 SDK，而不只是執行時間。 如果針對 Azure Dev Spaces 使用個別的 Dockerfile （例如 Dockerfile），則您必須在該 Dockerfile 中包含啟用開發案例所需的所有專案。
* 您的 Helm 圖必須支援將部分或整個影像標記當做 *值. yaml*中的值傳遞。
* 如果您要使用輸入來修改任何專案，您也可以更新您的 Helm 圖表，以使用 Azure Dev Spaces 所提供的輸入方案。
* 如果您想要使用 [Azure Dev Spaces 所提供的路由功能][dev-spaces-routing]，則個別專案的所有服務都必須納入單一 Kubernetes 命名空間中，而且必須使用簡單的命名來部署，例如 *服務-a*。 在標準 Helm 圖中，可以藉由指定 *fullnameOverride* 屬性的值來進行此具名更新。

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>我可以修改 Azure Dev Spaces 所產生的檔案嗎？

是，您可以在[準備您的專案時][dev-spaces-prep]，修改 Azure Dev Spaces 所產生的*Yaml*檔案、Dockerfile 和 Helm 圖表。 修改這些檔案會變更專案的建立和執行方式。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>我可以在沒有公用 IP 位址的情況下使用 Azure Dev Spaces 嗎？

否，您無法在沒有公用 IP 的 AKS 叢集上布建 Azure Dev Spaces。 [Azure Dev Spaces 路由需要][dev-spaces-routing]公用 IP。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>我可以搭配 Azure Dev Spaces 使用自己的輸入嗎？

是，您可以在輸入 Azure Dev Spaces 建立的位置設定自己的輸入。 例如，您可以使用 [traefik][ingress-traefik] 或 [NGINX][ingress-nginx]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>我可以搭配使用 HTTPS 與 Azure Dev Spaces 嗎？

是，您可以使用 [traefik][ingress-https-traefik] 或 [NGINX][ingress-https-nginx]來設定您自己的輸入與 HTTPS。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>我可以在使用 CNI 而非 kubenet 的叢集上使用 Azure Dev Spaces 嗎？ 

是，您可以在使用 CNI 進行網路功能的 AKS 叢集上使用 Azure Dev Spaces。 例如，您可以使用 AKS 叢集上的 Azure Dev Spaces 搭配 [現有的 Windows 容器][windows-containers]，其使用 CNI 的網路功能。 如需使用 CNI 與 Azure Dev Spaces 進行網路功能的詳細資訊，請參閱 [這裡](configure-networking.md#using-azure-cni)。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>我可以搭配 Windows 容器使用 Azure Dev Spaces 嗎？

目前，Azure Dev Spaces 僅適用于 Linux pod 和節點，但您可以在具有 [現有 Windows 容器][windows-containers]的 AKS 叢集上執行 Azure Dev Spaces。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>我可以在已啟用 API 伺服器授權 IP 位址範圍的 AKS 叢集上使用 Azure Dev Spaces 嗎？

是，您可以在已啟用 [API 伺服器授權 IP 位址範圍][aks-auth-range] 的 AKS 叢集上使用 Azure Dev Spaces。 如需使用已啟用 Azure Dev Spaces 的 API 伺服器授權 IP 位址範圍之 AKS 叢集的詳細資訊，請參閱 [這裡](configure-networking.md#using-api-server-authorized-ip-ranges)。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>我可以在 AKS 叢集上使用具有叢集節點之受限制輸出流量的 Azure Dev Spaces 嗎？

是的，您可以在 AKS 叢集上使用 Azure Dev Spaces，並在允許正確的 Fqdn 之後啟用叢集 [節點的受限制輸出流量][aks-restrict-egress-traffic] 。 如需有關使用 Azure Dev Spaces 啟用的叢集節點之受限制輸出流量的詳細資訊，請參閱 [這裡](configure-networking.md#ingress-and-egress-network-traffic-requirements)。

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>我可以在已啟用 RBAC 的 AKS 叢集上使用 Azure Dev Spaces 嗎？

是，您可以在 AKS 叢集上使用 Azure Dev Spaces，不論是否已啟用 RBAC。

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>當我在 Visual Studio 中啟用專案的輸入時，會發生什麼事？

使用 Visual Studio 來準備您的專案時，您可以選擇為您的服務啟用輸入。 啟用輸入會建立公用端點，以便在您的 AKS 叢集上執行時存取您的服務，這是選擇性的。 如果您未啟用輸入，您的服務只能從您的 AKS 叢集中存取。

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>我可以搭配 Azure Dev Spaces 使用 pod 受控識別嗎？

是，您可以在啟用 Azure Dev Spaces 的 AKS 叢集上使用 [pod 受控][aks-pod-managed-id] 識別，但在您于叢集上啟用具有 pod 受控識別的 Azure Dev Spaces 之後，還有 [其他設定步驟][dev-spaces-pod-managed-id-steps] 。 如果您已安裝 pod 受控識別，而且想要將它卸載，您可以在 [卸載附注][aks-pod-managed-id-uninstall]中找到更多詳細資料。

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>我可以在應用程式中使用具有多個微服務的 Azure Dev Spaces 嗎？

是，您可以在具有多個微服務的應用程式中使用 Azure Dev Spaces，但您必須在其根目錄準備和執行個別微服務。 Azure Dev Spaces CLI、Azure Dev Spaces VS Code 擴充功能，以及 Visual Studio Azure 開發工作負載，會預期 *yaml* 檔案位於微服務的根目錄，才能執行和 debug。 如需單一應用程式中多個微服務的範例，請參閱 [自行車共用範例應用程式][bike-sharing] 。

在 Visual Studio Code 中，您可以 [在單一工作區中開啟個別的專案][vs-code-multi-root-workspaces] ，並透過 Azure Dev Spaces 分別加以調試。 每個專案都必須各自獨立，並準備好進行 Azure Dev Spaces。

在 Visual Studio 中，您可以設定 .NET Core 解決方案，以透過 Azure Dev Spaces 進行偵錯工具。

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>我可以搭配使用 Azure Dev Spaces 與服務網格嗎？

目前，您無法使用 Azure Dev Spaces 搭配服務網格（例如 [Istio][istio] 或 [Linkerd][linkerd]）。 您可以在相同的 AKS 叢集上執行 Azure Dev Spaces 和服務網格，但不能同時在相同的命名空間中啟用 Azure Dev Spaces 和服務網格。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md