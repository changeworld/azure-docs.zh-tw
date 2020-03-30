---
title: Azure 紅帽開放移位常見問題
description: 以下是有關微軟 Azure 紅帽開放移位的常見問題解答
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619494"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure 紅帽開放移位常見問題解答

本文針對微軟 Azure 紅帽開放Shift的常見問題 （FAQ）。

## <a name="which-azure-regions-are-supported"></a>支援哪些 Azure 區域？

有關 Azure 紅帽開放Shift支援的全域區域清單，請參閱[支援的資源](supported-resources.md#azure-regions)。

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>是否可以將群集部署到現有虛擬網路？

否。 但是，您可以通過對等互連將 Azure 紅帽 OpenShift 群集連接到現有 VNET。 有關詳細資訊[，請參閱將群集的虛擬網路連接到現有虛擬網路](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)。

## <a name="what-cluster-operations-are-available"></a>哪些群集操作可用？

您只能向上或縮小計算節點的數量。 創建後不允許對`Microsoft.ContainerService/openShiftManagedClusters`資源進行其他修改。 計算節點的最大數量限制為 20。

## <a name="what-virtual-machine-sizes-can-i-use"></a>我可以使用哪些虛擬機器大小？

有關可與 Azure 紅帽 OpenShift 群集一起使用的虛擬機器大小清單，請參閱[Azure 紅帽 OpenShift 虛擬機器大小](supported-resources.md#virtual-machine-sizes)。

## <a name="is-data-on-my-cluster-encrypted"></a>群集上的資料是否加密？

預設情況下，靜態加密。 Azure 存儲平臺在保留資料之前會自動加密資料，並在檢索前解密資料。 有關詳細資訊[，請參閱 Azure 存儲服務加密以瞭解靜態資料](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>我可以使用普羅米圖斯/格拉法納來監視我的應用程式嗎？

可以，您可以在命名空間中部署 Prometheus，並在命名空間中監視應用程式。

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>我能否使用 Prometheus/Grafana 來監視與群集運行狀況和容量相關的指標？

不，不是目前時間。

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Docker 註冊表是否在外部可用，因此我可以使用 Jenkins 等工具？

Docker 註冊表可從`https://docker-registry.apps.<clustername>.<region>.azmosa.io/`但是，未提供強大的存儲持久性保證。 您還可以使用 Azure[容器註冊表](https://azure.microsoft.com/services/container-registry/)。

## <a name="is-cross-namespace-networking-supported"></a>是否支援跨命名空間網路？

客戶和單個專案管理員可以使用`NetworkPolicy`物件自訂跨命名空間網路（包括拒絕）。

## <a name="can-an-admin-manage-users-and-quotas"></a>管理員可以管理使用者和配額嗎？

是。 Azure 紅帽 OpenShift 管理員除了訪問所有使用者創建的專案外，還可以管理使用者和配額。

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>是否可以將群集限制為僅某些 Azure AD 使用者？

是。 您可以通過配置 Azure AD 應用程式來限制哪些 Azure AD 使用者可以登錄到群集。 有關詳細資訊，請參閱[：將應用限制為一組使用者](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>我可以限制使用者創建專案嗎？

是。 以 Azure 紅帽 OpenShift 管理員身份登錄到群集，並執行以下命令：

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

有關詳細資訊，請參閱有關[禁用自我預配的](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)OpenShift 文檔。

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>群集是否可以跨多個 Azure 區域具有計算節點？

否。 Azure 紅帽 OpenShift 群集中的所有節點必須源自同一 Azure 區域。

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>主節點和基礎結構節點的抽象性與 Azure Kubernetes 服務 （AKS） 一樣？

否。 所有資源（包括群集主資源）都在客戶訂閱中運行。 這些類型的資源被放入唯讀資源組中。

## <a name="is-open-service-broker-for-azure-osba-supported"></a>是否支援 Azure （OSBA） 的開放服務代理？

是。 您可以將 OSBA 與 Azure 紅帽開放Shift 一起使用。 有關詳細資訊[，請參閱打開服務代理。](https://github.com/Azure/open-service-broker-azure#openshift-project-template)

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>我嘗試在不同的訂閱中窺視到虛擬網路，但會出錯`Failed to get vnet CIDR`。

在具有虛擬網路的訂閱中，請確保向`Microsoft.ContainerService``az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>什麼是 Azure 紅帽開放移位 （ARO） 維護過程？

ARO 有三種類型的維護：蝕刻資料的升級、備份和恢復以及雲供應商啟動的維護。

+ 升級包括軟體升級和 CCV。 CVE 修正在啟動時通過`yum update`運行進行，並提供即時緩解。  並行將創建新的映射生成，以便將來創建群集。

+ 蝕刻資料的備份和管理是一個自動化過程，可能需要群集停機時間，具體取決於操作。 如果從備份還原蝕刻資料庫，將停機。 我們每小時備份蝕刻，並保留最後 6 小時的備份。

+ 雲供應商發起的維護包括網路、存儲和區域中斷。 維護依賴于雲供應商，並且依賴于供應商提供的更新。

## <a name="what-is-the-general-upgrade-process"></a>什麼是常規升級過程？

運行升級應該是一個安全運行過程，並且不應中斷叢集服務。 當新版本可用或 CCV 尚未完成時，SRE 可以觸發升級過程。

可用更新在階段環境中進行測試，然後應用於生產群集。 應用時，將臨時添加新節點，並旋轉方式更新節點，以便 pod 維護副本計數。 遵循最佳實踐有助於確保最短或無停機時間。

根據掛起的升級或更新的嚴重性，該過程可能有所不同，因為更新可能快速應用，以減輕服務對 CVE 的暴露。 新映射將作為群集升級非同步生成、測試和推出。 除了這一點，緊急和計畫維護之間沒有區別。 計畫中的維護不會與客戶預先計畫。

如果需要與客戶溝通，可以通過 ICM 和電子郵件發送通知。

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>緊急維修視窗與計畫維護視窗又如何？

我們不區分這兩種類型的維護。 我們的團隊全天候提供 365 服務，不使用傳統的"非工作時間"維護視窗。

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>主機作業系統和 OpenShift 軟體將如何更新？

主機作業系統和 OpenShift 軟體通過我們的一般升級和映射生成過程進行更新。

## <a name="whats-the-process-to-reboot-the-updated-node"></a>重新開機更新的節點的過程是什麼？

這應作為升級的一部分進行處理。

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>資料存儲在 ARO 上的蝕刻中是否加密？

它在蝕刻級別上未加密。 當前不支援打開它的選項。 OpenShift 支援此功能，但需要進行工程工作才能在路線圖上實現。 資料在磁片級別加密。 有關詳細資訊，請參閱在[資料存儲層加密資料](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)。

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>基礎 VM 的日誌是否可以資料流到客戶日誌分析系統？

Syslog、Docker 日誌、日誌和 dmesg 由託管服務處理，不會暴露給客戶。

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>客戶如何在節點級別訪問 CPU/記憶體等指標，以採取措施擴展、調試問題等。我似乎無法`kubectl top`在 ARO 群集上運行。

客戶可以使用命令`oc adm top nodes`或使用`kubectl top nodes`客戶管理員群集角色訪問節點級別的 CPU/記憶體指標。  客戶還可以`pods`使用 命令`oc adm top pods`或`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>ARO 的預設 pod 計畫程式配置是什麼？

ARO 使用在 OpenShift 中發貨的預設計畫程式。 ARO 中不支援幾個其他機制。 有關詳細資訊，請參閱[預設計畫程式文檔](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler)和[主計畫程式文檔](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)。

高級/自訂計畫當前不受支援。 有關詳細資訊，請參閱[計畫文檔](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)。

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>如果我們擴展部署，Azure 容錯域如何映射到窗格放置，以確保服務的所有 Pod 不會因單個容錯域中的故障而中斷？

預設情況下，在 Azure 中使用虛擬機器縮放集時有五個容錯域。 規模集中的每個虛擬機器實例都將放入這些容錯域之一。 這可確保部署到群集中的計算節點的應用程式將放置在單獨的容錯域中。

有關詳細資訊[，請參閱為虛擬機器規模集選擇正確數量的容錯域](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)。

## <a name="is-there-a-way-to-manage-pod-placement"></a>是否有管理吊艙放置的方法？

客戶能夠獲取節點和查看標籤作為客戶管理員。 這將提供一種以規模集中的任何 VM 為目標的方法。

使用特定標籤時必須小心：

- 不得使用主機名稱。 主機名稱經常通過升級和更新旋轉，並保證更改。

- 如果客戶請求特定標籤或部署策略，可以完成此任務，但需要工程工作，並且今天不支援。

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>ARO 群集中的最大窗格數是多少？ARO 中每個節點的最大窗格數是多少？

 Azure 紅帽 OpenShift 3.11 具有每個節點 50 個 pod 限制[，ARO 具有 20 個計算節點限制](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)，因此，將 ARO 群集中支援的最大 pod 數上限上限為 50*20 = 1000。

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>我們能否指定用於在專用 VNET 上部署的 IP 範圍，以避免一旦對等互連而與其他公司 VNET 發生衝突？

Azure 紅帽 OpenShift 支援 VNET 對等互連，並允許客戶提供 VNET 以對等和 VNET CIDR 進行 OpenShift 網路運行。

ARO 創建的 VNET 將受到保護，不會接受配置更改。 對等 VNET 由客戶控制並駐留在其訂閱中。

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>群集是否駐留在客戶訂閱中？ 

Azure 託管應用程式與客戶訂閱一起位於鎖定的資源組中。 客戶可以查看該 RG 中的物件，但不能修改。

## <a name="is-the-sdn-module-configurable"></a>SDN 模組是可配置的嗎？

SDN 是開放式移位-ovs 網路原則，不可配置。

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>哪些 UNIX 許可權（IaaS 中）可用於主節點/Infra/應用節點？

不適用於此產品/ 禁止節點訪問。

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>我們擁有哪些 OCP 權利？ 群集管理員？ 專案管理員？

有關詳細資訊，請參閱 Azure 紅帽 OpenShift[群集管理概述](https://docs.openshift.com/aro/admin_guide/index.html)。

## <a name="which-kind-of-federation-with-ldap"></a>與 LDAP 的聯盟類型？

這將通過 Azure AD 集成來實現。 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>ARO 中是否有任何元素與其他客戶共用？ 還是一切都是獨立的？

每個 Azure 紅帽 OpenShift 群集都專用於給定客戶，並生活在客戶的訂閱範圍內。 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>我們可以選擇任何持久性存儲解決方案，如 OCS 嗎？ 

可從以下兩個存儲類中選擇：Azure 磁片和 Azure 檔。

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>如何更新群集（包括因漏洞而成為主要和未成年人）？

請參閱[什麼是常規升級過程？](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>ARO 使用什麼 Azure 負載等化器？是標準還是基本，它是可配置的？

ARO 使用標準 Azure 負載等化器，並且不可配置。

## <a name="can-aro-use-netapp-based-storage"></a>ARO 可以使用基於 NetApp 的存儲嗎？

目前唯一支援的存儲選項是 Azure 磁片和 Azure 檔存儲類。 


