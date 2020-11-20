---
title: Azure Red Hat OpenShift 的常見問題
description: 以下是 Microsoft Azure Red Hat 的常見問題解答 OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: 3a474228776c689dbbd6f15ddd926f29383400ce
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964706"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift 常見問題

本文將回答有關 Microsoft Azure Red Hat OpenShift 的常見問題 (常見問題) 。

## <a name="installation-and-upgrade"></a>安裝與升級

### <a name="which-azure-regions-are-supported"></a>支援哪些 Azure 區域？

如需 Azure Red Hat OpenShift 4.x 支援的區域清單，請參閱可用的 [區域](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)。

如需 Azure Red Hat OpenShift 3.11 支援的區域清單，請參閱 [依區域提供的產品](supported-resources.md#azure-regions)。

### <a name="what-virtual-machine-sizes-can-i-use"></a>我可以使用哪些虛擬機器大小？

如需 Azure Red Hat OpenShift 4 支援的虛擬機器大小清單，請參閱 [Azure Red Hat OpenShift 4 支援的資源](support-policies-v4.md)。

如需 Azure Red Hat OpenShift 3.11 支援的虛擬機器大小清單，請參閱 [Azure Red Hat OpenShift 3.11 支援的資源](supported-resources.md)。

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 叢集中的 pod 數目上限為何？  Azure Red Hat OpenShift 中每個節點的最大 pod 數目為何？

受支援 pod 的實際數目取決於應用程式的記憶體、CPU 和儲存體需求。

Azure Red Hat OpenShift 4.x 有250個 pod-每個節點的限制和100個計算節點的限制。 這會將叢集中支援的 pod 數目上限設為 250 &times; 100 = 25000。

Azure Red Hat OpenShift 3.11 具有50個 pod 節點的限制和20個計算節點的限制。 這會將叢集中支援的 pod 數目上限設為 50 &times; 20 = 1000。

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>叢集是否可以跨多個 Azure 區域具有計算節點？

否。 Azure Red Hat OpenShift 叢集中的所有節點都必須來自相同的 Azure 區域。

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>叢集是否可以部署到多個可用性區域？

是。 如果您的叢集部署至支援可用性區域的 Azure 區域，則會自動發生這種情況。 如需詳細資訊，請參閱[可用性區域](../availability-zones/az-overview.md#availability-zones)。

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>控制平面節點是否會隨著 Azure Kubernetes Service (AKS) 而抽象化？

否。 所有資源（包括叢集主要節點）都是在您的客戶訂用帳戶中執行。 這些類型的資源會放在唯讀的資源群組中。

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>叢集是否位於客戶訂用帳戶中？ 

Azure 受控應用程式會存在於具有客戶訂用帳戶的已鎖定資源群組中。 客戶可以查看該資源群組中的物件，但不能加以修改。

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Azure Red Hat OpenShift 中是否有任何元素與其他客戶共用？ 或是所有專案都是獨立的？

每個 Azure Red Hat OpenShift 叢集都專用於特定的客戶，並存在於客戶的訂用帳戶中。 

### <a name="are-infrastructure-nodes-available"></a>是否可使用基礎結構節點？

在 Azure Red Hat OpenShift 4.x 叢集上，目前無法使用基礎結構節點。

在 Azure Red Hat OpenShift 3.11 叢集上，預設會包含基礎結構節點。

## <a name="how-do-i-handle-cluster-upgrades"></a>如何? 處理叢集升級？

如需升級、維護及支援版本的詳細資訊，請參閱 [支援週期指南](support-lifecycle.md)。

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>主機作業系統和 OpenShift 軟體將如何更新？

主機作業系統和 OpenShift 軟體會更新，因為 Azure Red Hat OpenShift 會使用來自上游 OpenShift 容器平臺的次要發行版本和修補程式。

### <a name="whats-the-process-to-reboot-the-updated-node"></a>重新開機已更新節點的程式為何？

節點會在升級過程中重新開機。

## <a name="cluster-operations"></a>叢集作業

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>我可以使用 Prometheus 來監視我的應用程式嗎？

Prometheus 適用于 Azure Red Hat OpenShift 4.x 叢集的預先安裝和設定。 深入瞭解叢集 [監視](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html)。

針對 Azure Red Hat OpenShift 3.11 叢集，您可以在命名空間中部署 Prometheus，並監視命名空間中的應用程式。 如需詳細資訊，請參閱 [在 Azure Red Hat OpenShift 叢集中部署 Prometheus 實例](howto-deploy-prometheus.md)。

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>我可以使用 Prometheus 來監視與叢集健康情況和容量相關的度量嗎？

在 Azure Red Hat OpenShift 4.x 中：是。

在 Azure Red Hat OpenShift 3.11： No。

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>基礎 Vm 的記錄可以串流處理到客戶記錄分析系統嗎？

來自基礎 Vm 的記錄是由受控服務處理，不會對客戶公開。

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>客戶如何存取節點層級的計量（例如 CPU/記憶體），以採取調整、偵測問題等等的動作？ 我似乎無法在 Azure Red Hat OpenShift 叢集上執行 kubectl top。

針對 Azure Red Hat OpenShift 4.x 叢集，OpenShift web 主控台包含節點層級的所有計量。 如需詳細資訊，請參閱關於如何 [查看叢集資訊](https://docs.openshift.com/aro/4/web_console/using-dashboard-to-get-cluster-information.html)的 Red Hat 檔。

針對 Azure Red Hat OpenShift 3.11 叢集，客戶可以使用命令或透過客戶-系統管理員叢集角色，存取節點層級的 CPU/記憶體計量 `oc adm top nodes` `kubectl top nodes` 。 客戶也可以使用命令或來存取的 CPU/記憶體計量 `pods` `oc adm top pods` `kubectl top pods` 。

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>如果我們擴大部署，Azure 容錯網域如何對應至 pod 放置，以確保服務的所有 pod 都不會因為單一容錯網域中的失敗而被挖掉？

在 Azure 中使用虛擬機器擴展集時，預設會有五個容錯網域。 擴展集中的每個虛擬機器實例都會放入其中一個容錯網域。 這可確保部署至叢集中計算節點的應用程式將會放置在不同的容錯網域中。

如需詳細資訊，請參閱 [為虛擬機器擴展集選擇正確的容錯網域數目](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md)。

### <a name="is-there-a-way-to-manage-pod-placement"></a>是否有管理 pod 放置的方法？

客戶能夠取得節點，並以客戶-系統管理員的身分來查看標籤。這會提供一種方式，以將擴展集中的任何 VM 設為目標。

使用特定標籤時必須謹慎使用：

- 不得使用主機名稱。 主機名稱通常會隨著升級和更新進行輪替，並保證會變更。
- 如果客戶有特定標籤或部署策略的要求，可以完成這項作業，但目前不支援工程工作。

如需詳細資訊，請參閱 [控制 pod 放置](https://docs.openshift.com/aro/4/nodes/scheduling/nodes-scheduler-about.html)。

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>映射登錄是否可在外部使用，因此我可以使用 Jenkins 之類的工具？

針對4.x 叢集，您需要公開安全的登錄並設定驗證。 如需詳細資訊，請參閱下列 Red Hat 檔：

- [公開登錄](https://docs.openshift.com/aro/4/registry/securing-exposing-registry.html)
- [存取登錄](https://docs.openshift.com/aro/4/registry/accessing-the-registry.html)

針對3.11 叢集，可以使用 Docker 映射登錄。 Docker 登錄可從取得 `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` 。 您也可以使用 Azure Container Registry。

## <a name="networking"></a>網路功能

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>我可以將叢集部署到現有的虛擬網路嗎？

在4.x 叢集中，您可以將叢集部署到現有的 VNet 中。

在3.11 叢集中，您無法將叢集部署到現有的 VNet。 您可以透過對等互連將 Azure Red Hat OpenShift 3.11 叢集連線到現有的 VNet。

### <a name="is-cross-namespace-networking-supported"></a>是否支援跨命名空間網路？

客戶和個別專案系統管理員可以自訂跨命名空間的網路 (，包括使用物件將其以個別專案來拒絕) `NetworkPolicy` 。

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>我嘗試對等互連至不同訂用帳戶中的虛擬網路，但無法取得 VNet CIDR 錯誤。

在具有虛擬網路的訂用帳戶中，請務必 `Microsoft.ContainerService` 使用下列命令來註冊 provider： `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>可以針對私人 VNet 上的部署指定 IP 範圍，以避免在對等互連後與其他公司 Vnet 發生衝突嗎？

在4.x 叢集中，您可以指定自己的 IP 範圍。

在3.11 叢集中，Azure Red Hat OpenShift 支援 VNet 對等互連，並可讓客戶提供對等互連的 vnet，以及 OpenShift 網路將在其中運作的 VNet CIDR。

Azure Red Hat OpenShift 所建立的 VNet 將受到保護，且不會接受設定變更。 對等互連的 VNet 由客戶控制，並位於其訂用帳戶中。

### <a name="is-the-software-defined-network-module-configurable"></a>軟體定義網路模組是否可設定？

軟體定義的網路是 `openshift-ovs-networkpolicy` 且無法設定。

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Azure Red Hat OpenShift 使用哪一個 Azure 負載平衡器？  它是標準或基本的，而且是否可設定？

Azure Red Hat OpenShift 使用標準 Azure Load Balancer，且無法設定。

## <a name="permissions"></a>權限

### <a name="can-an-admin-manage-users-and-quotas"></a>系統管理員可以管理使用者和配額嗎？

是。 Azure Red Hat OpenShift 系統管理員除了存取所有使用者建立的專案之外，還可以管理使用者和配額。

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>我可以將叢集限制為只有特定 Azure AD 的使用者嗎？

是。 您可以藉由設定 Azure AD 應用程式，來限制使用者可以登入叢集的 Azure AD。 如需詳細資訊，請參閱 [如何：將您的應用程式限制為一組使用者](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)。

### <a name="can-i-restrict-users-from-creating-projects"></a>我可以限制使用者建立專案嗎？

是。 以系統管理員身分登入您的叢集，然後執行此命令：

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

如需詳細資訊，請參閱 OpenShift 檔，以瞭解如何停用叢集版本的自我布建：

- [停用4.3 叢集中的自我布建](https://docs.openshift.com/aro/4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [停用3.11 叢集中的自我布建](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>IaaS) 中 (的 UNIX 許可權可用於 master/基礎結構/應用程式節點？

針對4.x 叢集，節點存取可透過叢集管理員角色取得。 如需詳細資訊，請參閱 [KUBERNETES RBAC 總覽](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html)。

針對3.11 叢集，禁止存取節點。

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>我們有哪些 OCP 權利？ 叢集-系統管理員？ 專案-系統管理員？

針對4.x 叢集，可以使用叢集管理員角色。 如需詳細資訊，請參閱 [KUBERNETES RBAC 總覽](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html)。

針對3.11 叢集，請參閱叢集 [管理總覽](https://docs.openshift.com/aro/admin_guide/index.html) 以取得詳細資料。

### <a name="which-identity-providers-are-available"></a>有哪些身分識別提供者可供使用？

針對4.x 叢集，您可以設定自己的身分識別提供者。 如需詳細資訊，請參閱關於設定身分 [識別 prodivers](https://docs.openshift.com/aro/4/authentication/identity_providers/configuring-ldap-identity-provider.html)的 Red Hat 檔。

針對3.11 叢集，您可以使用 Azure AD 整合。 

## <a name="storage"></a>儲存體

### <a name="is-data-on-my-cluster-encrypted"></a>我的叢集上的資料是否已加密？

依預設，資料會在靜止時加密。 Azure 儲存體平臺會在保存資料之前自動將資料加密，並在抓取之前解密資料。 如需詳細資訊，請參閱 [Azure 儲存體 rest 資料的服務加密](../storage/common/storage-service-encryption.md)。

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>資料是儲存在 Azure Red Hat OpenShift 上加密的 etcd 嗎？

針對 Azure Red Hat OpenShift 4 叢集，資料預設不會加密，但您可以選擇啟用加密。 如需詳細資訊，請參閱 [加密 etcd](https://docs.openshift.com/container-platform/4.3/authentication/encrypting-etcd.html)指南。

針對3.11 叢集，資料不會在 etcd 層級進行加密。 目前不支援開啟加密的選項。 OpenShift 支援這項功能，但需要工程工作才能將它置於藍圖上。 資料會在磁片層級進行加密。 如需詳細資訊，請參閱在資料存放區 [層加密資料](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) 。

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>我們可以選擇任何持續性儲存體解決方案，例如 OCS 嗎？ 

針對4.x 叢集，Azure 磁片 (Premium_LRS) 會設定為預設儲存類別。 如需其他存放裝置提供者，以及設定詳細資料 (包括 Azure 檔案) ，請參閱 [持續性儲存體](https://docs.openshift.com/aro/4/storage/understanding-persistent-storage.html)的 Red Hat 檔。

針對3.11 叢集，預設會提供兩個儲存體類別：一個適用于 Azure 磁片 (Premium_LRS) ，另一個用於 Azure 檔案。

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>ARO 是否將任何客戶資料儲存在叢集區域以外？

否。 在 ARO 叢集中建立的所有資料都會保留在叢集的區域內。
