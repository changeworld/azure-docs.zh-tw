---
title: 使用新的 Azure 應用程式閘道實例來啟用新 AKS 叢集的輸入控制器附加元件
description: 瞭解如何使用 Azure CLI，以新的應用程式閘道實例為新的 AKS 叢集啟用輸入控制器附加元件。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 4634421829cf71c0c5b9476f8ff3d08b9caa7dbd
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117337"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>教學課程：使用新的應用程式閘道實例來啟用新 AKS 叢集的輸入控制器附加元件（預覽）

您可以使用 Azure CLI 來啟用[Azure Kubernetes Services （AKS）](https://azure.microsoft.com/services/kubernetes-service/)叢集的[應用程式閘道輸入控制器（AGIC）](ingress-controller-overview.md)附加元件。 附加元件目前為預覽狀態。

在本教學課程中，您將建立已啟用 AGIC 附加元件的 AKS 叢集。 建立叢集將會自動建立要使用 Azure 應用程式閘道實例。 接著，您將會部署範例應用程式，以使用附加元件透過應用程式閘道公開應用程式。 

附加元件可讓您更快速地部署 AKS 叢集的 AGIC，而非[先前透過 Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)的方式。 它也提供完全受控的體驗。    

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立資源群組。 
> * 建立新的 AKS 叢集，並啟用 AGIC 附加元件。 
> * 在 AKS 叢集上使用 AGIC 來部署範例應用程式。
> * 檢查是否可透過應用程式閘道來連線到應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 版2.0.4 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

使用[az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register)命令來註冊*AKS-IngressApplicationGatewayAddon*功能旗標，如下列範例所示。 當附加元件仍處於預覽狀態時，您只需要針對每個訂用帳戶執行此動作一次。
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間才會顯示狀態 `Registered` 。 您可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令檢查註冊狀態：
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

當您準備好時，請使用[az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register)命令重新整理 microsoft.containerservice 資源提供者的註冊：
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

安裝或更新本教學課程的 aks-preview 延伸模組。 使用下列 Azure CLI 命令：
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。 下列範例會在*canadacentral*位置（區域）中建立名為*myResourceGroup*的資源群組： 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>部署已啟用附加元件的 AKS 叢集

您現在會部署新的 AKS 叢集，並啟用 AGIC 附加元件。 如果您未提供要在此進程中使用的現有應用程式閘道實例，我們將會自動建立並設定新的應用程式閘道實例，以提供 AKS 叢集的流量。  

> [!NOTE]
> 應用程式閘道輸入控制器附加元件*僅*支援應用程式閘道 v2 Sku （STANDARD 和 WAF），而*非*應用程式閘道 v1 sku。 當您透過 AGIC 附加元件部署新的應用程式閘道實例時，只能部署應用程式閘道 Standard_v2 SKU。 如果您想要啟用應用程式閘道 WAF_v2 SKU 的附加元件，請使用下列其中一種方法：
>
> - 透過入口網站在應用程式閘道上啟用 WAF。 
> - 先建立 WAF_v2 應用程式閘道實例，然後遵循如何[使用現有的 AKS 叢集和現有的應用程式閘道實例來啟用 AGIC 附加](tutorial-ingress-controller-add-on-existing.md)元件的指示。 

在下列範例中，您將使用[AZURE CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking)和[受控](https://docs.microsoft.com/azure/aks/use-managed-identity)識別，部署名為*myCluster*的新 AKS 叢集。 AGIC 附加元件將會在您建立的資源群組中啟用， *myResourceGroup*。 

在未指定現有應用程式閘道實例的情況下，若要部署已啟用 AGIC 附加元件的新 AKS 叢集，就表示自動建立 Standard_v2 SKU 應用程式閘道實例。 因此，您也會指定應用程式閘道實例的名稱和子網位址空間。 應用程式閘道實例的名稱將會是*myApplicationGateway*，而我們所使用的子網位址空間是 10.2.0.0/16。 請確定您已在本教學課程的開頭新增或更新 aks-preview 延伸模組。 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

若要設定命令的其他參數 `az aks create` ，請參閱[這些參考](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create)。 

> [!NOTE]
> 您所建立的 AKS 叢集將會出現在您所建立的資源群組中， *myResourceGroup*。 不過，自動建立的應用程式閘道實例將會在代理程式組件區所在的節點資源群組中。 節點資源群組依預設會命名為*MC_resource 群組 name_cluster name_location* ，但可加以修改。 

## <a name="deploy-a-sample-application-by-using-agic"></a>使用 AGIC 部署範例應用程式

您現在會將範例應用程式部署至您所建立的 AKS 叢集。 應用程式會使用 AGIC 附加元件進行輸入，並將應用程式閘道實例連線到 AKS 叢集。 

首先，執行下列命令，以取得 AKS 叢集的認證 `az aks get-credentials` ： 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

現在您已有認證，請執行下列命令來設定使用 AGIC 來輸入叢集的範例應用程式。 AGIC 會將您先前設定的應用程式閘道實例，更新為您所部署的新範例應用程式的對應路由規則。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>檢查應用程式是否可連線

既然應用程式閘道實例已設定為可提供 AKS 叢集的流量，讓我們來驗證您的應用程式是否可連線。 首先，取得輸入的 IP 位址： 

```azurecli-interactive
kubectl get ingress
```

請檢查您所建立的範例應用程式是否正在執行：

- 造訪執行上述命令所獲得應用程式閘道實例的 IP 位址。
- 使用 `curl` 。 

應用程式閘道可能需要一分鐘的時間來取得更新。 如果應用程式閘道在入口網站上仍處於 [**正在更新**] 狀態，請先讓它完成，然後再嘗試連線到 IP 位址。 

## <a name="clean-up-resources"></a>清除資源

當您不再需要它們時，請移除資源群組、應用程式閘道實例和所有相關資源：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟
* [瞭解如何停用 AGIC 附加元件](./ingress-controller-disable-addon.md)
* [瞭解 AGIC 支援哪些注釋](./ingress-controller-annotations.md)
* [針對 AGIC 的問題進行疑難排解](./ingress-controller-troubleshoot.md)

