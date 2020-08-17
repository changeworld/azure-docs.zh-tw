---
title: Azure 上的 Kubernetes 教學課程 - 部署叢集
description: 在本 Azure Kubernetes Service (AKS) 教學課程中，您會建立 AKS 叢集，並使用 kubectl 連線至 Kubernetes 主要節點。
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 72c2a664b3994d53fdd3602b432df1cabdaeb3ef
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002962"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>教學課程：部署 Azure Kubernetes Service (AKS) 叢集

Kubernetes 會提供容器化應用程式的分散式平台。 透過 AKS，您可以快速地建立生產環境就緒的 Kubernetes 叢集。 在本教學課程 (3/7 部分) 中，將 Kubernetes 叢集部署在 AKS 中。 您會了解如何：

> [!div class="checklist"]
> * 部署可向 Azure 容器登錄進行驗證的 Kubernetes AKS 叢集
> * 安裝 Kubernetes CLI (kubectl)
> * 設定 kubectl 以連線至您的 AKS 叢集

在其他教學課程中，Azure Vote 應用程式會部署至叢集、進行調整並加以更新。

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已建立容器映像並上傳到 Azure Container Registry 執行個體。 如果您尚未完成這些步驟，而且想要跟著做，請從[教學課程 1 – 建立容器映像][aks-tutorial-prepare-app]開始。

在本教學課程中，您必須執行 Azure CLI 2.0.53 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-a-kubernetes-cluster"></a>建立 Kubernetes 叢集

AKS 叢集可使用 Kubernetes 角色型存取控制 (RBAC)。 這些控制可讓您根據指派給使用者的角色，來定義資源的存取權。 如果為使用者指派了多個角色，即會合併權限，而且可將權限的範圍設定為單一命名空間或整個叢集。 根據預設，Azure CLI 會在您建立 AKS 叢集時自動啟用 RBAC。

使用 [az aks create][] 建立 AKS 叢集。 下列範例會在名為 myResourceGroup  的資源群組中建立名為 myAKSCluster  的叢集。 在[先前的教學課程][aks-tutorial-prepare-acr]中，我們已在 *eastus* 區域建立此資源群組。 下列範例不會指定區域，因此也會在 *eastus* 區域中建立 AKS 叢集。 如需 AKS 的資源限制和區域可用性的詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中的配額、虛擬機器大小限制和區域可用性][quotas-skus-regions]。

為了允許 AKS 叢集與其他 Azure 資源互動，系統會自動建立 Azure Active Directory 服務主體，因為您未指定服務主體。 在這裡，此服務主體已[獲得提取映像的權限][container-registry-integration]，可從先前教學課程中建立的 Azure Container Registry (ACR) 執行個體中提取映像。 請注意，您可以使用[受控識別](use-managed-identity.md)而不是服務主體，以方便管理。

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

您也可以手動設定服務主體，從 ACR 提取映像。 如需詳細資訊，請參閱[使用服務主體進行 ACR 驗證](../container-registry/container-registry-auth-service-principal.md)或[使用提取密碼從 Kubernetes 進行驗證](../container-registry/container-registry-auth-kubernetes.md)。

部署會在數分鐘之後完成，並以 JSON 格式傳回 AKS 部署的相關資訊。

> [!NOTE]
> 若要確保叢集能夠可靠地運作，您應該執行至少 2 個 (兩個) 節點。

## <a name="install-the-kubernetes-cli"></a>安裝 Kubernetes CLI

若要從本機電腦連線至 Kubernetes 叢集，您應使用 [kubectl][kubectl] (Kubernetes 命令列用戶端)。

如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 您也可以使用 [az aks install-cli][] 命令將其安裝於本機：

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>使用 kubectl 連線至叢集

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][] 命令。 下列範例會針對 *myResourceGroup* 中名為 *myAKSCluster* 的 AKS 叢集取得認證：

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證叢集的連線，請執行 [kubectl get nodes][kubectl-get] 命令以傳回叢集節點的清單：

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，Kubernetes 叢集已部署在 AKS 中，且您已設定 `kubectl` 加以連線。 您已了解如何︰

> [!div class="checklist"]
> * 部署可向 Azure 容器登錄進行驗證的 Kubernetes AKS 叢集
> * 安裝 Kubernetes CLI (kubectl)
> * 設定 kubectl 以連線至您的 AKS 叢集

繼續進行下一個教學課程，以了解如何將應用程式部署至叢集。

> [!div class="nextstepaction"]
> [在 Kubernetes 中部署應用程式][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
