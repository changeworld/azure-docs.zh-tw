---
title: 遷移到 Azure 庫伯奈斯服務 （AKS）
description: 遷移到 Azure 庫伯奈斯服務 （AKS）。
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624809"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>遷移到 Azure 庫伯奈斯服務 （AKS）

本文可説明您規劃和執行成功遷移到 Azure 庫伯奈斯服務 （AKS）。 為了説明您做出關鍵決策，本指南提供了 AKS 當前推薦配置的詳細資訊。 本文並不涵蓋每個方案，在適當的情況下，本文包含指向更詳細資訊的連結，以便規劃成功的遷移。

本文檔可用於説明支援以下方案：

* 將由[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)支援的 AKS 群集遷移到[虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* 遷移 AKS 群集以使用[標準 SKU 負載等化器](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* 從[Azure 容器服務 （ACS） 遷移 - 2020 年 1 月 31 日停用](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)AKS
* 從[AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)遷移到 AKS
* 從非 Azure 庫伯奈斯群集遷移到 AKS

遷移時，請確保目標庫伯內斯版本位於 AKS 支援的視窗內。 如果使用舊版本，它可能不在支援的範圍內，並且需要 AKS 支援的升級版本。 有關詳細資訊，請參閱[AKS 支援的庫伯奈斯版本](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)。

如果要遷移到新版本的 Kubernetes，請查看[庫伯內斯版本和版本偏斜支援策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)。

根據您的方案，多個開源工具可以説明您進行遷移：

* [韋雷羅](https://velero.io/)（要求庫伯內斯 1.7°）
* [Azure 庫貝 CLI 擴展](https://github.com/yaron2/azure-kube-cli)
* [重新換檔器](https://github.com/mhausenblas/reshifter)

在本文中，我們將總結遷移詳細資訊：

> [!div class="checklist"]
> * 帶標準負載等化器和虛擬機器規模集的 AKS
> * 現有附加的 Azure 服務
> * 確保有效的配額
> * 高可用性和業務連續性
> * 無狀態應用程式的注意事項
> * 有狀態應用程式的注意事項
> * 部署群集配置

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>帶標準負載等化器和虛擬機器規模集的 AKS

AKS 是一種託管服務，提供獨特的功能，管理開銷更低。 作為託管服務，您必須從 AKS 支援的一組[區域](https://docs.microsoft.com/azure/aks/quotas-skus-regions)中選擇。 從現有群集到 AKS 的過渡可能需要修改現有應用程式，以便在 AKS 託管控制平面上保持正常運行。

我們建議使用由[虛擬機器縮放集](https://docs.microsoft.com/azure/virtual-machine-scale-sets)和[Azure 標準負載等化器](https://docs.microsoft.com/azure/aks/load-balancer-standard)支援的 AKS 群集，以確保在發佈時獲取[多個節點池](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)、[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)、[授權 IP 範圍](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges)、[群集自動縮放器](https://docs.microsoft.com/azure/aks/cluster-autoscaler)[、AKS 的 Azure 策略](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)等功能。

由[虛擬機器可用性集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)支援的 AKS 群集不支援其中許多功能。

下面的示例創建一個 AKS 群集，該群集由虛擬機器規模集支援單個節點池。 它使用標準負載等化器。 它還啟用叢集節點池上的群集自動縮放器，並至少設置*1*個節點，最多*設置 3 個*節點：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>現有附加的 Azure 服務

遷移群集時，您可能已連接外部 Azure 服務。 它們不需要資源娛樂，但它們需要更新從以前的群集到新群集的連接以維護功能。

* Azure Container Registry
* Log Analytics
* Application Insights
* 流量管理員
* 儲存體帳戶
* 外部資料庫

## <a name="ensure-valid-quotas"></a>確保有效的配額

由於在移轉期間會有額外的虛擬機器部署到訂用帳戶，因此您應確認您的配額與限制足以讓這些資源使用。 您可能需要請求增加[vCPU 配額](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。

您可能需要請求增加[網路配額](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests)，以確保不會耗盡 IP。 有關其他資訊，請參閱[AKS 的網路和 IP 範圍](https://docs.microsoft.com/azure/aks/configure-kubenet)。

有關詳細資訊，請參閱[Azure 訂閱和服務限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。 要檢查當前配額，請在 Azure 門戶中轉到[訂閱邊欄選項卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，選擇訂閱，然後選擇 **"使用方式 + 配額**"。

## <a name="high-availability-and-business-continuity"></a>高可用性和業務連續性

如果應用程式無法處理停機時間，則需要遵循高可用性遷移方案的最佳做法。  複雜業務連續性規劃、災害復原和最大化停機時間的最佳做法超出了本文檔的範圍。  詳細瞭解[Azure 庫伯奈斯服務 （AKS） 中業務連續性和災害復原的最佳做法，](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)瞭解更多資訊。

對於複雜的應用程式，您的遷移作業通常會進行一段時間，而非全部一次完成。 這意味著新舊環境可能需要通過網路進行通信。 以前使用`ClusterIP`服務進行通信的應用程式可能需要作為類型`LoadBalancer`公開並加以適當保護。

要完成遷移，您需要將用戶端指向在 AKS 上運行的新服務。 我們建議您通過更新 DNS 來重定向流量，以指向位於 AKS 群集前面的負載等化器。

[Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/)可以將客戶定向到所需的庫伯奈斯群集和應用程式實例。  流量管理器是基於 DNS 的流量負載等化器，可以跨區域分配網路流量。  為了獲得最佳性能和冗余，請在所有應用程式流量通過流量管理器在它進入 AKS 群集之前進行引導。  在多群集部署中，客戶應連接到指向每個 AKS 群集上的服務的流量管理器 DNS 名稱。 使用流量管理器終結點定義這些服務。 每個終結點都是*服務負載等化器 IP。* 使用此配置可以將網路流量從一個區域中的流量管理器終結點定向到其他區域中的終結點。

![帶流量管理器的 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure 前門服務](https://docs.microsoft.com/azure/frontdoor/front-door-overview)是 AKS 群集路由流量的另一個選項。  Azure Front Door Service 可讓您針對最佳效能和立即全域容錯移轉以獲得高可用性最佳化，定義、管理及監視網路流量的全域路由。 

### <a name="considerations-for-stateless-applications"></a>無狀態應用程式的注意事項

無狀態應用程式移轉是最直接的案例。 將資源定義（YAML 或 Helm）應用於新群集，確保一切按預期工作，並重定向流量以啟動新群集。

### <a name="considerations-for-stateful-applications"></a>有狀態應用程式的注意事項

仔細規劃有狀態應用程式的遷移，以避免資料丟失或意外停機。

如果使用 Azure 檔，則可以將檔共用作為卷裝載到新群集中：
* [將靜態 Azure 檔裝入卷](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

如果使用 Azure 託管磁片，則只有在未連接到任何 VM 時才能裝載磁片：
* [將靜態 Azure 磁片裝入卷](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

如果這兩種方法都不起作用，則可以使用備份和還原選項：
* [Azure 上的 Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure 檔案

不同於磁碟，Azure 檔案服務可同時掛接到多部主機。 在 AKS 群集中，Azure 和 Kubernets 不會阻止您創建 ACS 群集仍然使用的窗格。 為了防止資料丟失和意外行為，請確保群集不會同時寫入相同的檔。

如果應用程式可以承載指向同一檔共用的多個副本，請按照無狀態遷移步驟將 YAML 定義部署到新群集。 如果不是，則可能的移轉方法包含下列步驟：

* 驗證應用程式是否正常工作。
* 將即時流量指向新的 AKS 群集。
* 斷開舊群集。

如果要從空共用開始並複製來源資料，可以使用命令[`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest)遷移資料。


#### <a name="migrating-persistent-volumes"></a>遷移持久卷

如果要將現有持久卷遷移到 AKS，通常將按照以下步驟操作：

* 靜止寫入應用程式。 （此步驟是可選的，需要停機。
* 拍攝磁片的快照。
* 從快照創建新的託管磁片。
* 在 AKS 中創建持久卷。
* 更新窗格規範[以使用現有卷](https://docs.microsoft.com/azure/aks/azure-disk-volume)，而不是持久卷聲明（靜態預配）。
* 將應用程式部署到 AKS。
* 驗證應用程式是否正常工作。
* 將即時流量指向新的 AKS 群集。

> [!IMPORTANT]
> 如果選擇不靜默寫入，則需要將資料複製到新部署。 否則，您將錯過拍攝磁片快照後寫入的資料。

某些開源工具可以説明您創建託管磁片並在 Kubernetes 群集之間遷移卷：

* [Azure CLI 磁碟複製擴展副本](https://github.com/noelbundick/azure-cli-disk-copy-extension)並跨資源組和 Azure 區域轉換磁片。
* [Azure Kube CLI 擴展](https://github.com/yaron2/azure-kube-cli)枚舉 ACS 庫伯內特卷並將其遷移到 AKS 群集。


### <a name="deployment-of-your-cluster-configuration"></a>部署群集配置

我們建議您使用現有的持續集成 （CI） 和連續交付 （CD） 管道將已知良好的配置部署到 AKS。 可以使用 Azure 管道[生成應用程式並將其部署到 AKS。](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops) 克隆現有部署任務並確保`kubeconfig`指向新的 AKS 群集。

如果無法這樣做，請從現有 Kubernetes 群集匯出資源定義，然後將它們應用於 AKS。 您可以使用 `kubectl` 匯出物件。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>將現有資源移動到另一個區域

您可能希望將 AKS 群集移動到[AKS 支援的不同區域][region-availability]。 我們建議您在其他區域中創建新群集，然後將資源和應用程式部署到新群集。 此外，如果 AKS 群集上運行任何服務（如[Azure 開發人員空間][azure-dev-spaces]），還需要在新區域中的群集上安裝和配置這些服務。


在本文中，我們總結了遷移的詳細資訊：

> [!div class="checklist"]
> * 帶標準負載等化器和虛擬機器規模集的 AKS
> * 現有附加的 Azure 服務
> * 確保有效的配額
> * 高可用性和業務連續性
> * 無狀態應用程式的注意事項
> * 有狀態應用程式的注意事項
> * 部署群集配置


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/