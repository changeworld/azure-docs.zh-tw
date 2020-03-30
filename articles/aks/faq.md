---
title: Azure Kubernetes Service (AKS) 的常見問題集
description: 查找有關 Azure 庫伯奈斯服務 （AKS） 的一些常見問題的解答。
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497763"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 的常見問題集

本文將說明關於 Azure Kubernetes Service (AKS) 的常見問題。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 區域當前提供 AKS？

有關可用區域的完整清單，請參閱[AKS 區域和可用性][aks-regions]。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支援節點自動調整？

是，在 AKS 中自動水準縮放代理節點的能力目前在預覽版中可用。 有關說明[，請參閱自動縮放群集以滿足 AKS 中的應用程式需求][aks-cluster-autoscaler]。 AKS自動縮放基於[庫貝內茨自動縮放器][auto-scaler]。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>可以將 AKS 部署到我現有的虛擬網路嗎？

是的，您可以使用[高級網路功能][aks-advanced-networking]將 AKS 群集部署到現有虛擬網路。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>我可以限制誰有權訪問庫伯內斯 API 伺服器嗎？

是的，您可以使用[API 伺服器授權的 IP 範圍][api-server-authorized-ip-ranges]限制對 Kubernetes API 伺服器的訪問。

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>我能否使 Kubernetes API 伺服器只能在虛擬網路中訪問？

目前不是，但這是計畫好的。 您可以跟蹤[AKS GitHub 存儲庫][private-clusters-github-issue]的進度。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>單個群集中是否可以具有不同的 VM 大小？

是的，您可以通過創建[多個節點池][multi-node-pools]在 AKS 群集中使用不同的虛擬機器大小。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全性更新是否會套用至 AKS 代理程式節點？

Azure 可按每晚計畫自動將安全修補程式應用於群集中的 Linux 節點。 但是，您有責任確保根據需要重新開機這些 Linux 節點。 有幾個用於重新開機節點的選項：

- 手動、透過 Azure 入口網站，或透過 Azure CLI。
- 藉由升級 AKS 叢集。 群集會自動升級[警戒線和排水節點][cordon-drain]，然後將新節點與最新的 Ubuntu 映射和新的修補程式版本或小型 Kubernetes 版本連線。 如需詳細資訊，請參閱[升級 AKS 叢集][aks-upgrade]。
- 通過使用[Kured](https://github.com/weaveworks/kured)Kured，Kubernetes 的開源重啟守護進程。 Kured 作為[守護進程運行](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)，並監視每個節點是否存在指示需要重新開機的檔。 在整個群集中，作業系統重新開機由與群集升級相同的[警戒線和排水過程][cordon-drain]管理。

如需使用 kured 的詳細資訊，請參閱[將安全性和核心更新套用至 AKS 中的節點][node-updates-kured]。

### <a name="windows-server-nodes"></a>Windows 伺服器節點

對於 Windows 伺服器節點（當前在 AKS 中預覽），Windows 更新不會自動運行和應用最新更新。 在有關 Windows 更新發佈週期和您自己的驗證過程的定期計畫下，應在 AKS 群集中的群集和 Windows Server 節點池上執行升級。 此升級過程創建運行最新 Windows Server 映射和修補程式的節點，然後刪除較舊的節點。 有關此過程的詳細資訊，請參閱[升級 AKS 中的節點池][nodepool-upgrade]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>為何會使用 AKS 建立兩個資源群組？

AKS 基於許多 Azure 基礎結構資源構建，包括虛擬機器擴展集、虛擬網路和託管磁片。 這使您能夠利用 AKS 提供的託管 Kubernetes 環境中 Azure 平臺的許多核心功能。 例如，大多數 Azure 虛擬機器類型可以直接與 AKS 一起使用，Azure 預留可用於自動接收這些資源的折扣。

要啟用此體系結構，每個 AKS 部署跨越兩個資源組：

1. 創建第一個資源組。 此組僅包含庫伯內斯服務資源。 AKS 資來源提供者在部署期間自動創建第二個資源組。 第二個資源組的示例*MC_myResourceGroup_myAKSCluster_eastus。* 有關如何指定第二個資源組的名稱的資訊，請參閱下一節。
1. 第二個資源組（稱為*節點資源組*）包含與群集關聯的所有基礎結構資源。 這些資源包括 Kubernetes 節點 VM、虛擬網路和儲存體。 預設情況下，節點資源組的名稱類似于*MC_myResourceGroup_myAKSCluster_eastus*。 每當刪除群集時，AKS 都會自動刪除節點資源，因此它應僅用於共用群集生命週期的資源。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>我可以為 AKS 節點資源組提供自己的名稱嗎？

是。 預設情況下，AKS 將命名節點資源組*MC_resourcegroupname_clustername_location*，但您也可以提供您自己的名稱。

要指定自己的資源組名稱，請安裝[aks 預覽][aks-preview-cli]Azure CLI 擴展版本*0.3.2*或更高版本。 使用[az aks 創建][az-aks-create]命令創建 AKS 群集時，請使用 *--node-資源組*參數並為資源組指定名稱。 如果使用[Azure 資源管理器範本][aks-rm-template]來部署 AKS 群集，則可以使用*節點ResourceGroup*屬性定義資源組名稱。

* 輔助資源組由 Azure 資來源提供者在您自己的訂閱中自動創建。
* 僅當創建群集時，才能指定自訂資源組名稱。

使用節點資源組時，請記住，您不能：

* 為節點資源組指定現有資源組。
* 為節點資源組指定不同的訂閱。
* 創建群集後更改節點資源組名稱。
* 指定節點資源組中的託管資源的名稱。
* 修改或刪除節點資源組中託管資源的標記。 （請參閱下一節中的其他資訊。

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>是否可以修改節點資源組中 AKS 資源的標籤和其他屬性？

如果修改或刪除節點資源組中的 Azure 創建的標記和其他資源屬性，則可能會收到意外的結果，如縮放和升級錯誤。 AKS 允許您創建和修改自訂標記。 您可能希望創建或修改自訂標記，例如，分配業務單位或成本中心。 通過修改 AKS 群集中節點資源組下的資源，可以打破服務等級目標 （SLO）。 有關詳細資訊，請參閱[AKS 是否提供服務等級協定？](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS 支援哪些 Kubernetes 許可控制器？ 是否可以新增或移除許可控制器？

AKS 支援下列[許可控制器][admission-controllers]：

- *NamespaceLifecycle*
- *LimitRanger*
- *服務帳戶*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

目前，您無法修改 AKS 中的入場控制器清單。

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault 是否會與 AKS 整合？

AKS 當前未與 Azure 金鑰保存庫本機集成。 但是，[庫伯內斯專案的 Azure 金鑰保存庫 FlexVolume 專案][keyvault-flexvolume]支援從 Kubernetes 窗格直接集成到金鑰保存庫機密。

## <a name="can-i-run-windows-server-containers-on-aks"></a>我是否可以在 AKS 上執行 Windows Server 容器？

是，Windows Server 容器在預覽版中可用。 要在 AKS 中運行 Windows Server 容器，請創建一個節點池，該節點池將 Windows 伺服器作為客體作業系統運行。 Windows 伺服器容器只能使用 Windows 伺服器 2019。 要開始，請參閱[使用 Windows Server 節點池創建 AKS 群集][aks-windows-cli]。

對節點池的 Windows 伺服器支援包括一些限制，這些限制是 Kubernetes 專案中上游 Windows Server 的一部分。 有關這些限制的詳細資訊，請參閱[AKS 限制 中的 Windows Server 容器][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服務等級協定？

在服務等級協定 （SLA） 中，如果未滿足已發佈的服務等級，供應商同意向客戶報銷服務成本。 由於 AKS 是免費的，因此無需報銷任何費用，因此 AKS 沒有正式的 SLA。 但是，AKS 尋求保持 Kubernetes API 伺服器至少 99.5% 的可用性。

請務必認識到 AKS 服務可用性之間的區別，後者是指 Kubernetes 控制平面的正常執行時間，以及 Azure 虛擬機器上運行的特定工作負載的可用性。 儘管如果控制平面未就緒，控制平面可能不可用，但 Azure VM 上運行的群集工作負荷仍可以正常工作。 給定 Azure VM 是付費資源，它們由財務 SLA 支援。 請在此處閱讀，瞭解有關 Azure VM SLA 以及如何使用[可用性區域][availability-zones]等功能提高可用性[的更多詳細資訊](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。

## <a name="why-cant-i-set-maxpods-below-30"></a>為什麼我不能將 maxPods 設置為 30 以下？

在 AKS 中，可以使用`maxPods`Azure CLI 和 Azure 資源管理器範本在創建群集時設置值。 但是，Kubenet 和 Azure CNI 都需要*最小值*（在創建時驗證）：

| 網路 | 最小值 | 最大值 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| 庫貝內特 | 30 | 110 |

由於 AKS 是託管服務，因此我們部署和管理載入項和 pod 作為群集的一部分。 過去，使用者可以定義`maxPods`的值低於託管窗格運行所需的值（例如，30）。 AKS 現在使用此公式計算最小窗格數：（（maxPods 或（maxPods = vm_count）））>託管附加窗格的最小值。

使用者無法覆蓋最小`maxPods`驗證。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>是否可以將 Azure 預留折扣應用於 AKS 代理節點？

AKS 代理節點作為標準 Azure 虛擬機器計費，因此，如果您已購買 AKS 中正在使用的 VM 大小的[Azure 預留][reservation-discounts]，則會自動應用這些折扣。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>是否可以在 Azure 租戶之間移動/遷移群集？

該`az aks update-credentials`命令可用於在 Azure 租戶之間移動 AKS 群集。 按照["選擇更新"或"創建服務主體](https://docs.microsoft.com/azure/aks/update-credentials)"中的說明進行操作，然後[使用新憑據更新 aks 群集](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>是否可以在訂閱之間移動/遷移群集？

當前不支援在訂閱之間移動群集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>是否可以將 AKS 群集從當前 Azure 訂閱移動到另一個 Azure 訂閱？ 

不支援在 Azure 訂閱之間移動 AKS 群集及其關聯的資源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>為什麼我的群集刪除需要這麼長時間？ 

大多數群集應使用者請求刪除;在某些情況下，特別是當客戶自帶資源組或執行跨 RG 任務刪除時，可能需要更多時間或失敗。 如果刪除出現問題，請仔細檢查 RG 上沒有鎖，RG 以外的任何資源都與 RG 無關等。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果我的"NodeLost"或"未知"狀態下有 pod/部署，我是否可以仍升級我的群集？

您可以，但 AKS 不建議這樣做。 理想情況下，當群集的狀態已知且正常時，應執行升級。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果我有一個具有一個或多個節點的群集處於不正常狀態或關閉，是否可以執行升級？

否，請在升級之前刪除/刪除處於故障狀態或從群集中刪除的任何節點。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>我運行了群集刪除，但看到錯誤`[Errno 11001] getaddrinfo failed` 

最常見的情況是，這是由使用者仍有一個或多個網路安全性群組 （NSG） 仍在使用並與群集關聯造成的。  請刪除它們，然後重試刪除。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>我運行了升級，但現在我的窗格處於崩潰迴圈中，並且就緒性探測失敗？

請確認您的服務主體尚未過期。  請參閱[：AKS服務主體](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS更新憑據](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>我的群集工作，但突然無法預配負載平衡器、安裝 PVC 等？ 

請確認您的服務主體尚未過期。  請參閱[：AKS服務主體](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS更新憑據](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>我可以使用虛擬機器規模集 API 手動縮放嗎？

不可以，不支援使用虛擬機器規模集 API 進行縮放操作。 使用 AKS API`az aks scale`（ 。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>我可以使用虛擬機器縮放集手動縮放到 0 個節點嗎？

不可以，不支援使用虛擬機器規模集 API 進行縮放操作。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>我可以停止或取消分配所有 VM 嗎？

雖然 AKS 具有能夠承受此類配置並從中恢復的彈性機制，但這不是推薦的配置。

## <a name="can-i-use-custom-vm-extensions"></a>我可以使用自訂 VM 擴展嗎？

沒有 AKS 是託管服務，並且不支援對 IaaS 資源的操作。 安裝自訂群組件等。 請利用庫伯奈斯 API 和機制。 例如，利用守護進程集來安裝所需的元件。

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
