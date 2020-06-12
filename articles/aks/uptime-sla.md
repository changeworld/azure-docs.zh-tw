---
title: Azure Kubernetes Service (AKS) 與運作時間 SLA
description: 了解 Azure Kubernetes Service (AKS) API 伺服器的選用運作時間 SLA 供應項目。
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e0e1399f69640dddfd618ac99637023390f28a92
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683214"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes Service (AKS) 運作時間 SLA

運作時間 SLA 是一項選用功能，可為叢集啟用受財務支援的高層級 SLA。 運作時間 SLA 保證使用[可用性區域][availability-zones]的叢集能保有 Kubernetes API 伺服器端點 99.95% 的可用性，而未使用可用性區域的叢集則保有 99.9% 的可用性。 AKS 會在更新和容錯網域之間使用主要節點複本，以確保符合 SLA 需求。

需藉由 SLA 達到合規性需求，或需要將 SLA 延伸至其使用者的客戶，應啟用這項功能。 客戶若有重要工作負載可因較高層級的運作時間 SLA 獲益，也可能受益於這項功能。 將運作時間 SLA 功能與可用性區域搭配使用，可讓 Kubernetes API 伺服器的運作時間具有更高的可用性。  

客戶仍可建立具有 99.5% 服務等級目標 (SLO) 的無限制免費叢集，並視需要選擇慣用的 SLO 或 SLA 運作時間。

> [!Important]
> 針對具有輸出鎖定的叢集，請參閱[限制輸出流量](limit-egress-traffic.md)以開啟適當的連接埠。

## <a name="sla-terms-and-conditions"></a>SLA 條款及條件

運作時間 SLA 是付費功能，可對個別叢集啟用。 運作時間 SLA 定價取決於離散叢集的數目，而不是個別叢集的大小。 如需詳細資訊，請檢視[運作時間 SLA 定價詳細資料](https://azure.microsoft.com/pricing/details/kubernetes-service/)。

## <a name="region-availability"></a>區域可用性

運作時間 SLA 適用於下列區域：

* 澳大利亞東部
* 加拿大中部
* 美國東部
* 美國東部 2
* 美國中南部
* 東南亞
* 美國西部 2

## <a name="before-you-begin"></a>開始之前

* Azure CLI 2.7.0 版或更新版本

## <a name="creating-a-cluster-with-uptime-sla"></a>透過運作時間 SLA 建立叢集

若要透過運作時間 SLA 建立新叢集，請使用 Azure CLI。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。 下列範例會建立名為 myAKSCluster 並包含一個節點的叢集。 使用 *--enable-addons monitoring* 參數也可啟用適用於容器的 Azure 監視器。  此作業需要幾分鐘才能完成。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。 下列 JSON 程式碼片段顯示 SKU 的付費層，這表示您的叢集已啟用運作時間 SLA。

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>限制

* 目前無法將現有的叢集轉換為啟用運作時間 SLA。
* 目前，若在建立 AKS 叢集時啟用了運作時間 SLA，即無法從叢集中移除 SLA。  
* 目前不支援私人叢集。

## <a name="next-steps"></a>後續步驟

使用[可用性區域][availability-zones]提高 AKS 叢集工作負載的高可用性。
將您的叢集設定為[限制輸出流量](limit-egress-traffic.md)。

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
