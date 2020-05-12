---
title: Azure Kubernetes Service （AKS）具有執行時間 SLA 的高可用性
description: 瞭解適用于 Azure Kubernetes Service （AKS） API 伺服器的選擇性高可用性執行時間 SLA 供應專案。
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125718"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes Service （AKS）執行時間 SLA

執行時間 SLA 是選擇性的功能，可為叢集啟用以財務支援的高 SLA。 執行時間 SLA 保證針對使用[可用性區域][availability-zones]之叢集的 Kubernetes API 伺服器端點，以及不使用可用性區域之叢集的可用性為99.9% 的叢集，可提供99.95% 的可用性。 AKS 會在更新和容錯網域之間使用主要節點複本，以確保符合 SLA 需求。

基於合規性理由或將 SLA 延伸至客戶的客戶，應開啟這項功能。 具有重要工作負載且需要更高可用性的客戶，並提供 SLA 優點，無法啟用這項功能。 啟用可用性區域的功能，以取得 Kubernetes API 伺服器的更高可用性。  

客戶可以使用99.5% 的服務等級目標（SLO），建立無限制的免費叢集。

> [!Important]
> 針對具有輸出鎖定的叢集，請參閱[限制輸出流量](limit-egress-traffic.md)以開啟適當的埠以達執行時間 SLA。

## <a name="sla-terms-and-conditions"></a>SLA 條款及條件

執行時間 SLA 是付費功能，而且每個叢集都會啟用。 執行時間 SLA 的定價取決於叢集數目，而不是叢集的大小。 如需詳細資訊，您可以參閱[執行時間 SLA 定價詳細資料](https://azure.microsoft.com/pricing/details/kubernetes-service/)。

## <a name="region-availability"></a>區域可用性

執行時間 SLA 會在下欄區域內上市：

* 澳大利亞東部
* 加拿大中部
* 美國東部
* 美國東部 2
* 美國中南部
* 東南亞
* 美國西部 2

## <a name="before-you-begin"></a>開始之前

* Azure CLI 版2.7.0 或更新版本

## <a name="creating-a-cluster-with-uptime-sla"></a>建立具有執行時間 SLA 的叢集

若要建立具有執行時間 SLA 的新叢集，請使用 Azure CLI。

下列範例會在 eastus  位置建立名為 myResourceGroup  的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。 下列範例會建立名為 myAKSCluster  並包含一個節點的叢集。 使用 *--enable-addons monitoring* 參數也可啟用適用於容器的 Azure 監視器。  此作業需要幾分鐘才能完成。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。 下列 JSON 程式碼片段顯示 SKU 的付費層，這表示您的叢集已啟用執行時間 SLA。

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>限制

* 您目前無法將執行時間 SLA 新增至現有的叢集。
* 目前沒有任何方法可從 AKS 叢集移除執行時間 SLA。  

## <a name="next-steps"></a>後續步驟

使用[可用性區域][availability-zones]來增加 AKS 叢集工作負載的高可用性。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
