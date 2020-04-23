---
title: Azure Kubernetes Service (AKS) 的常見問題集
description: 尋找一些關於 Azure Kubernetes Service （AKS）常見問題的解答。
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: f91fe1c63430a0eac23cf9cbc184babb6dd5f7a4
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106079"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 的常見問題集

本文將說明關於 Azure Kubernetes Service (AKS) 的常見問題。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 區域目前提供 AKS？

如需可用區域的完整清單，請參閱[AKS 區域和可用性][aks-regions]。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支援節點自動調整？

是，在 AKS 中，以水準方式自動調整代理程式節點的功能目前僅供預覽。 如需相關指示，請參閱[在 AKS 中自動調整叢集以符合應用程式需求][aks-cluster-autoscaler]。 AKS 自動調整是以[Kubernetes 自動調整程式][auto-scaler]為基礎。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>可以將 AKS 部署到我現有的虛擬網路嗎？

是，您可以使用[advanced network 功能][aks-advanced-networking]，將 AKS 叢集部署到現有的虛擬網路中。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>我可以限制能夠存取 Kubernetes API 伺服器的人員嗎？

是，您可以使用[API 伺服器授權的 IP 範圍][api-server-authorized-ip-ranges]來限制 Kubernetes API 伺服器的存取權。

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>我可以讓 Kubernetes API 伺服器只在我的虛擬網路中存取嗎？

目前尚不這麼做，但已進行規劃。 您可以在[AKS GitHub][private-clusters-github-issue]存放庫上追蹤進度。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>單一叢集中是否可以有不同的 VM 大小？

是，您可以藉由建立[多個節點][multi-node-pools]集區，在 AKS 叢集中使用不同的虛擬機器大小。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全性更新是否會套用至 AKS 代理程式節點？

Azure 會在夜間排程自動將安全性修補程式套用至叢集中的 Linux 節點。 不過，您必須負責確保這些 Linux 節點會視需要重新開機。 您有數個選項可以重新開機節點：

- 手動、透過 Azure 入口網站，或透過 Azure CLI。
- 藉由升級 AKS 叢集。 叢集會自動升級[cordon 和清空節點][cordon-drain]，然後使用最新的 Ubuntu 映射和新的修補程式版本或次要 Kubernetes 版本，讓新的節點上線。 如需詳細資訊，請參閱[升級 AKS 叢集][aks-upgrade]。
- 藉由使用[Kured](https://github.com/weaveworks/kured)，Kubernetes 的開放原始碼重新開機守護程式。 Kured 會以[DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)的形式執行，並監視每個節點是否有指出需要重新開機的檔案。 在叢集上，OS 重新開機是由相同的[cordon，並][cordon-drain]在叢集升級時進行的清空程式所管理。

如需使用 kured 的詳細資訊，請參閱[將安全性和核心更新套用至 AKS 中的節點][node-updates-kured]。

### <a name="windows-server-nodes"></a>Windows Server 節點

對於 Windows Server 節點（目前在 AKS 中處於預覽狀態），Windows Update 不會自動執行並套用最新的更新。 依照 Windows Update 發行週期和您自己的驗證程式的定期排程，您應該在 AKS 叢集中的叢集和 Windows Server 節點集區上執行升級。 此升級程式會建立節點來執行最新的 Windows Server 映射和修補程式，然後移除較舊的節點。 如需此程式的詳細資訊，請參閱[升級 AKS 中的節點集][nodepool-upgrade]區。

## <a name="why-are-two-resource-groups-created-with-aks"></a>為何會使用 AKS 建立兩個資源群組？

AKS 建基於一些 Azure 基礎結構資源，包括虛擬機器擴展集、虛擬網路和受控磁片。 這可讓您在 AKS 所提供的受控 Kubernetes 環境中，利用 Azure 平臺的許多核心功能。 例如，大部分的 Azure 虛擬機器類型可直接與 AKS 搭配使用，而 Azure 保留可用於自動接收這些資源的折扣。

若要啟用此架構，每個 AKS 部署都跨越兩個資源群組：

1. 您會建立第一個資源群組。 此群組僅包含 Kubernetes 服務資源。 AKS 資源提供者會在部署期間自動建立第二個資源群組。 第二個資源群組的範例是*MC_myResourceGroup_myAKSCluster_eastus*。 如需如何指定此第二個資源群組名稱的相關資訊，請參閱下一節。
1. 第二個資源群組（稱為*節點資源群組*）包含與叢集相關聯的所有基礎結構資源。 這些資源包括 Kubernetes 節點 VM、虛擬網路和儲存體。 根據預設，節點資源群組具有類似*MC_myResourceGroup_myAKSCluster_eastus*的名稱。 每當刪除叢集時，AKS 就會自動刪除節點資源，因此它應該只用于共用叢集生命週期的資源。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>我可以為 AKS 節點資源群組提供自己的名稱嗎？

是。 根據預設，AKS 會將節點資源群組命名為*MC_resourcegroupname_clustername_location*，但您也可以提供您自己的名稱。

若要指定您自己的資源組名，請安裝[aks-preview][aks-preview-cli] Azure CLI 延伸模組版本*0.3.2*或更新版本。 當您使用[az AKS create][az-aks-create]命令建立 AKS 叢集時，請使用 *--node--群組*參數並指定資源群組的名稱。 如果您[使用 Azure Resource Manager 範本][aks-rm-template]來部署 AKS 叢集，您可以使用*nodeResourceGroup*屬性來定義資源組名。

* 次要資源群組會由您自己的訂用帳戶中的 Azure 資源提供者自動建立。
* 只有在建立叢集時，才可以指定自訂資源組名。

當您使用 node 資源群組時，請記住，您不能：

* 為節點資源群組指定現有的資源群組。
* 為節點資源群組指定不同的訂用帳戶。
* 建立叢集之後，請變更節點資源組名。
* 指定節點資源群組內的受控資源名稱。
* 修改或刪除節點資源群組中的受控資源標記。 （請參閱下一節中的其他資訊）。

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>我可以在節點資源群組中修改 AKS 資源的標記和其他屬性嗎？

如果您在節點資源群組中修改或刪除 Azure 建立的標記和其他資源屬性，您可能會收到非預期的結果，例如調整和升級錯誤。 AKS 可讓您建立和修改自訂標記。 例如，您可能會想要建立或修改自訂標記，以指派業務單位或成本中心。 藉由修改 AKS 叢集中節點資源群組下的資源，您會中斷服務層級目標（SLO）。 如需詳細資訊，請參閱[AKS 是否提供服務等級協定？](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS 支援哪些 Kubernetes 許可控制器？ 是否可以新增或移除許可控制器？

AKS 支援下列[許可控制器][admission-controllers]：

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

目前，您無法修改 AKS 中的許可控制器清單。

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>我可以在 AKS 上使用許可控制站 webhook 嗎？

是，您可以在 AKS 上使用「許可控制器 webhook。 建議您排除以 [**控制平面] 卷**標標記的內部 AKS 命名空間。 例如，將下列內容新增至 webhook 設定：

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>許可控制站 webhook 會影響 kube 系統和內部 AKS 命名空間嗎？

為了保護系統的穩定性，並防止自訂的許可控制器影響 kube 系統中的內部服務，namespace AKS 具有**許可 Enforcer**，它會自動排除 kube 系統和 AKS 內部命名空間。 此服務可確保自訂的許可控制器不會影響在 kube 系統中執行的服務。

如果您的使用案例是在 kube 系統（不建議）上部署某些專案，而您的自訂許可 webhook 必須涵蓋這些專案，您可以新增下列標籤或批註，讓許可 Enforcer 忽略它。

標籤```"admissions.enforcer/disabled": "true"``` ：或批註：```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault 是否會與 AKS 整合？

AKS 目前不會與 Azure Key Vault 整合。 不過，[適用于 Kubernetes 專案的 Azure Key Vault FlexVolume][keyvault-flexvolume]可讓您從 Kubernetes pod 直接整合到 Key Vault 的秘密。

## <a name="can-i-run-windows-server-containers-on-aks"></a>我是否可以在 AKS 上執行 Windows Server 容器？

是，Windows Server 容器可供預覽。 若要在 AKS 中執行 Windows Server 容器，請建立執行 Windows Server 的節點集區做為虛擬作業系統。 Windows Server 容器只能使用 Windows Server 2019。 若要開始使用，請參閱[建立具有 Windows Server 節點集][aks-windows-cli]區的 AKS 叢集。

適用于節點集區的 Windows Server 支援包含在 Kubernetes 專案中屬於上游 Windows Server 的一些限制。 如需這些限制的詳細資訊，請參閱[AKS 限制的 Windows Server 容器][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服務等級協定？

在服務等級協定（SLA）中，如果未符合已發佈的服務層級，則提供者會同意補償客戶的服務成本。 由於 AKS 是免費的，因此不會有任何費用可補償，因此 AKS 沒有正式的 SLA。 不過，AKS 會尋求維護至少99.5% 的 Kubernetes API 伺服器可用性。

請務必辨識 AKS 服務可用性之間的區別，這是指 Kubernetes 控制平面的執行時間，以及在 Azure 虛擬機器上執行的特定工作負載可用性。 雖然控制平面可能無法使用，但如果沒有就緒，則在 Azure Vm 上執行的叢集工作負載仍然可以運作。 假設 Azure Vm 是付費資源，其受金融 SLA 支援。 如需 Azure VM SLA 的詳細資訊，以及如何使用[可用性區域][availability-zones]之類的功能增加該可用性，請參閱[這裡](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。

## <a name="why-cant-i-set-maxpods-below-30"></a>為什麼我無法將 maxPods 設定為30以下？

在 AKS 中，您可以使用`maxPods` [Azure CLI] 和 [Azure Resource Manager] 範本來設定建立叢集時的值。 不過，Kubenet 和 Azure CNI 都需要*最小值*（在建立時進行驗證）：

| 網路功能 | 最小值 | 最大值 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

因為 AKS 是受控服務，所以我們會部署和管理附加元件和 pod，做為叢集的一部分。 在過去，使用者可以將`maxPods`值定義為低於所需的受控 pod 執行的值（例如，30）。 AKS 現在會使用下列公式來計算最小 pod 數目：（（maxPods 或（maxPods * vm_count）） > 受管理的附加元件最少。

使用者無法覆寫最低`maxPods`驗證。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>我可以將 Azure 保留折扣套用至我的 AKS 代理程式節點嗎？

AKS 代理程式節點會以標準 Azure 虛擬機器計費，因此如果您已針對在 AKS 中使用的 VM 大小購買[Azure 保留][reservation-discounts]，則會自動套用這些折扣。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>我可以在 Azure 租使用者之間移動/遷移我的叢集嗎？

命令`az aks update-credentials`可以用來在 Azure 租使用者之間移動 AKS 叢集。 遵循[選擇更新或建立服務主體](https://docs.microsoft.com/azure/aks/update-credentials)中的指示，然後[使用新的認證更新 aks](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)叢集。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>我可以在訂用帳戶之間移動/遷移我的叢集嗎？

目前不支援在訂用帳戶之間移動叢集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>我可以將我的 AKS 叢集從目前的 azure 訂用帳戶移至另一個嗎？ 

不支援在 Azure 訂用帳戶之間移動您的 AKS 叢集和其相關聯的資源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>為什麼我的叢集刪除會花費很長的時間？ 

大部分的叢集會在使用者要求時刪除;在某些情況下，特別是當客戶帶入自己的資源群組，或執行跨 RG 工作刪除作業時，可能需要額外的時間或失敗。 如果您有刪除的問題，請再次檢查您沒有 RG 的鎖定，該 rg 外的任何資源會從 RG 解除關聯等等。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果我有處於 ' NodeLost ' 或「不明」狀態的 pod/部署，仍然可以升級我的叢集嗎？

您可以，但 AKS 不建議這麼做。 理想的升級應該在叢集的狀態為已知且狀況良好時執行。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果我有一個或多個節點處於狀況不良狀態或已關閉的叢集，我可以執行升級嗎？

否，請刪除/移除處於失敗狀態的任何節點，或在升級之前從叢集中移除。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>我已執行叢集刪除，但看到錯誤`[Errno 11001] getaddrinfo failed` 

最常見的原因是使用者有一或多個網路安全性群組（Nsg）仍在使用中，且與叢集相關聯。  請將其移除，然後再次嘗試刪除。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>我已執行升級，但現在我的 pod 處於損毀迴圈，而準備好探查失敗？

請確認您的服務主體未過期。  請參閱： [AKS 服務主體](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS 更新認證](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>我的叢集已正常運作，但突然無法布建 LoadBalancers、掛接 Pvc 等專案？ 

請確認您的服務主體未過期。  請參閱： [AKS 服務主體](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS 更新認證](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>我可以使用虛擬機器擴展集 Api 來手動調整嗎？

否，不支援使用虛擬機器擴展集 Api 進行調整作業。 使用 AKS Api （`az aks scale`）。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>我可以使用虛擬機器擴展集來手動調整為0個節點嗎？

否，不支援使用虛擬機器擴展集 Api 進行調整作業。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>我可以停止或取消配置我的所有 Vm 嗎？

雖然 AKS 具有可承受這類設定並從中復原的恢復機制，但這不是建議的設定。

## <a name="can-i-use-custom-vm-extensions"></a>我可以使用自訂 VM 擴充功能嗎？

AKS 不是受控服務，且不支援操作 IaaS 資源。 安裝自訂群組件等。 請利用 Kubernetes Api 和機制。 例如，利用 Daemonset 來安裝必要的元件。

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
