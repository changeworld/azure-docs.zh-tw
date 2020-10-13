---
title: 使用 Azure Kubernetes Service (AKS) 中的系統節點集區
description: '瞭解如何在 Azure Kubernetes Service (AKS 中建立及管理系統節點集區) '
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit
ms.openlocfilehash: 2cb6ed265d3e94c2c162381dfb80ba0c5427a71f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888955"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>管理 Azure Kubernetes Service (AKS) 中的系統節點集區

在 Azure Kubernetes Service (AKS) 中，相同設定的節點會一起分組到 *節點*集區中。 節點集區包含執行應用程式的基礎 Vm。 系統節點集區和使用者節點集區是 AKS 叢集的兩個不同節點集區模式。 系統節點集區提供裝載重要系統 pod （例如和）的主要用途 `CoreDNS` `metrics-server` 。 使用者節點集區提供裝載應用程式 pod 的主要用途。 但是，如果您想要在 AKS 叢集中只有一個集區，則可以在系統節點集區上排程應用程式 pod。 每個 AKS 叢集至少必須包含一個具有至少一個節點的系統節點集區。

> [!Important]
> 如果您在生產環境中為您的 AKS 叢集執行單一系統節點集區，建議您針對節點集區使用至少三個節點。

## <a name="before-you-begin"></a>開始之前

* 您需要安裝並設定 Azure CLI 2.3.1 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

當您建立和管理支援系統節點集區的 AKS 叢集時，適用下列限制。

* [如 Azure Kubernetes Service (AKS) ，請參閱配額、虛擬機器大小限制和區域可用性][quotas-skus-regions]。
* AKS 叢集必須以虛擬機器擴展集作為 VM 類型和 *標準* SKU 負載平衡器來建立。
* 節點集區的名稱只能包含小寫英數位元，且開頭必須是小寫字母。 Linux 節點集區的長度必須介於1到12個字元之間。 Windows 節點集區的長度必須介於1到6個字元之間。
* 您必須使用2020-03-01 或更高的 API 版本來設定節點集區模式。 在2020-03-01 之前的 API 版本上建立的叢集只會包含使用者節點集區，但可透過下列 [更新集區模式步驟](#update-existing-cluster-system-and-user-node-pools)來遷移，以包含系統節點集區。
* 節點集區的模式是必要的屬性，而且必須在使用 ARM 範本或直接 API 呼叫時明確設定。

## <a name="system-and-user-node-pools"></a>系統和使用者節點集區

針對系統節點集區，AKS 會自動將標籤 **kubernetes.azure.com/mode：系統** 指派給其節點。 這會導致 AKS 偏好在包含此標籤的節點集區上排程系統 pod。 此標籤不會讓您無法排程系統節點集區上的應用程式 pod。 不過，我們建議您從您的應用程式 pod 隔離重要的系統 pod，以防止設定錯誤或 rogue 應用程式 pod 不小心終止系統 pod。 您可以藉由建立專用的系統節點集區來強制執行此行為。 使用 `CriticalAddonsOnly=true:NoSchedule` 污點防止應用程式 pod 在系統節點集區上進行排程。

系統節點集區具有下列限制：

* 系統集區 osType 必須是 Linux。
* 使用者節點集區 osType 可能是 Linux 或 Windows。
* 系統集區必須包含至少一個節點，而使用者節點集區可包含零或多個節點。
* 系統節點集區需要至少2個個 vcpu 和4GB 記憶體的 VM SKU。
* 系統節點集區必須支援至少30個 pod，如 pod 的 [最小值和最大值公式][maximum-pods]所述。
* 點節點集區需要使用者節點集區。
* 新增額外的系統節點集區，或變更哪個節點集區是系統節點集區，將 *不* 會自動移動系統 pod。 系統 pod 可以繼續在相同的節點集區上執行，即使您將它變更為使用者節點集區也一樣。 如果您刪除或縮小執行先前為系統節點集區之系統 pod 的節點集區，則會重新部署這些系統 pod，並將慣用排程新增至新的系統節點集區。

您可以使用節點集區執行下列作業：

* 建立專用的系統節點集區 (偏好將系統 pod 排程到) 的節點集區 `mode:system`
* 將系統節點集區變更為使用者節點集區，前提是您有另一個系統節點集區在 AKS 叢集中進行放置。
* 將使用者節點集區變更為系統節點集區。
* 刪除使用者節點集區。
* 您可以刪除系統節點集區，前提是您有另一個系統節點集區可在 AKS 叢集中進行。
* AKS 叢集可能會有多個系統節點集區，而且至少需要一個系統節點集區。
* 如果您想要變更現有節點集區上的各種固定設定，可以建立新的節點集區來取代它們。 其中一個範例是使用新的 maxPods 設定來新增節點集區，並刪除舊的節點集區。

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>使用系統節點集區建立新的 AKS 叢集

當您建立新的 AKS 叢集時，會自動建立具有單一節點的系統節點集區。 初始節點集區預設為系統類型的模式。 當您使用建立新的節點集區時 `az aks nodepool add` ，除非您明確指定模式參數，否則這些節點集區會是使用者節點集區。

下列範例會在 eastus 地區建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。 下列範例會建立名為 *myAKSCluster* 的叢集，其中包含一個包含一個節點的專用系統集區。 針對您的生產工作負載，請確定您使用的系統節點集區至少有三個節點。 此作業可能需要幾分鐘的時間才能完成。

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>將專用系統節點集區新增至現有的 AKS 叢集

> [!Important]
> 建立節點集區之後，您就無法透過 CLI 變更節點污點。

您可以將一或多個系統節點集區新增至現有的 AKS 叢集。 建議您將應用程式 pod 排程在使用者節點集區上，並將系統節點集區專門用於重要的系統 pod。 這可防止惡意應用程式 pod 不慎終止系統 pod。 使用您系統節點集區的 `CriticalAddonsOnly=true:NoSchedule` [污點][aks-taints] 來強制執行此行為。 

下列命令會以三個節點的預設計數，加入模式類型系統的專用節點集區。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>顯示節點集區的詳細資料

您可以使用下列命令來檢查節點集區的詳細資料。  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

系統節點集區定義了 **系統** 節點集區的模式，以及為使用者節點集區定義 **使用者** 類型的模式。 針對系統集區，確認污點設定為，以 `CriticalAddonsOnly=true:NoSchedule` 防止應用程式 pod 在此節點集區上排程。

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>更新現有的叢集系統和使用者節點集區

> [!NOTE]
> 您必須使用2020-03-01 或更高的 API 版本來設定系統節點集區模式。 在2020-03-01 之前的 API 版本上建立的叢集只會包含使用者節點集區。 若要在舊叢集上接收系統節點集區功能和優點，請在最新的 Azure CLI 版本上，使用下列命令來更新現有節點集區的模式。

您可以變更系統和使用者節點集區的模式。 只有當 AKS 叢集中已有其他系統節點集區時，您才能將系統節點集區變更為使用者集區。

此命令會將系統節點集區變更為使用者節點集區。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

此命令會將使用者節點集區變更為系統節點集區。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>刪除系統節點集區

> [!Note]
> 若要在 API 2020-03-02 版之前使用 AKS 叢集上的系統節點集區，請新增系統節點集區，然後刪除原始的預設節點集區。

您必須在 AKS 叢集上至少有兩個系統節點集區，才能刪除其中一個。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>清除資源

若要刪除叢集，請使用 [az group delete][az-group-delete] 命令來刪除 AKS 資源群組：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何建立和管理 AKS 叢集中的系統節點集區。 如需有關如何使用多個節點集區的詳細資訊，請參閱 [使用多個節點][use-multiple-node-pools]集區。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
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
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
