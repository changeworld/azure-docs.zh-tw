---
title: 使用現有的 Azure 應用程式閘道為現有的 AKS 叢集啟用輸入控制器附加元件
description: 使用此教學課程，針對現有的 AKS 叢集，使用現有的應用程式閘道啟用輸入控制器附加元件
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 6cbfac4794a685e5858e689c20d6603807edcedf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987979"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>教學課程：使用現有的應用程式閘道透過 Azure CLI （預覽）啟用現有 AKS 叢集的應用程式閘道輸入控制器附加元件

您可以使用 Azure CLI 來啟用[Azure Kubernetes Services （AKS）](https://azure.microsoft.com/services/kubernetes-service/)叢集的[應用程式閘道輸入控制器（AGIC）](ingress-controller-overview.md)附加元件（目前為預覽狀態）。 在本教學課程中，您將瞭解如何使用 AGIC 附加元件，透過部署在不同虛擬網路中的現有應用程式閘道，在現有的 AKS 叢集中公開您的 Kubernetes 應用程式。 首先，您會在一個虛擬網路中建立 AKS 叢集，並在不同的虛擬網路中建立一個應用程式閘道來模擬現有的資源。 接著，您將會啟用 AGIC 附加元件，同時對兩個虛擬網路進行對等互連，並部署範例應用程式，透過應用程式閘道使用 AGIC 附加元件來公開。 如果您要在相同的虛擬網路中為現有應用程式閘道和現有的 AKS 叢集啟用 AGIC 附加元件，則可以略過下面的對等互連步驟。 附加元件可讓您更快速地部署 AKS 叢集的 AGIC，而非[先前透過 Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) ，同時也提供完全受控的體驗。  

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立資源群組 
> * 建立新的 AKS 叢集 
> * 建立新的應用程式閘道 
> * 使用現有的應用程式閘道在現有的 AKS 叢集中啟用 AGIC 附加元件 
> * 將應用程式閘道虛擬網路與 AKS 叢集虛擬網路對等互連
> * 使用 AGIC 針對 AKS 叢集上的輸入部署範例應用程式
> * 檢查是否可透過應用程式閘道來連線到應用程式

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程需要您執行 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

使用[az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register)命令來註冊*AKS-IngressApplicationGatewayAddon*功能旗標，如下列範例所示：當附加元件仍處於預覽狀態時，您只需要針對每個訂用帳戶執行此動作一次：
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) 命令檢查註冊狀態：
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

準備就緒時，使用 [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) 命令重新整理 Microsoft.ContainerService 資源提供者的註冊：
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

請務必安裝/更新本教學課程的 aks-preview 延伸模組;使用下列 Azure CLI 命令
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。 下列範例會在*canadacentral*位置（區域）中建立名為*myResourceGroup*的資源群組。 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>部署新的 AKS 叢集

您現在會部署新的 AKS 叢集，以模擬現有的 AKS 叢集，而您想要為其啟用 AGIC 附加元件。  

在下列範例中，您將使用[AZURE CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking)和您建立的資源群組*MyResourceGroup*中的[受控](https://docs.microsoft.com/azure/aks/use-managed-identity)識別，部署名為*myCluster*的新 AKS 叢集。    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

若要設定命令的其他參數 `az aks create` ，請造訪[這裡](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create)的參考。 

## <a name="deploy-a-new-application-gateway"></a>部署新的應用程式閘道 

您現在將會部署新的應用程式閘道，以模擬現有的應用程式閘道，您想要使用它來對 AKS 叢集*myCluster*的流量進行負載平衡。 應用程式閘道的名稱將會是*myApplicationGateway*，但您必須先建立一個名為*MYPUBLICIP*的公用 IP 資源，以及一個名為*myVnet*的新虛擬網路（位址空間11.0.0.0 版/8），以及一個具有位址空間 11.1.0.0/16 的子網（稱為*MySubnet*），然後使用*mySubnet*在*myPublicIp*中部署您的應用程式閘道。 

當您使用 AKS 叢集，並在不同的虛擬網路中應用程式閘道時，兩個虛擬網路的位址空間不能重迭。 AKS 叢集所部署的預設位址空間是 10.0.0.0/8，因此我們將應用程式閘道虛擬網路位址首碼設定為11.0.0.0 版/8。 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> 應用程式閘道輸入控制器（AGIC）附加元件**僅**支援應用程式閘道 v2 Sku （標準和 WAF），而**非**應用程式閘道 v1 sku。 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>在現有的 AKS 叢集中，使用現有的應用程式閘道啟用 AGIC 附加元件 

現在，您會在您建立的 AKS 叢集中啟用 AGIC 附加元件*myCluster*，並指定 AGIC 附加元件，以使用您建立的現有應用程式閘道， *myApplicationGateway*。 請確定您已在本教學課程的開頭新增/更新 aks-preview 延伸模組。 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>將兩個虛擬網路對等互連

由於我們已將 AKS 叢集部署在自己的虛擬網路中，而應用程式閘道在另一個虛擬網路中，因此您必須將兩個虛擬網路對等互連，才能讓流量從應用程式閘道流到叢集中的 pod。 對等互連兩個虛擬網路需要在兩個不同的時間執行 Azure CLI 命令，以確保連接是雙向的。 第一個命令會建立從應用程式閘道虛擬網路到 AKS 虛擬網路的對等互連連線;第二個命令將會建立另一個方向的對等互連連接。 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>使用 AGIC 部署範例應用程式 

您現在會將範例應用程式部署至您所建立的 AKS 叢集，該叢集將會使用輸入的 AGIC 附加元件，並將應用程式閘道連接到 AKS 叢集。 首先，您會透過執行命令，取得您所部署之 AKS 叢集的認證 `az aks get-credentials` 。 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

一旦您擁有所建立叢集的認證，請執行下列命令來設定使用 AGIC 來輸入叢集的範例應用程式。 AGIC 會將您稍早設定的應用程式閘道，更新為您所部署的新範例應用程式的對應路由規則。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>檢查應用程式是否可連線

既然應用程式閘道已設定為提供 AKS 叢集的流量，讓我們來驗證您的應用程式是否可連線。 您會先取得輸入的 IP 位址。 

```azurecli-interactive
kubectl get ingress
```

請造訪執行上述命令所得到的應用程式閘道的 IP 位址或檢查，以檢查您所建立的範例應用程式是否已啟動且正在執行 `curl` 。 取得更新可能需要應用程式閘道一分鐘的時間，因此，如果應用程式閘道在入口網站上仍處於「更新中」狀態，請先讓它完成，然後再嘗試到達 IP 位址。 

## <a name="clean-up-resources"></a>清除資源

若不再需要，可移除資源群組、應用程式閘道和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>後續步驟
* [深入瞭解如何停用 AGIC 附加元件](./ingress-controller-disable-addon.md)
* [深入瞭解 AGIC 支援哪些注釋](./ingress-controller-annotations.md)
* [針對 AGIC 的問題進行疑難排解](./ingress-controller-troubleshoot.md)

