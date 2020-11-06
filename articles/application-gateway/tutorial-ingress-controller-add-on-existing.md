---
title: 教學課程：使用現有的 Azure 應用程式閘道執行個體來啟用現有 AKS 叢集的輸入控制器附加元件
description: 使用此教學課程，針對現有的 AKS 叢集，使用現有的應用程式閘道啟用輸入控制器附加元件
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: 10f78167b9c3f557fa16061cfac8aad080519415
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397122"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>教學課程：使用現有的應用程式閘道，透過 Azure CLI (預覽) 啟用現有 AKS 叢集的應用程式閘道輸入控制器附加元件

您可以使用 Azure CLI 來啟用 [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 叢集的 [應用程式閘道輸入控制器 (AGIC)](ingress-controller-overview.md) 附加元件 (目前為預覽狀態)。 在本教學課程中，您將了解如何使用 AGIC 附加元件，透過部署在不同虛擬網路中的現有應用程式閘道，在現有的 AKS 叢集中公開您的 Kubernetes 應用程式。 首先，您會在一個虛擬網路中建立 AKS 叢集，並在另一個的虛擬網路中建立應用程式閘道來模擬現有的資源。 接著您會啟用 AGIC 附加元件，同時對兩個虛擬網路進行對等互連，並部署範例應用程式，透過應用程式閘道使用 AGIC 附加元件來公開。 如果要在相同的虛擬網路中為現有應用程式閘道和現有的 AKS 叢集啟用 AGIC 附加元件，則可以略過下面的對等互連步驟。 相較於[之前透過 Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)，附加元件可讓您更快速地部署 AKS 叢集的 AGIC，同時也提供完全受控的經驗。  

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立資源群組 
> * 建立新的 AKS 叢集 
> * 建立新的應用程式閘道 
> * 使用現有的應用程式閘道在現有的 AKS 叢集中啟用 AGIC 附加元件 
> * 將應用程式閘道虛擬網路與 AKS 叢集虛擬網路對等互連
> * 使用 AGIC 在 AKS 叢集上輸入，以部署範例應用程式
> * 檢查是否可透過應用程式閘道連線到應用程式

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程需要您執行 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

使用 [az feature register](/cli/azure/feature#az-feature-register) 命令註冊 AKS-IngressApplicationGatewayAddon 功能旗標，如下列範例所示；當附加元件仍處於預覽狀態時，您只需要針對每個訂用帳戶執行此動作一次：
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

狀態需要幾分鐘才會顯示「已註冊」。 您可以使用 [az feature list](/cli/azure/feature#az-feature-register) 命令檢查註冊狀態：
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

準備就緒時，使用 [az provider register](/cli/azure/provider#az-provider-register) 命令重新整理 Microsoft.ContainerService 資源提供者的註冊：
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

請務必安裝/更新本教學課程的 aks-preview 擴充功能；使用下列 Azure CLI 命令
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。 下列範例會在加大中部位置 (區域) 中建立名為 myResourceGroup 的資源群組。 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>部署新的 AKS 叢集

您現在會部署新的 AKS 叢集，以模擬您想要為其啟用 AGIC 附加元件的現有的 AKS 叢集。  

在下列範例中，您將會使用所建立之資源群組 myResourceGroup 中的 [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) 和[受控識別](../aks/use-managed-identity.md)來部署名為 myCluster 的新 AKS 叢集。    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

若要設定 `az aks create` 命令的其他參數，請造訪[這些參考](/cli/azure/aks?view=azure-cli-latest#az-aks-create)。 

## <a name="deploy-a-new-application-gateway"></a>部署新的應用程式閘道 

您現在會部署新的應用程式閘道以模擬現有的應用程式閘道，而您要使用此應用程式閘道對 AKS 叢集 myCluster 的流量進行負載平衡。 應用程式閘道的名稱會是 myApplicationGateway，但您必須先建立一個名為 myPublicIp 的公用 IP 資源，以及一個位址空間為 11.0.0.0/8 且名為 myVnet 的新虛擬網路，還有一個位址空間為 11.1.0.0/16 且名為 mySubnet 的子網路，並在 mySubnet 中使用 myPublicIp 部署您的應用程式閘道。 

在不同的虛擬網路中使用 AKS 叢集和應用程式閘道時，這兩個虛擬網路的位址空間不能重疊。 AKS 叢集所部署的預設位址空間是 10.0.0.0/8，因此我們將應用程式閘道虛擬網路位址的首碼設為 11.0.0.0/8。 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> 應用程式閘道輸入控制器附加元件 (AGIC)「僅支援」應用程式閘道 v2 SKU (標準和 WAF)，而「不支援」應用程式閘道 v1 SKU。 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>使用現有的應用程式閘道在現有的 AKS 叢集中啟用 AGIC 附加元件 

現在，您會在您建立的 AKS 叢集 myCluster 中啟用 AGIC 附加元件，並指定 AGIC 附加元件以使用您建立的現有應用程式閘道 myApplicationGateway。 請確定您已在本教學課程的開頭新增/更新 aks-preview 擴充功能。 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>讓兩個虛擬網路一起對等互連

由於我們已將 AKS 叢集部署在自己的虛擬網路，而將應用程式閘道在另一個虛擬網路，因此您必須將兩個虛擬網路對等互連，才能讓流量從應用程式閘道流到叢集中的 Pod。 將兩個虛擬網路對等互連需要在不同的時間執行 Azure CLI 命令，以確保連線是雙向的。 第一個命令會建立從應用程式閘道虛擬網路到 AKS 虛擬網路的對等互連連線；第二個命令會建立另一個方向的對等互連連線。 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>使用 AGIC 部署範例應用程式 

您現在會將範例應用程式部署至您所建立的 AKS 叢集，該叢集會使用 AGIC 附加元件輸入，並將應用程式閘道連線到 AKS 叢集。 首先，您可以藉由執行 `az aks get-credentials` 命令，取得部署之 AKS 叢集的認證。 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

取得所建立之叢集的認證後，請執行下列命令來設定使用 AGIC 來輸入叢集的範例應用程式。 AGIC 會將您先前設定的應用程式閘道，更新為您所部署之新範例應用程式的對應路由規則。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>檢查是否可連線到應用程式

既然應用程式閘道已設定為可提供 AKS 叢集的流量，讓我們來驗證您的應用程式是否可連線。 首先取得輸入的 IP 位址。 

```azurecli-interactive
kubectl get ingress
```

請造訪執行上述命令所得到之應用程式閘道的 IP 位址，或使用 `curl` 檢查，以檢查您所建立的範例應用程式是否已啟動且正在執行。 應用程式閘道可能需要一分鐘才能取得更新；因此如果應用程式閘道在入口網站上仍處於「更新中」狀態，請先讓其完成，然後再嘗試連線到 IP 位址。 

## <a name="clean-up-resources"></a>清除資源

若不再需要，可移除資源群組、應用程式閘道和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解如何停用 AGIC 附加元件](./ingress-controller-disable-addon.md)