---
title: Azure Red Hat OpenShift 的常見問題
description: 以下是 Microsoft Azure Red Hat OpenShift 的常見問題解答
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 92529c2d60b32e9c8b57b897008b5333adc2a4d4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594962"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift 常見問題

本文說明 Microsoft Azure Red Hat OpenShift 的常見問題（Faq）。

## <a name="which-azure-regions-are-supported"></a>支援哪些 Azure 區域？

如需支援 Azure Red Hat OpenShift 的全域區域清單，請參閱[支援的資源](supported-resources.md#azure-regions)。

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>我可以將叢集部署到現有的虛擬網路嗎？

不需要。 但是，您可以透過對等互連將 Azure Red Hat OpenShift 叢集連線到現有的虛擬網路。 如需詳細資訊，請參閱[將叢集的虛擬網路連線到現有的虛擬網路](tutorial-create-cluster.md#create-the-cluster)。

## <a name="what-cluster-operations-are-available"></a>有哪些叢集作業可供使用？

您只能相應增加或減少計算節點的數目。 建立之後，不允許對`Microsoft.ContainerService/openShiftManagedClusters`資源進行其他修改。 計算節點的最大數目限制為20。

## <a name="what-virtual-machine-sizes-can-i-use"></a>我可以使用哪些虛擬機器大小？

如需可與 Azure Red Hat OpenShift 叢集搭配使用的虛擬機器大小清單，請參閱[Azure Red Hat OpenShift 虛擬機器大小](supported-resources.md#virtual-machine-sizes)。

## <a name="is-data-on-my-cluster-encrypted"></a>我的叢集上的資料已加密嗎？

根據預設，待用加密。 Azure 儲存體平臺會在保存資料之前自動進行加密，並在抓取之前解密資料。 如需詳細資訊，請參閱待用[資料的 Azure 儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>我可以使用 Prometheus/Grafana 來監視我的應用程式嗎？

是，您可以在命名空間中部署 Prometheus，以及監視命名空間中的應用程式。

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>我可以使用 Prometheus/Grafana 來監視與叢集健康情況和容量相關的計量嗎？

否，目前時間不是。

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Docker 登錄是否可在外部使用，因此我可以使用 Jenkins 這類工具？

中`https://docker-registry.apps.<clustername>.<region>.azmosa.io/`提供 Docker 登錄，但未提供強大的儲存持久性保證。 您也可以使用[Azure Container Registry](https://azure.microsoft.com/services/container-registry/)。

## <a name="is-cross-namespace-networking-supported"></a>是否支援跨命名空間的網路？

客戶和個別專案管理員可以使用`NetworkPolicy`物件，針對每個專案自訂跨命名空間的網路功能（包括拒絕它）。

## <a name="can-an-admin-manage-users-and-quotas"></a>系統管理員可以管理使用者和配額嗎？

可以。 除了存取所有使用者建立的專案之外，Azure Red Hat OpenShift 系統管理員還可以管理使用者和配額。

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>我可以將叢集限制為只有特定 Azure AD 的使用者嗎？

可以。 您可以藉由設定 Azure AD 應用程式來限制哪些 Azure AD 使用者可以登入叢集。 如需詳細資訊，請參閱[如何：將您的應用程式限制為一組使用者](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>我可以限制使用者建立專案嗎？

可以。 以 Azure Red Hat OpenShift 系統管理員身分登入您的叢集，並執行此命令：

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

如需詳細資訊，請參閱停用[自我布建](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)的 OpenShift 檔。

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>叢集可以跨多個 Azure 區域擁有計算節點嗎？

不需要。 Azure Red Hat OpenShift 叢集中的所有節點都必須來自相同的 Azure 區域。

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>主要和基礎結構節點是否會與 Azure Kubernetes Service （AKS）一起抽象化？

不需要。 所有資源（包括叢集主機）都是在您的客戶訂用帳戶中執行。 這些類型的資源會放在唯讀的資源群組中。

## <a name="is-open-service-broker-for-azure-osba-supported"></a>是否支援 Open Service Broker for Azure （OSBA）？

可以。 您可以使用 OSBA 搭配 Azure Red Hat OpenShift。 如需詳細資訊，請參閱[Open Service Broker For Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) 。

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>我嘗試對等互連至不同訂用帳戶中的虛擬網路， `Failed to get vnet CIDR`但收到錯誤。

在具有虛擬網路的訂用帳戶中，請務必向`Microsoft.ContainerService`註冊提供者`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>什麼是 Azure Red Hat OpenShift （ARO）維護程式？

ARO 有三種類型的維護：升級、備份和還原 etcd 資料，以及雲端提供者起始的維護。

+ 升級包含軟體升級和 Cve。 CVE 補救會在啟動時執行`yum update` ，並提供立即緩和措施。  在平行中，將會建立新的映射組建，以供未來的叢集建立。

+ 備份和管理 etcd 資料是一個自動化程式，視動作而定，可能需要叢集停機。 如果正在從備份還原 etcd 資料庫，將會有停機時間。 我們會每小時備份 etcd，並保留過去6小時的備份。

+ 雲端提供者起始的維護包括網路、儲存體和區域性中斷。 維護取決於雲端提供者，並依賴提供者提供的更新。

## <a name="what-is-the-general-upgrade-process"></a>什麼是一般升級程式？

執行升級應該是安全的進程，而且不應該中斷叢集服務。 當有新版本可用或 Cve 未處理時，SRE 可以觸發升級程式。

可用的更新會在階段環境中進行測試，然後套用至生產叢集。 套用時，會暫時新增節點，並以旋轉方式更新節點，讓 pod 維持複本計數。 遵循最佳做法有助於確保最短的停機時間。

視擱置升級或更新的嚴重性而定，此程式可能會有不同之處，因為更新可能會快速套用，以減輕服務暴露于 CVE 的風險。 新的映射會以非同步方式建立、測試，並隨著叢集升級而推出。 另一方面，緊急和規劃的維護並沒有差別。 客戶不會 prescheduled 預定的維護。

如果需要與客戶進行通訊，則可以透過 ICM 和電子郵件傳送通知。

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>那麼緊急與預定的維護時段呢？

我們不會區分兩種類型的維護。 我們的團隊可以使用24/7/365，而且不會使用傳統排程的「非時數」維護時段。

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>如何更新主機的作業系統和 OpenShift 軟體？

主機作業系統和 OpenShift 軟體會透過我們的一般升級和映射建立程式來進行更新。

## <a name="whats-the-process-to-reboot-the-updated-node"></a>重新開機已更新節點的程式為何？

這應該在升級過程中處理。

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>儲存在 etcd 中的資料是以 ARO 加密嗎？

它不會在 etcd 層級上加密。 目前不支援將其開啟的選項。 OpenShift 支援這項功能，但必須在藍圖上進行工程工作。 資料會在磁片層級進行加密。 如需詳細資訊，請參閱在資料存放區[層加密資料](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)。

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>可以將基礎 Vm 的記錄串流處理到客戶記錄分析系統嗎？

Syslog、docker logs、日記和 dmesg 是由受控服務處理，不會向客戶公開。

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>客戶如何取得節點層級的計量（例如 CPU/記憶體），以採取動作來調整規模、進行調試問題等。我似乎不能在`kubectl top` ARO 叢集上執行。

客戶可以使用命令`oc adm top nodes`或`kubectl top nodes`與客戶系統管理員 clusterrole，存取節點層級的 CPU/記憶體計量。  客戶也可以`pods`使用命令`oc adm top pods`來存取的 CPU/記憶體計量，或`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>ARO 的預設 pod 排程器設定為何？

ARO 會使用 OpenShift 中隨附的預設排程器。 ARO 不支援幾個額外的機制。 如需詳細資訊，請參閱預設排程器[檔](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler)和主要排程器[檔](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v16/data/master/etc/origin/master/scheduler.json)。

目前不支援先進/自訂排程。 如需詳細資訊，請參閱[排程檔](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)。

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>如果我們相應增加部署，Azure 容錯網域如何對應到 pod 位置，以確保服務的所有 pod 不會因單一容錯網域中的失敗而被破壞？

在 Azure 中使用虛擬機器擴展集時，預設會有五個容錯網域。 擴展集中的每個虛擬機器實例將會放入其中一個容錯網域。 這可確保部署至叢集中計算節點的應用程式會放在不同的容錯網域中。

如需詳細資訊，請參閱為[虛擬機器擴展集選擇正確的容錯網域數目](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)。

## <a name="is-there-a-way-to-manage-pod-placement"></a>是否有管理 pod 位置的方法？

客戶可以取得節點，並以客戶系統管理員的身分來查看標籤。 這會提供將目標設為擴展集中任何 VM 的方式。

使用特定標籤時，必須小心使用：

- 不得使用主機名稱。 主機名稱經常會隨著升級和更新而旋轉，並保證會變更。

- 如果客戶有特定標籤或部署策略的要求，就可以完成這項作業，但需要進行工程工作，而且目前不受支援。

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>ARO 叢集中的 pod 數目上限為何？在 ARO 中，每個節點的 pod 數目上限為何？

 Azure Red Hat OpenShift 3.11 具有每個節點的 50-pod 限制，其中的[aro 具有20個計算節點的限制](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)，因此在 aro 叢集中支援的 pod 數目上限為 50 * 20 = 1000。

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>我們可以在私人 VNET 上指定部署的 IP 範圍，避免在對等互連後與其他公司 Vnet 發生衝突嗎？

Azure Red Hat OpenShift 支援 VNET 對等互連，並可讓客戶使用和 VNET CIDR （OpenShift 網路將在其中操作）來提供 VNET 對等互連。

由 ARO 建立的 VNET 會受到保護，而且不會接受設定變更。 對等互連的 VNET 由客戶控制，並位於其訂用帳戶中。

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>叢集是否位於客戶訂用帳戶中？ 

Azure 受控應用程式存在於已鎖定的資源群組中，且具有客戶訂用帳戶。 客戶可以查看該 RG 中的物件，但不能修改。

## <a name="is-the-sdn-module-configurable"></a>SDN 模組是否可設定？

SDN 是 openshift-ovs-es-networkpolicy，而且無法設定。

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>主要/基礎/應用程式節點可以使用哪些 UNIX 許可權（在 IaaS 中）？

不適用於此供應專案。 禁止存取節點。

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>我們有哪些 OCP 權利？ 叢集-系統管理？ 專案-系統管理？

如需詳細資訊，請參閱 Azure Red Hat OpenShift 叢集[管理總覽](https://docs.openshift.com/aro/admin_guide/index.html)。

## <a name="which-kind-of-federation-with-ldap"></a>LDAP 有何種同盟？

這可以透過 Azure AD 整合來達成。 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>ARO 中是否有任何元素與其他客戶共用？ 或所有專案都是獨立的嗎？

每個 Azure Red Hat OpenShift 叢集專門用於指定的客戶，並在客戶的訂用帳戶內。 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>我們可以選擇任何持續性儲存體解決方案，例如 OCS 嗎？ 

有兩個儲存類別可供選取： Azure 磁片和 Azure 檔案。

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>叢集如何更新（包括因弱點而主修和未成年人）？

請參閱[什麼是一般升級程式？](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>ARO 使用哪個 Azure 負載平衡器？是否為標準或基本，而且是否可設定？

ARO 使用標準 Azure Load Balancer，而且無法設定。

## <a name="can-aro-use-netapp-based-storage"></a>ARO 可以使用以 NetApp 為基礎的存放裝置嗎？

目前唯一支援的儲存體選項是 Azure 磁片和 Azure 檔案儲存體類別。 


