---
title: 有關 Azure 開發空間的常見問題
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: 查找有關 Azure 開發空間的一些常見問題的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: e7b4620faa01aa9f6d46c34bafb1c623c338beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240506"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>有關 Azure 開發空間的常見問題

這解決了有關 Azure 開發空間的常見問題。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>哪些 Azure 區域當前提供 Azure 開發空間？

有關可用區域的完整清單，請參閱[支援的區域][supported-regions]。

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>是否可以將具有 Azure 開發空間的 AKS 群集遷移到其他區域？

是的，如果要將具有 Azure 開發人員空間的 AKS 群集移動到其他[受支援的區域][supported-regions]，我們建議您在其他區域中創建新群集，然後安裝和配置 Azure 開發空間，並將資源和應用程式部署到新群集。 有關遷移 AKS 的詳細資訊，請參閱[遷移到 Azure 庫伯奈斯服務 （AKS）。][aks-migration]

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>是否可以將 Azure 開發人員空間與現有的 Dockerfile 或 Helm 圖表一起使用？

是，如果專案已具有 Dockerfile 或 Helm 圖表，則可以使用這些檔與 Azure 開發空間一起。 運行`azds prep`時，請使用 參數`--chart`並指定圖表的位置。 Azure 開發人員空間仍將生成*azds.yaml*和*Dockerfile.開發*檔，但不會替換或修改現有的 Dockerfile 或 Helm 圖表。 您可能需要修改*azds.yaml*和*Dockerfile.開發*檔，以便在運行時`azds up`使所有內容都與現有應用程式正確配合使用。

使用您自己的 Dockerfile 或 Helm 圖表時，有以下限制：
* 如果僅使用一個 Dockerfile，它必須包含啟用開發方案所需的一切，例如語言 SDK，而不僅僅是運行時。 如果對 Azure 開發人員空間（如 Dockerfile.開發）使用單獨的 Dockerfile，則啟用開發方案所需的一切必須包含在該 Dockerfile 中。
* Helm 圖表必須支援將部分或整個圖像標記作為*值從值*傳遞。
* 如果要使用入口修改任何內容，還可以更新 Helm 圖表以使用 Azure 開發人員空間提供的入口解決方案。
* 如果要使用[Azure 開發人員空間提供的路由功能][dev-spaces-routing]，則單個專案的所有服務必須適合單個 Kubernetes 命名空間，並且必須使用簡單的命名（例如*服務 -a）* 進行部署。 在標準 Helm 圖表中，可以通過指定*全名覆蓋*屬性的值來完成此具名更新。

要將您自己的 Dockerfile 或 Helm 圖表與適用于 Azure 開發人員空間的現有版本進行比較，請查看[快速入門][quickstart-cli]中生成的檔。


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>是否可以修改 Azure 開發空間生成的檔？

可以，您可以在[準備專案時修改 Azure 開發人員空間生成的][dev-spaces-prep] *azds.yaml*檔、Dockerfile 和 Helm 圖表。 修改這些檔會更改專案的生成和運行方式。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>是否可以在沒有公共 IP 位址的情況下使用 Azure 開發空間？

否，如果沒有公共 IP，無法在 AKS 群集上預配 Azure 開發空間。 [Azure 開發空間需要][dev-spaces-routing]公共 IP 才能進行路由。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>是否可以使用自己的入口與 Azure 開發空間？

可以，您可以沿入口 Azure 開發人員空間創建的一側配置自己的入口。 例如，您可以使用[曲解][ingress-traefik]或[NGINX][ingress-nginx]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>是否可以將 HTTPS 與 Azure 開發空間一起使用？

是的，您可以使用[traefik][ingress-https-traefik]或[NGINX][ingress-https-nginx]使用 HTTPS 配置自己的入口。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>是否可以在使用 CNI 而不是 kubenet 的群集上使用 Azure 開發空間？ 

可以，您可以在使用 CNI 進行網路化的 AKS 群集上使用 Azure 開發空間。 例如，可以將 AKS 群集上的 Azure 開發空間與[現有的 Windows 容器][windows-containers]一起使用，該容器使用 CNI 進行網路化。 有關使用 CNI 與 Azure 開發空間聯網的詳細資訊，[請訪問此處](configure-networking.md#using-azure-cni)。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>是否可以將 Azure 開發空間與 Windows 容器一起使用？

目前，Azure 開發人員空間僅用於 Linux pod 和節點，但您可以在具有[現有 Windows 容器][windows-containers]的 AKS 群集上運行 Azure 開發人員空間。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>啟用 API 伺服器授權的 IP 位址範圍後，是否可以在 AKS 群集上使用 Azure 開發空間？

可以，您可以在啟用[API 伺服器授權的 IP 位址範圍][aks-auth-range]的 AKS 群集上使用 Azure 開發空間。 有關使用具有 AZURE 開發空間啟用的 API 伺服器授權的 IP 位址範圍的 AKS 群集的詳細資訊[，可在此處獲取](configure-networking.md#using-api-server-authorized-ip-ranges)。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>是否可以在具有叢集節點出口流量受限的 AKS 群集上使用 Azure 開發空間？

可以，在允許正確的 FQDN 後，可以在 AKS 群集上使用 Azure 開發空間，對[叢集節點啟用受限出口流量][aks-restrict-egress-traffic]。 有關使用具有受限出口流量的 AKS 群集，以及使用 Azure 開發空間啟用的叢集節點的詳細資訊[，可在此處獲取](configure-networking.md#ingress-and-egress-network-traffic-requirements)。

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>是否可以在啟用 RBAC 的 AKS 群集上使用 Azure 開發空間？

可以，您可以在啟用或未啟用 RBAC 的 AKS 群集上使用 Azure 開發空間。

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>當我在 Visual Studio 中為專案啟用入口時會發生什麼情況？

使用 Visual Studio 準備專案時，您可以選擇為服務啟用入口。 啟用入口將創建一個公共終結點，用於在 AKS 群集上運行時訪問服務，這是可選的。 如果不啟用入口，則服務只能通過 AKS 群集內訪問。

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>是否可以使用具有 Azure 開發空間的窗格託管標識？

目前，Azure 開發人員空間不支援在啟用 Azure 開發空間的 AKS 群集上使用[pod 託管標識][aks-pod-managed-id]。 如果安裝了 pod 託管標識，並希望卸載它，則可以在[卸載說明][aks-pod-managed-id-uninstall]中找到更多詳細資訊。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md