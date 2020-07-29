---
title: Azure Red Hat OpenShift 的常見問題
description: 以下是 Microsoft Azure Red Hat OpenShift 的常見問題解答
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 0c4c5ddfebe9e2b5b37a2c28ec4941f6c38668f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84219226"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift 常見問題

本文會回答有關 Microsoft Azure Red Hat OpenShift 的常見問題（Faq）。

## <a name="installation-and-upgrade"></a>安裝與升級

### <a name="which-azure-regions-are-supported"></a>支援哪些 Azure 區域？

如需 Azure Red Hat OpenShift 4.x 支援的區域清單，請參閱可用的[區域](https://docs.openshift.com/aro/4/welcome/index.html#available-regions)。

如需 Azure Red Hat OpenShift 3.11 支援的區域清單，請參閱[依區域提供的產品](supported-resources.md#azure-regions)。

### <a name="what-virtual-machine-sizes-can-i-use"></a>我可以使用哪些虛擬機器大小？

如需 Azure Red Hat OpenShift 4 支援的虛擬機器大小清單，請參閱[Azure Red Hat OpenShift 4 支援的資源](support-policies-v4.md)。

如需 Azure Red Hat OpenShift 3.11 支援的虛擬機器大小清單，請參閱[Azure Red Hat OpenShift 3.11 支援的資源](supported-resources.md)。

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 叢集中的 pod 數目上限為何？  Azure Red Hat OpenShift 中每個節點的 pod 數目上限為何？

實際支援的 pod 數目取決於應用程式的記憶體、CPU 和儲存需求。

Azure Red Hat OpenShift 4.x 具有250個 pod 的每個節點限制和100個計算節點限制。 此上限為 250 100 = 25000 的叢集中支援的 pod 數目上限 &times; 。

Azure Red Hat OpenShift 3.11 具有50個每個節點的 pod 限制和20個計算節點限制。 此上限為 50 20 = 1000 的叢集中支援的 pod 數目上限 &times; 。

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>叢集可以跨多個 Azure 區域擁有計算節點嗎？

否。 Azure Red Hat OpenShift 叢集中的所有節點都必須源自相同的 Azure 區域。

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>是否可以跨多個可用性區域部署叢集？

是。 如果您的叢集部署到支援可用性區域的 Azure 區域，就會自動發生這種情況。 如需詳細資訊，請參閱[可用性區域](../availability-zones/az-overview.md#availability-zones)。

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>控制平面節點是否會與 Azure Kubernetes Service （AKS）一起抽象化？

否。 所有資源（包括叢集主要節點）都是在您的客戶訂用帳戶中執行。 這些類型的資源會放在唯讀的資源群組中。

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>叢集是否位於客戶訂用帳戶中？ 

Azure 受控應用程式存在於已鎖定的資源群組中，且具有客戶訂用帳戶。 客戶可以查看該資源群組中的物件，但不能加以修改。

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Azure Red Hat OpenShift 中是否有任何元素與其他客戶共用？ 或所有專案都是獨立的嗎？

每個 Azure Red Hat OpenShift 叢集專門用於指定的客戶，並在客戶的訂用帳戶內。 

### <a name="are-infrastructure-nodes-available"></a>是否有可用的基礎結構節點？

在 Azure Red Hat OpenShift 4.x 叢集上，基礎結構節點目前無法使用。

在 Azure Red Hat OpenShift 3.11 叢集上，預設會包含基礎結構節點。

## <a name="upgrades"></a>升級

###  <a name="what-is-the-general-upgrade-process"></a>什麼是一般升級程式？

修補程式會自動套用到您的叢集。 您不需要採取任何動作，即可在叢集上接收修補程式升級。

執行升級是安全的程式，而且不應該中斷叢集服務。 聯合 Microsoft Red Hat 小組可以在有新版本可用或常見的弱點和暴露未完成時，觸發升級程式。 可用的更新會在預備環境中進行測試，然後套用至生產叢集。 遵循最佳做法有助於確保最短的停機時間。

客戶不會 prescheduled 預定的維護。 與維護相關的通知可透過電子郵件傳送。

### <a name="what-is-the-azure-red-hat-openshift-maintenance-process"></a>什麼是 Azure Red Hat OpenShift 維護程式？

Azure Red Hat OpenShift 有兩種維護類型：升級和雲端提供者起始的維護。
- 升級包含軟體升級，以及常見的弱點和漏洞。
- 雲端提供者起始的維護包括網路、儲存體和區域性中斷。 維護取決於雲端提供者，並依賴提供者提供的更新。

### <a name="what-about-emergency-vs-planned-maintenance-windows"></a>那麼緊急與預定的維護時段呢？

我們不會區分兩種類型的維護。 我們的團隊可以使用24/7/365，而且不會使用傳統排程的「非時數」維護時段。

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>主機作業系統和 OpenShift 軟體會如何更新？

主機作業系統和 OpenShift 軟體會更新，因為 Azure Red Hat OpenShift 會使用來自上游 OpenShift 容器平臺的次要發行版本和修補程式。

### <a name="whats-the-process-to-reboot-the-updated-node"></a>重新開機已更新節點的程式為何？

節點會在升級過程中重新開機。

## <a name="cluster-operations"></a>叢集作業

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>我可以使用 Prometheus 來監視我的應用程式嗎？

Prometheus 已預先安裝，並已針對 Azure Red Hat OpenShift 4.x 叢集進行設定。 深入瞭解叢集[監視](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html)。

針對 Azure Red Hat OpenShift 3.11 叢集，您可以在命名空間中部署 Prometheus，並監視命名空間中的應用程式。 如需詳細資訊，請參閱[在 Azure Red Hat OpenShift 叢集中部署 Prometheus 實例](howto-deploy-prometheus.md)。

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>我可以使用 Prometheus 來監視與叢集健康情況和容量相關的計量嗎？

在 Azure Red Hat OpenShift 4.x 中： [是]。

在 Azure Red Hat OpenShift 3.11：否。

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>可以將基礎 Vm 的記錄串流處理到客戶記錄分析系統嗎？

來自基礎 Vm 的記錄會由受控服務處理，不會向客戶公開。

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>客戶如何取得節點層級的計量（例如 CPU/記憶體），以採取動作來調整規模、偵測問題等。 我似乎無法在 Azure Red Hat OpenShift 叢集上執行 kubectl top。

針對 Azure Red Hat OpenShift 4.x 叢集，OpenShift web 主控台包含節點層級的所有計量。 如需詳細資訊，請參閱關於查看叢集[資訊](https://docs.openshift.com/aro/4/web_console/using-dashboard-to-get-cluster-information.html)的 Red Hat 檔。

針對 Azure Red Hat OpenShift 3.11 叢集，客戶可以使用命令或搭配客戶管理叢集角色，存取節點層級的 CPU/記憶體計量 `oc adm top nodes` `kubectl top nodes` 。 客戶也可以使用命令或來存取的 CPU/記憶體計量 `pods` `oc adm top pods` `kubectl top pods` 。

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>如果我們相應增加部署，Azure 容錯網域如何對應到 pod 位置，以確保服務的所有 pod 不會因單一容錯網域中的失敗而被破壞？

在 Azure 中使用虛擬機器擴展集時，預設會有五個容錯網域。 擴展集中的每個虛擬機器實例將會放入其中一個容錯網域。 這可確保部署至叢集中計算節點的應用程式會放在不同的容錯網域中。

如需詳細資訊，請參閱[為虛擬機器擴展集選擇正確的容錯網域數目](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md)。

### <a name="is-there-a-way-to-manage-pod-placement"></a>是否有管理 pod 位置的方法？

客戶可以取得節點，並以客戶系統管理員的身分來查看標籤。這會提供將目標設為擴展集中任何 VM 的方式。

使用特定標籤時，必須小心使用：

- 不得使用主機名稱。 主機名稱經常會隨著升級和更新而旋轉，並保證會變更。
- 如果客戶有特定標籤或部署策略的要求，就可以完成這項作業，但需要進行工程工作，而且目前不受支援。

如需詳細資訊，請參閱[控制 pod 位置](https://docs.openshift.com/aro/4/nodes/scheduling/nodes-scheduler-about.html)。

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>映射登錄是否可供外部使用，因此我可以使用 Jenkins 這類工具？

針對4.x 叢集，您必須公開安全的登錄並設定驗證。 如需詳細資訊，請參閱下列 Red Hat 檔：

- [公開登錄](https://docs.openshift.com/aro/4/registry/securing-exposing-registry.html)
- [存取登錄](https://docs.openshift.com/aro/4/registry/accessing-the-registry.html)

針對3.11 叢集，可以使用 Docker 映射登錄。 Docker registry 可從取得 `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` 。 您也可以使用 Azure Container Registry。

## <a name="networking"></a>網路功能

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>我可以將叢集部署到現有的虛擬網路嗎？

在4.x 叢集中，您可以將叢集部署到現有的 VNet 中。

在3.11 叢集中，您無法將叢集部署到現有的 VNet 中。 您可以透過對等互連將 Azure Red Hat OpenShift 3.11 叢集連線到現有的 VNet。

### <a name="is-cross-namespace-networking-supported"></a>是否支援跨命名空間的網路？

客戶和個別專案管理員可以使用物件，針對每個專案自訂跨命名空間的網路功能（包括拒絕它） `NetworkPolicy` 。

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>我嘗試對等互連至不同訂用帳戶中的虛擬網路，但無法取得 VNet CIDR 錯誤。

在具有虛擬網路的訂用帳戶中，請務必 `Microsoft.ContainerService` 使用下列命令來註冊提供者：`az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>我們可以在私人 VNet 上指定部署的 IP 範圍，避免在對等互連後與其他公司 Vnet 發生衝突嗎？

在4.x 叢集中，您可以指定您自己的 IP 範圍。

在3.11 叢集中，Azure Red Hat OpenShift 支援 VNet 對等互連，並可讓客戶使用和 VNet CIDR （OpenShift 網路將在其中操作）來提供 VNet 對等互連。

Azure Red Hat OpenShift 所建立的 VNet 會受到保護，而且不會接受設定變更。 對等互連的 VNet 由客戶控制，並位於其訂用帳戶中。

### <a name="is-the-software-defined-network-module-configurable"></a>軟體定義網路模組是否可設定？

軟體定義的網路是 `openshift-ovs-networkpolicy` ，而且無法設定。

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Azure Red Hat OpenShift 所使用的 Azure 負載平衡器有哪些？  是否為標準或基本，而且是否可設定？

Azure Red Hat OpenShift 使用標準 Azure Load Balancer，而且無法設定。

## <a name="permissions"></a>權限

### <a name="can-an-admin-manage-users-and-quotas"></a>系統管理員可以管理使用者和配額嗎？

是。 除了存取所有使用者建立的專案之外，Azure Red Hat OpenShift 系統管理員還可以管理使用者和配額。

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>我可以將叢集限制為只有特定 Azure AD 的使用者嗎？

是。 您可以藉由設定 Azure AD 應用程式來限制哪些 Azure AD 使用者可以登入叢集。 如需詳細資訊，請參閱[如何：將您的應用程式限制為一組使用者](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)。

### <a name="can-i-restrict-users-from-creating-projects"></a>我可以限制使用者建立專案嗎？

是。 以系統管理員身分登入您的叢集，並執行此命令：

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

如需詳細資訊，請參閱停用叢集版本的自行布建的 OpenShift 檔：

- [停用4.3 叢集中的自我布建](https://docs.openshift.com/aro/4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [停用3.11 叢集中的自我布建](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>主要/基礎/應用程式節點可以使用哪些 UNIX 許可權（在 IaaS 中）？

針對4.x 叢集，可以透過叢集管理員角色來存取節點。 如需詳細資訊，請參閱[RBAC 總覽](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html)。

若為3.11 叢集，則禁止存取節點。

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>我們有哪些 OCP 權利？ 叢集-系統管理？ 專案-系統管理？

針對4.x 叢集，可以使用叢集系統管理員角色。 如需詳細資訊，請參閱[RBAC 總覽](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html)。

若為3.11 叢集，請參閱叢集[管理總覽](https://docs.openshift.com/aro/admin_guide/index.html)以取得詳細資料。

### <a name="which-identity-providers-are-available"></a>哪些身分識別提供者可供使用？

針對4.x 叢集，您可以設定自己的身分識別提供者。 如需詳細資訊，請參閱關於設定身分[識別 prodivers](https://docs.openshift.com/aro/4/authentication/identity_providers/configuring-ldap-identity-provider.html)的 Red Hat 檔。

針對3.11 叢集，您可以使用 Azure AD 整合。 

## <a name="storage"></a>儲存體

### <a name="is-data-on-my-cluster-encrypted"></a>我的叢集上的資料已加密嗎？

根據預設，資料會在靜止時加密。 Azure 儲存體平臺會在保存資料之前自動進行加密，並在抓取之前解密資料。 如需詳細資訊，請參閱待用[資料的 Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)。

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>儲存在 etcd 中的資料會在 Azure Red Hat OpenShift 上加密嗎？

針對 Azure Red Hat OpenShift 4 叢集，預設不會加密資料，但您可以選擇啟用加密。 如需詳細資訊，請參閱[加密 etcd](https://docs.openshift.com/container-platform/4.3/authentication/encrypting-etcd.html)指南。

若是3.11 叢集，資料在 etcd 層級上不會加密。 目前不支援開啟加密的選項。 OpenShift 支援這項功能，但必須在藍圖上進行工程工作。 資料會在磁片層級進行加密。 如需詳細資訊，請參閱在資料存放區[層加密資料](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)。

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>我們可以選擇任何持續性儲存體解決方案，例如 OCS 嗎？ 

針對4.x 叢集，Azure 磁片（Premium_LRS）會設定為預設儲存類別。 如需其他存放裝置提供者，以及設定詳細資料（包括 Azure 檔案），請參閱[持續性儲存體](https://docs.openshift.com/aro/4/storage/understanding-persistent-storage.html)上的 Red Hat 檔。

針對3.11 叢集，預設會提供兩個儲存體類別：一個用於 Azure 磁片（Premium_LRS），另一個用於 Azure 檔案。
