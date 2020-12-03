---
title: 關於 Azure Dev Spaces 的常見問題
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: 尋找有關 Azure Dev Spaces 的一些常見問題解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548829"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>關於 Azure Dev Spaces 的常見問題

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

這會解決有關 Azure Dev Spaces 的常見問題。

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Azure Dev Spaces 支援哪些版本的 Kubernetes？

Azure Dev Spaces 支援 [目前在 AKS 高達1.18 的正式運作) 版本 Kubernetes 正式 (推出][aks-supported-k8s]。 AKS 上的 Kubernetes 1.19 和更新版本會使用 ContainerD 作為容器執行時間，而不會使用 Azure Dev Spaces。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 區域目前提供 Azure Dev Spaces？

如需可用區域的完整清單，請參閱 [支援的區域][supported-regions] 。

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>我可以將 AKS 叢集 Azure Dev Spaces 遷移到另一個區域嗎？

是的，如果您想要將 AKS 叢集 Azure Dev Spaces 移至另一個 [支援的區域][supported-regions]，建議您在其他區域中建立新的叢集，然後安裝並設定 Azure Dev Spaces，然後將資源和應用程式部署到新的叢集。 如需有關遷移 AKS 的詳細資訊，請參閱 [遷移至 Azure Kubernetes Service (AKS) ][aks-migration]。

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>我可以搭配現有的 Dockerfile 或 Helm 圖表使用 Azure Dev Spaces 嗎？

是的，如果您的專案已經有 Dockerfile 或 Helm 圖表，您可以搭配 Azure Dev Spaces 使用這些檔案。 當您執行時 `azds prep` ，請使用 `--chart` 參數並指定圖表的位置。 Azure Dev Spaces 仍會產生 *azds yaml* 和 *Dockerfile* ，但是它不會取代或修改現有的 Dockerfile 或 Helm 圖表。 您可能需要修改 *azds. yaml* 和 *Dockerfile* ，以便在執行時，讓所有專案都能正確地與您現有的應用程式一起運作 `azds up` 。

使用您自己的 Dockerfile 或 Helm 圖表時，有下列限制：
* 如果只使用一個 Dockerfile，它必須包含啟用開發案例所需的所有專案，例如語言 SDK 而不只是執行時間。 如果針對 Azure Dev Spaces 使用不同的 Dockerfile （例如 Dockerfile），則您必須在該 Dockerfile 中包含啟用開發案例所需的一切。
* 您的 Helm 圖必須支援將部分或整個影像標記作為值 *. yaml* 的值傳遞。
* 如果您要使用輸入來修改任何事物，也可以更新您的 Helm 圖表，以使用 Azure Dev Spaces 所提供的輸入解決方案。
* 如果您想要使用 [Azure Dev Spaces 所提供的路由功能][dev-spaces-routing]，個別專案的所有服務都必須符合單一 Kubernetes 命名空間，而且必須以簡單的命名（例如 *服務-a*）進行部署。 在標準 Helm 圖中，您可以藉由指定 *fullnameOverride* 屬性的值來完成此具名更新。

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>我可以修改 Azure Dev Spaces 產生的檔案嗎？

是，您可以在 [準備專案時修改 Azure Dev Spaces 所產生][dev-spaces-prep]的 *Yaml* 檔案、Dockerfile 和 Helm 圖表。 修改這些檔案會變更建立和執行專案的方式。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>我是否可以在不使用公用 IP 位址的情況下使用 Azure Dev Spaces？

否，您無法在沒有公用 IP 的 AKS 叢集上布建 Azure Dev Spaces。 [路由 Azure Dev Spaces 需要][dev-spaces-routing]公用 IP。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>我可以搭配 Azure Dev Spaces 使用自己的輸入嗎？

是的，您可以設定您自己的輸入，Azure Dev Spaces 所建立的輸入。 例如，您可以使用 [traefik][ingress-traefik] 或 [NGINX][ingress-nginx]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>我可以使用 HTTPS 搭配 Azure Dev Spaces 嗎？

是的，您可以使用 [traefik][ingress-https-traefik] 或 [NGINX][ingress-https-nginx]，以 HTTPS 設定自己的輸入。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>我可以在使用 CNI 而不是 kubenet 的叢集上使用 Azure Dev Spaces 嗎？ 

是的，您可以在使用 CNI 進行網路的 AKS 叢集上使用 Azure Dev Spaces。 例如，您可以在 AKS 叢集上使用具有 [現有 Windows 容器][windows-containers]的 Azure Dev Spaces，這會使用 CNI 來進行網路功能。 如需有關使用 CNI 進行網路功能與 Azure Dev Spaces 的詳細資訊，請參閱 [這裡](configure-networking.md#using-azure-cni)。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>我可以將 Azure Dev Spaces 與 Windows 容器搭配使用嗎？

目前，Azure Dev Spaces 僅適用于 Linux pod 和節點，但您可以在具有 [現有 Windows 容器][windows-containers]的 AKS 叢集上執行 Azure Dev Spaces。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>我可以在已啟用 API 伺服器授權 IP 位址範圍的 AKS 叢集上使用 Azure Dev Spaces 嗎？

是，您可以在已啟用 [API 伺服器授權 IP 位址範圍][aks-auth-range] 的 AKS 叢集上使用 Azure Dev Spaces。 [這裡](configure-networking.md#using-api-server-authorized-ip-ranges)提供使用 AKS 叢集搭配 AZURE DEV SPACES 啟用 API 伺服器授權 IP 位址範圍的詳細資訊。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>我可以在 AKS 叢集上使用 Azure Dev Spaces，在叢集節點上有受限制的輸出流量嗎？

是的，您可以在 AKS 叢集上使用 Azure Dev Spaces，並在允許正確的 Fqdn 之後啟用叢集 [節點的限制輸出流量][aks-restrict-egress-traffic] 。 如需有關使用 AKS 叢集搭配 Azure Dev Spaces 啟用的叢集節點之受限制輸出流量的詳細資訊，請參閱 [這裡](configure-networking.md#ingress-and-egress-network-traffic-requirements)。

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>我可以在已啟用 Kubernetes RBAC 的 AKS 叢集上使用 Azure Dev Spaces 嗎？

是，您可以在 AKS 叢集上使用 Azure Dev Spaces，不論是否有 Kubernetes 角色型存取控制， (Kubernetes RBAC) 已啟用。

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>當我在 Visual Studio 中啟用專案的輸入時，會發生什麼事？

使用 Visual Studio 來準備您的專案時，您可以選擇啟用服務的輸入。 啟用輸入會建立公用端點，以在您的 AKS 叢集上執行時存取您的服務，這是選擇性的。 如果您未啟用輸入，則只能從 AKS 叢集中存取您的服務。

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>我可以使用 pod 受控身分識別搭配 Azure Dev Spaces 嗎？

是，您可以在已啟用 Azure Dev Spaces 的 AKS 叢集上使用 [pod 受控][aks-pod-managed-id] 身分識別，但在您使用 pod 受控身分識別在您的叢集上啟用 Azure Dev Spaces 之後，還有 [其他設定步驟][dev-spaces-pod-managed-id-steps] 。 如果您已安裝 pod 受控識別，而且想要將其卸載，您可以在 [卸載附注][aks-pod-managed-id-uninstall]中找到更多詳細資料。

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>我可以在應用程式中使用 Azure Dev Spaces 搭配多個微服務嗎？

是的，您可以在具有多個微服務的應用程式中使用 Azure Dev Spaces，但您必須在其根目錄中準備並執行個別微服務。 Azure Dev Spaces CLI、Azure Dev Spaces VS Code 擴充功能和 Visual Studio Azure 開發工作負載都預期 *azds yaml* 檔案必須位於微服務的根目錄，才能執行和偵錯工具。 如需在單一應用程式中多個微服務的範例，請參閱 [自行車共用範例應用程式][bike-sharing] 。

在 Visual Studio Code 中，您可以 [在單一工作區中開啟個別的專案][vs-code-multi-root-workspaces] ，並透過 Azure Dev Spaces 分別進行偵錯工具。 每個專案都必須是獨立的，而且已準備好進行 Azure Dev Spaces。

在 Visual Studio 中，您可以設定 .NET Core 解決方案，以透過 Azure Dev Spaces 進行偵錯工具。

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>我可以搭配服務網格使用 Azure Dev Spaces 嗎？

目前，您不能將 Azure Dev Spaces 與服務網格（例如 [Istio][istio] 或 [Linkerd][linkerd]）搭配使用。 您可以在相同的 AKS 叢集上執行 Azure Dev Spaces 和服務網格，但不能同時在相同的命名空間中啟用 Azure Dev Spaces 和服務網格。

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