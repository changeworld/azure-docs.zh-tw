---
title: 在 Azure 庫伯奈斯服務 (AKS) 中使用系統節點池
description: 瞭解如何在 Azure 庫伯奈斯服務 (AKS) 中建立和管理系統節點池
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: b567d9e618877463e1e659f368d35fbb787a4ef2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259063"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>在 Azure 庫伯奈斯服務 (AKS) 中管理系統節點池

在 Azure 庫伯奈斯服務 (AKS) 中,相同配置的節點分組到*節點池*中。 節點池包含運行應用程式的基礎 VM。 系統節點池和用戶節點池是 AKS 群集的兩種不同的節點池模式。 系統節點池主要用於託管關鍵系統 pod(如 CoreDNS 和隧道前)。 用戶節點池的主要目的是託管應用程式窗格。 但是,如果您希望在 AKS 群集中只具有一個池,則可以在系統節點池上安排應用程式窗格。 每個 AKS 群集必須至少包含一個至少具有一個節點的系統節點池。 

> [!Important]
> 如果在生產環境中為 AKS 群集運行單個系統節點池,我們建議您為節點池至少使用三個節點。

## <a name="before-you-begin"></a>開始之前

* 您需要 Azure CLI 版本 2.3.1 或更高版本安裝和配置。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

創建和管理支援系統節點池的 AKS 群集時,以下限制適用。

* 請參考[Azure 庫伯奈斯服務 (AKS) 中的配額、虛擬機器大小限制和地區可用性][quotas-skus-regions]。
* 必須使用虛擬機縮放集作為 VM 類型構建 AKS 群集。
* 節點池的名稱可能僅包含小寫字母數位字元,並且必須以小寫字母開頭。 對於 Linux 節點池,長度必須介於 1 到 12 個字元之間。 對於 Windows 節點池,長度必須介於 1 到 6 個字元之間。

## <a name="system-and-user-node-pools"></a>系統和使用者節點池

系統節點池節點各有**標籤kubernetes.azure.com/mode:系統**。 每個 AKS 群集至少包含一個系統節點池。 系統節點池具有以下限制:

* 系統池 osType 必須是 Linux。
* 用戶節點池 osType 可能是 Linux 或 Windows。
* 系統池必須至少包含一個節點,並且用戶節點池可能包含零個或多個節點。
* 系統節點池需要至少 2 個 vCPU 和 4GB 記憶體的 VM SKU。
* 系統節點池必須支援至少 30 個[pod,如窗格的最小值和最大值公式][maximum-pods]所述。
* Spot 節點池需要用戶節點池。

您可以對節點池執行以下操作:

* 將系統節點池更改為使用者節點池,前提是您有另一個系統節點池要將其置於 AKS 群集中的位置。
* 將用戶節點池更改為系統節點池。
* 刪除使用者節點池。
* 您可以刪除系統節點池,前提是您有另一個系統節點池要將其置於 AKS 群集中的位置。
* AKS 群集可能具有多個系統節點池,並且至少需要一個系統節點池。

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>使用系統節點池建立新的 AKS 叢集

創建新 AKS 群集時,將自動建立具有單個節點的系統節點池。 初始節點池預設為類型系統模式。 當您使用 az aks 節點池添加創建新節點池時,這些節點池是用戶節點池,除非您顯式指定模式參數。

下面的範例在*東部*區域創建名為*myResourceGroup*的資源組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。 下面的示例創建一個名為*myAKSCluster*的群集,其中一個系統池包含一個節點。 對於生產工作負載,請確保使用至少包含三個節點的系統節點池。 此作業可能需要幾分鐘的時間才能完成。

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>將系統節點池新增到現有 AKS 群集

您可以將一個或多個系統節點池添加到現有 AKS 群集。 以下命令添加預設計數為三個節點的模式類型系統的節點池。

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>顯示節點池的詳細資訊

您可以使用以下命令檢查節點池的詳細資訊。  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

為**系統**節點池定義了系統類型模式,並為使用者節點池定義了「**使用者」** 類型模式。

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>更新系統和使用者節點池

您可以更改系統和用戶節點池的模式。 僅當 AKS 群集上已存在另一個系統節點池時,才能將系統節點池更改為使用者池。

此命令將系統節點池更改為用戶節點池。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

此命令將用戶節點池更改為系統節點池。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>移除系統節點池

> [!Note]
> 要在 API 版本 2020-03-02 之前在 AKS 叢集上使用系統節點池,請添加新的系統節點池,然後刪除原始默認節點池。

以前無法刪除系統節點池,這是 AKS 群集中的初始預設節點池。 現在,您可以靈活地從群集中刪除任何節點池。 由於 AKS 群集至少需要一個系統節點池,因此必須在 AKS 群集上至少有兩個系統節點池,然後才能刪除其中一個節點池。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>後續步驟

在本文中,您學習了如何在 AKS 群集中創建和管理系統節點池。 有關如何使用多個節點池的詳細資訊,請參閱[使用多個節點池][use-multiple-node-pools]。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30