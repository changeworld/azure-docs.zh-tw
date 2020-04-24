---
title: 遷移至 Azure Kubernetes Service （AKS）
description: 遷移至 Azure Kubernetes Service （AKS）。
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 9a5e2c1e36a742115ed2f5c690c81a186a86dee7
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129091"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>遷移至 Azure Kubernetes Service （AKS）

本文可協助您規劃和執行成功的遷移至 Azure Kubernetes Service （AKS）。 為了協助您做出重要的決策，本指南提供 AKS 目前建議設定的詳細資料。 本文並未涵蓋每個案例，而且在適當的情況下，本文會包含更多詳細資訊的連結，以便規劃成功的遷移。

本檔可用於協助支援下列案例：

* 將[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)所支援的 AKS 叢集遷移至[虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* 遷移 AKS 叢集以使用[標準 SKU 負載平衡器](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* 從[Azure Container Service （ACS）遷移-從2020年1月31日淘汰](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)至 AKS
* 從[AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)遷移至 AKS
* 從非以 Azure 為基礎的 Kubernetes 叢集遷移至 AKS

在遷移時，請確定您的目標 Kubernetes 版本位於支援的 AKS 視窗內。 如果使用較舊的版本，它可能不在支援的範圍內，而且需要升級 AKS 支援的版本。 如需詳細資訊，請參閱[AKS 支援的 Kubernetes 版本](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)。

如果您要遷移至較新版本的 Kubernetes，請參閱[Kubernetes 版本和版本誤差支援原則](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)。

視您的案例而定，有數個開放原始碼工具可協助您進行遷移：

* [Velero](https://velero.io/) （需要 Kubernetes 1.7 +）
* [Azure Kube CLI 擴充功能](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

在本文中，我們將摘要說明的遷移詳細資料：

> [!div class="checklist"]
> * Standard Load Balancer 和虛擬機器擴展集的 AKS
> * 現有已附加的 Azure 服務
> * 確保有效的配額
> * 高可用性和商務持續性
> * 無狀態應用程式的考慮
> * 具狀態應用程式的考慮
> * 部署叢集設定

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>Standard Load Balancer 和虛擬機器擴展集的 AKS

AKS 是受控服務，以較低的管理額外負荷提供獨特的功能。 由於成為受控服務，因此您必須從 AKS 支援的一組[區域](https://docs.microsoft.com/azure/aks/quotas-skus-regions)中選取。 從現有的叢集轉換到 AKS 可能需要修改現有的應用程式，使其在 AKS 受控控制平面上保持良好狀態。

我們建議使用[虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets)和[Azure STANDARD LOAD BALANCER](https://docs.microsoft.com/azure/aks/load-balancer-standard)支援的 AKS 叢集，以確保您可以取得諸如[多個節點](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)集區、[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)、[授權的 IP 範圍](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges)、叢集[自動調整程式](https://docs.microsoft.com/azure/aks/cluster-autoscaler)、 [AKS 的 Azure 原則](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)，以及發行時的其他新功能等功能。

由[虛擬機器可用性設定組](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)支援的 AKS 叢集缺少許多這些功能的支援。

下列範例會建立 AKS 叢集，其中包含虛擬機器擴展集所支援的單一節點集區。 它會使用標準負載平衡器。 它也會在叢集的節點集區上啟用叢集自動調整程式，並設定最少*1*個和最多*3*個節點：

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

## <a name="existing-attached-azure-services"></a>現有已附加的 Azure 服務

在遷移叢集時，您可能已附加外部 Azure 服務。 這些不需要重新建立資源，但它們將需要更新從先前到新叢集的連線，才能維護功能。

* Azure Container Registry
* Log Analytics
* Application Insights
* 流量管理員
* 儲存體帳戶
* 外部資料庫

## <a name="ensure-valid-quotas"></a>確保有效的配額

由於在移轉期間會有額外的虛擬機器部署到訂用帳戶，因此您應確認您的配額與限制足以讓這些資源使用。 您可能需要要求增加[vCPU 配額](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。

您可能需要要求增加[網路配額](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests)，以確保您不會耗盡 ip。 如需其他資訊，請參閱[AKS 的網路和 IP 範圍](https://docs.microsoft.com/azure/aks/configure-kubenet)。

如需詳細資訊，請參閱[Azure 訂用帳戶和服務限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。 若要檢查您目前的配額，請在 Azure 入口網站[中，移](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)至 [訂用帳戶] 分頁，選取您的訂閱，然後選取 [**使用量 + 配額**]。

## <a name="high-availability-and-business-continuity"></a>高可用性和商務持續性

如果您的應用程式無法處理停機時間，您將需要遵循高可用性遷移案例的最佳作法。  複雜商務持續性規劃、嚴重損壞修復和最大化執行時間的最佳作法已超出本檔的範圍。  若要深入瞭解，請參閱[Azure Kubernetes Service （AKS）中的商務持續性和嚴重損壞修復的最佳作法](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)。

對於複雜的應用程式，您的遷移作業通常會進行一段時間，而非全部一次完成。 這表示新舊環境可能需要透過網路進行通訊。 先前使用`ClusterIP`服務進行通訊的應用程式可能需要公開為類型`LoadBalancer` ，並適當地加以保護。

若要完成遷移，您需要將用戶端指向在 AKS 上執行的新服務。 我們建議您將 DNS 更新為指向位於 AKS 叢集前方的 Load Balancer，以重新導向流量。

[Azure 流量管理員](https://docs.microsoft.com/azure/traffic-manager/)可以將客戶導向所需的 Kubernetes 叢集和應用程式實例。  流量管理員是以 DNS 為基礎的流量負載平衡器，可將網路流量分散到各個區域。  為了獲得最佳效能和冗余，請將所有應用程式流量導向至 AKS 叢集之前，先透過流量管理員。  在 multicluster 部署中，客戶應連線到指向每個 AKS 叢集上之服務的流量管理員 DNS 名稱。 使用流量管理員端點來定義這些服務。 每個端點都是*服務負載平衡器 IP*。 使用此設定可將某個區域中流量管理員端點的網路流量導向不同區域中的端點。

![具有流量管理員的 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front 開門服務](https://docs.microsoft.com/azure/frontdoor/front-door-overview)是路由傳送 AKS 叢集流量的另一個選項。  Azure Front Door Service 可讓您針對最佳效能和立即全域容錯移轉以獲得高可用性最佳化，定義、管理及監視網路流量的全域路由。 

### <a name="considerations-for-stateless-applications"></a>無狀態應用程式的考慮

無狀態應用程式移轉是最直接的案例。 將您的資源定義（YAML 或 Helm）套用至新叢集，確定所有專案都如預期般運作，然後重新導向流量以啟用您的新叢集。

### <a name="considerations-for-stateful-applications"></a>具狀態應用程式的考慮

請仔細規劃您的具狀態應用程式遷移，以避免資料遺失或非預期的停機時間。

如果您使用 Azure 檔案儲存體，您可以將檔案共用掛接為磁片區以作為新的叢集：
* [將靜態 Azure 檔案儲存體掛接為磁片區](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

如果您使用 Azure 受控磁碟，只有在未連接到任何 VM 時，才可以掛接磁片：
* [將靜態 Azure 磁片掛接為磁片區](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

如果這兩種方法都不適用，您可以使用備份和還原選項：
* [Azure 上的 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Azure 檔案

不同於磁碟，Azure 檔案服務可同時掛接到多部主機。 在您的 AKS 叢集中，Azure 和 Kubernetes 不會防止您建立 ACS 叢集仍然使用的 pod。 若要防止資料遺失和非預期的行為，請確定叢集不會同時寫入相同的檔案。

如果您的應用程式可以裝載多個指向相同檔案共用的複本，請遵循無狀態遷移步驟，並將您的 YAML 定義部署至新叢集。 如果不是，則可能的移轉方法包含下列步驟：

* 驗證您的應用程式是否正常運作。
* 將您的即時流量指向新的 AKS 叢集。
* 中斷舊叢集的連線。

如果您想要從空的共用開始，並複製來源資料，您可以使用[`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest)命令來遷移資料。


#### <a name="migrating-persistent-volumes"></a>遷移持續性磁片區

如果您要將現有的持續性磁片區遷移至 AKS，通常會遵循下列步驟：

* 停止寫入應用程式。 （這是選擇性步驟，需要停機）。
* 製作磁片的快照集。
* 從快照集建立新的受控磁片。
* 在 AKS 中建立持續性磁片區。
* 將 pod 規格更新為[使用現有的磁片](https://docs.microsoft.com/azure/aks/azure-disk-volume)區，而不是 PersistentVolumeClaims （靜態布建）。
* 將您的應用程式部署至 AKS。
* 驗證您的應用程式是否正常運作。
* 將您的即時流量指向新的 AKS 叢集。

> [!IMPORTANT]
> 如果您選擇不停止寫入，則必須將資料複寫到新的部署。 否則，您將會錯過在磁片快照集之後所寫入的資料。

某些開放原始碼工具可協助您建立受控磁片，並在 Kubernetes 叢集之間遷移磁片區：

* [Azure CLI 磁碟複製延伸](https://github.com/noelbundick/azure-cli-disk-copy-extension)模組會在資源群組和 Azure 區域之間複製和轉換磁片。
* [Azure KUBE CLI 擴充](https://github.com/yaron2/azure-kube-cli)功能會列舉 ACS Kubernetes 磁片區，並將其遷移至 AKS 叢集。


### <a name="deployment-of-your-cluster-configuration"></a>部署叢集設定

建議您使用現有的持續整合（CI）和持續傳遞（CD）管線，將已知良好的設定部署至 AKS。 您可以使用 Azure Pipelines 來[建立應用程式，並將其部署至 AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops)。 複製現有的部署工作，並確定`kubeconfig`指向新的 AKS 叢集。

如果無法這麼做，請從現有的 Kubernetes 叢集中匯出資源定義，然後將它們套用至 AKS。 您可以使用 `kubectl` 匯出物件。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>將現有的資源移至另一個區域

您可能想要將 AKS 叢集移至[AKS 所支援的不同區域][region-availability]。 我們建議您在其他區域中建立新的叢集，然後將您的資源和應用程式部署到新的叢集。 此外，如果您的 AKS 叢集上有[Azure Dev Spaces][azure-dev-spaces]執行的服務，您也必須在新區域中的叢集上安裝及設定這些服務。


在本文中，我們摘要說明下列專案的遷移詳細資料：

> [!div class="checklist"]
> * Standard Load Balancer 和虛擬機器擴展集的 AKS
> * 現有已附加的 Azure 服務
> * 確保有效的配額
> * 高可用性和商務持續性
> * 無狀態應用程式的考慮
> * 具狀態應用程式的考慮
> * 部署叢集設定


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/