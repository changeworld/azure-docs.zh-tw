---
title: 教學課程：使用新的 Azure 應用程式閘道執行個體來啟用新 AKS 叢集的輸入控制器附加元件
description: 使用此教學課程來了解如何使用 Azure CLI，以新的應用程式閘道執行個體，為新的 AKS 叢集啟用輸入控制器附加元件。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: 3cae4591a5da53683c965d7c6ba3ec169249c87e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566124"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>教學課程：使用新的應用程式閘道執行個體來啟用新 AKS 叢集的輸入控制器附加元件 (預覽)

您可以使用 Azure CLI 來啟用 [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 叢集的 [應用程式閘道輸入控制器 (AGIC)](ingress-controller-overview.md) 附加元件。 此附加元件目前為預覽狀態。

在本教學課程中，您將建立已啟用 AGIC 附加元件的 AKS 叢集。 建立叢集將會自動建立要使用的 Azure 應用程式閘道執行個體。 接著，您將會部署範例應用程式，以使用附加元件透過應用程式閘道公開應用程式。 

相較於[之前透過 Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)，附加元件可讓您更快速地部署 AKS 叢集的 AGIC， 同時也提供完全受控的體驗。    

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立資源群組。 
> * 建立啟用 AGIC 附加元件的新 AKS 叢集。 
> * 使用 AGIC 在 AKS 叢集上輸入，以部署範例應用程式。
> * 檢查是否可透過應用程式閘道連線到應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 本教學課程需要 2.0.4 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

 - 若要註冊 AKS-IngressApplicationGatewayAddon 功能旗標，請使用 [az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令，如下列範例所示。 當附加元件仍處於預覽狀態時，您只需要針對每個訂用帳戶執行此動作一次。
    ```azurecli-interactive
    az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
    ```

   可能需要數分鐘的時間狀態才會顯示為 `Registered`。 您可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令檢查註冊狀態：
    ```azurecli-interactive
    az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
    ```

 - 準備就緒時，使用 [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) 命令重新整理 Microsoft.ContainerService 資源提供者的註冊：
    ```azurecli-interactive
    az provider register --namespace Microsoft.ContainerService
    ```

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。 下列範例會在加大中部位置 (區域) 中建立名為 myResourceGroup 的資源群組： 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>部署已啟用附加元件的 AKS 叢集

您現在可以部署啟用 AGIC 附加元件的新 AKS 叢集。 如果您未提供要在此程序中使用的現有應用程式閘道執行個體，我們會自動建立並設定新的應用程式閘道執行個體，以提供 AKS 叢集的流量。  

> [!NOTE]
> 應用程式閘道輸入控制器附加元件「僅支援」應用程式閘道 v2 SKU (標準和 WAF)，而「不支援」應用程式閘道 v1 SKU。 當您透過 AGIC 附加元件部署新的應用程式閘道執行個體時，只能部署應用程式閘道 Standard_v2 SKU。 如果要啟用應用程式閘道 WAF_v2 SKU 的附加元件，請使用下列其中一種方法：
>
> - 透過入口網站在應用程式閘道中啟用 WAF。 
> - 先建立 WAF_v2 應用程式閘道執行個體，然後遵循如何[使用現有的 AKS 叢集和現有的應用程式閘道執行個體啟用 AGIC 附加元件](tutorial-ingress-controller-add-on-existing.md)的指示。 

在下列範例中，您將使用 [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) 和 [受控識別](../aks/use-managed-identity.md)，部署名為 *myCluster* 的新 AKS 叢集。 AGIC 附加元件將會在您建立的資源群組 myResourceGroup 中啟用。 

在未指定現有應用程式閘道執行個體的情況下，若要部署已啟用 AGIC 附加元件的新 AKS 叢集，就表示系統會自動建立 Standard_v2 SKU 應用程式閘道執行個體。 因此，您也會指定應用程式閘道執行個體的名稱和子網路位址空間。 應用程式閘道執行個體的名稱會是 myApplicationGateway，而我們所使用的子網路位址空間則是 10.2.0.0/16。 請確定您已在本教學課程的開頭新增或更新 aks-preview 擴充功能。 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" --generate-ssh-keys
```

若要設定 `az aks create` 命令的其他參數，請參閱[這些參考](/cli/azure/aks?view=azure-cli-latest#az-aks-create)。 

> [!NOTE]
> 您所建立的 AKS 叢集會出現在您所建立的 myResourceGroup 資源群組中。 不過，自動建立的應用程式閘道執行個體將會位於代理程式集區所在的節點資源群組中。 根據預設，節點資源群組會命名為 MC_resource-group-name_cluster-name_location，但可加以修改。 

## <a name="deploy-a-sample-application-by-using-agic"></a>使用 AGIC 部署範例應用程式

您現在會將範例應用程式部署至您建立的 AKS 叢集。 應用程式會使用 AGIC 附加元件進行輸入，並將應用程式閘道執行個體連線到 AKS 叢集。 

首先，藉由執行 `az aks get-credentials` 命令，取得 AKS 叢集的認證： 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

現在您已有認證，請執行下列命令來設定使用 AGIC 來輸入叢集的範例應用程式。 AGIC 會將您先前設定的應用程式閘道執行個體，更新為您所部署之新範例應用程式的對應路由規則。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>檢查是否可連線到應用程式

既然應用程式閘道執行個體已設定為可提供 AKS 叢集的流量，讓我們來驗證您的應用程式是否可連線。 首先取得輸入的 IP 位址： 

```azurecli-interactive
kubectl get ingress
```

請使用下列其中一種方法，檢查您所建立的範例應用程式是否正在執行：

- 造訪執行上述命令所獲得之應用程式閘道執行個體的 IP 位址。
- 使用 `curl`。 

應用程式閘道可能需要一分鐘來取得更新。 如果應用程式閘道在入口網站上仍為 **更新** 狀態，請先讓其完成，再嘗試連線 IP 位址。 

## <a name="clean-up-resources"></a>清除資源

若不再需要這些資源，可移除資源群組、應用程式閘道執行個體和所有相關資源：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何停用 AGIC 附加元件](./ingress-controller-disable-addon.md)