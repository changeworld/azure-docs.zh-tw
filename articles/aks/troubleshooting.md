---
title: 針對常見的 Azure Kubernetes Service 問題進行疑難排解
description: 了解在使用 Azure Kubernetes Service (AKS) 時，如何針對常見問題進行疑難排解並加以解決
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 855e5e5e23371f600a7e73139f2e6da1eebc91d0
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068824"
---
# <a name="aks-troubleshooting"></a>AKS 疑難排解

當您建立或管理 Azure Kubernetes Service (AKS) 叢集時，偶爾可能會遇到問題。 本文將詳細說明一些常見問題與疑難排解步驟。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>我通常可以在哪裡找到針對 Kubernetes 進行偵錯問題的相關資訊？

嘗試[針對 Kubernetes 叢集進行疑難排解的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
此外，也有 Microsoft 工程師所發佈的[疑難排解指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，該指南可用來針對 Pod、節點、叢集和其他功能進行疑難排解。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>我在建立或升級期間收到「已超出配額」錯誤。 我該怎麼辦？ 

 [要求更多核心](../azure-portal/supportability/resource-manager-core-quotas-request.md) \(部分機器翻譯\)。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>對於 AKS，每個節點的最大 Pod 數設定為何？

如果您在 Azure 入口網站中部署 AKS 叢集，則每個節點的最大 Pod 數設定會預設為 30。
如果您在 Azure CLI 中部署 AKS 叢集，則每個節點的最大 Pod 數設定會預設為 110。 (確定您使用最新版的 Azure CLI)。 在 `az aks create` 命令中使用 `–-max-pods` 旗標，即可變更此設定。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>當我使用進階網路設定部署 AKS 叢集時，收到 insufficientSubnetSize 錯誤。 我該怎麼辦？

此錯誤表示叢集中使用的子網不再有可在其 CIDR 中使用的 Ip，以進行成功的資源指派。 針對 Kubenet 叢集，叢集內的每個節點都需要有足夠的 IP 空間。 針對 Azure CNI 叢集，叢集內的每個節點和 pod 都需要有足夠的 IP 空間。
深入瞭解 [AZURE CNI 的設計，以將 ip 指派給](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)pod。

這些錯誤也會出現在 [AKS 診斷](./concepts-diagnostics.md) 中，以主動呈現子網大小不足的問題。

下列三 (3) 案例會導致子網大小不足的錯誤：

1. AKS Scale 或 AKS Nodepool scale
   1. 如果使用 Kubenet，當小於時，就會發生這種情況 `number of free IPs in the subnet` **less than** `number of new nodes requested` 。
   1. 如果使用 Azure CNI，當小於時，就會發生這種情況 `number of free IPs in the subnet` **less than** `number of nodes requested times (*) the node pool's --max-pod value` 。

1. AKS Upgrade 或 AKS Nodepool upgrade
   1. 如果使用 Kubenet，當小於時，就會發生這種情況 `number of free IPs in the subnet` **less than** `number of buffer nodes needed to upgrade` 。
   1. 如果使用 Azure CNI，當小於時，就會發生這種情況 `number of free IPs in the subnet` **less than** `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` 。
   
   根據預設，AKS 叢集會將最大的 (升級緩衝區) 值設定為一個 (1) ，但您可以藉由設定 [節點集區的最大激增值](upgrade-cluster.md#customize-node-surge-upgrade-preview) 來自訂此升級行為，這會增加完成升級所需的可用 ip 數目。

1. AKS create 或 AKS Nodepool add
   1. 如果使用 Kubenet，當小於時，就會發生這種情況 `number of free IPs in the subnet` **less than** `number of nodes requested for the node pool` 。
   1. 如果使用 Azure CNI，當小於時，就會發生這種情況 `number of free IPs in the subnet` **less than** `number of nodes requested times (*) the node pool's --max-pod value` 。

建立新的子網可以採取下列緩和措施。 因為無法更新現有子網的 CIDR 範圍，所以需要建立新子網的許可權。

1. 以較大的 CIDR 範圍重建新的子網，以滿足操作目標：
   1. 使用新的所需非重迭範圍來建立新的子網。
   1. 在新的子網上建立新的 nodepool。
   1. 從位於舊子網的舊 nodepool 中清空 pod，以取代。
   1. 刪除舊的子網和舊的 nodepool。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 會在 CrashLoopBackOff 模式中停滯。 我該怎麼辦？

有各種不同的原因可能會讓 Pod 在該模式中停滯。 您可以查看：

* Pod 本身 (使用 `kubectl describe pod <pod-name>`)。
* 記錄 (使用`kubectl logs <pod-name>`)。

如需有關如何針對 Pod 問題進行疑難排解的詳細資訊，請參閱[偵錯應用程式](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>我 `TCP timeouts` 在使用 `kubectl` 或其他連接至 API 伺服器的協力廠商工具時收到
AKS 具有 HA 控制平面，可根據核心數目垂直調整，以確保其服務等級目標 (Slo) 和服務等級協定 (Sla) 。 如果您遇到連線超時，請檢查下列內容：

- **您的所有 API 命令都是以一致或只是幾個方式來進行？** 如果只有少數，您的 `tunnelfront` pod 或 `aks-link` pod （負責節點 > 的控制平面通訊）可能不是處於執行中狀態。 請確定裝載此 pod 的節點未過度使用或在壓力下。 請考慮將它們移至自己的[ `system` 節點集](use-system-pools.md)區。
- **您是否已開啟 [AKS 限制輸出流量](limit-egress-traffic.md)檔上所述的所有必要端口、Fqdn 和 ip？** 否則數個命令呼叫可能會失敗。
- **您目前的 IP 是否包含在 [API Ip 授權範圍內](api-server-authorized-ip-ranges.md)？** 如果您使用這項功能，而您的 IP 未包含在範圍內，則您的呼叫將會遭到封鎖。 
- **您是否有用戶端或應用程式正在洩漏對 API 伺服器的呼叫？** 請務必使用監看式，而不是頻繁的 get 呼叫，且您的協力廠商應用程式未洩漏這類呼叫。 例如，Istio 混音器中的錯誤會導致每次在內部讀取秘密時，都會建立新的 API 伺服器監看連接。 由於這種行為會定期發生，因此，監看連線會快速累積，而且最終會導致 API 伺服器因為調整模式而超載。 https://github.com/istio/istio/issues/19481
- **您的 helm 部署中有多個版本嗎？** 這種情況可能會導致這兩個 tiller 在節點上使用太多記憶體，以及大量的 `configmaps` ，這可能會導致 API 伺服器上不必要的尖峰。 請考慮 `--history-max` 在中設定 `helm init` ，並利用新的 Helm 3。 下列問題的詳細資料： 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[節點之間的內部流量是否遭到封鎖？](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>我收到 `TCP timeouts` ，例如 `dial tcp <Node_IP>:10250: i/o timeout`

這些超時可能與節點封鎖之間的內部流量有關。 確認未封鎖此流量，例如，您叢集節點的子網上的 [網路安全性群組](concepts-security.md#azure-network-security-groups) 。

## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>我正嘗試在現有叢集上啟用角色型存取控制 (RBAC)。 如何執行該作業？

目前不支援在現有叢集上啟用角色型存取控制 (RBAC)，其必須在建立新叢集時加以設定。 使用 CLI、入口網站或高於 `2020-03-01` 的 API 版本時，預設會啟用 RBAC。

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>我建立了已啟用 RBAC 的叢集，而現在我在 Kubernetes 儀表板上看到許多警告。 該儀表板一直可正常運作，而且未產生任何警告。 我該怎麼辦？

警告的原因是叢集已啟用 RBAC，而對儀表板的存取現在預設會受到限制。 這種方法通常是良好的做法，因為預設向叢集的所有使用者公開該儀表板可能會導致安全性威脅。 如果您仍然想要啟用該儀表板，請依照[這篇部落格文章](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) \(英文\) 中的步驟執行。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>我無法使用 kubectl 記錄取得記錄，或無法連線到 API 伺服器。 我收到「伺服器發生錯誤: 撥接後端時發生錯誤: 撥接 tcp...」。 我該怎麼辦？

請確定已開啟連接埠 22、9000 和 1194，以連線至 API 伺服器。 使用 `kubectl get pods --namespace kube-system` 命令，檢查 `tunnelfront` 或 `aks-link` Pod 是否正在 *kube-system* 命名空間中執行。 若非如此，請強制刪除該 Pod，而它會重新啟動。

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>當我連線 `"tls: client offered only unsupported versions"` 到 AKS API 時，我是從我的用戶端取得。 我該怎麼辦？

AKS 中支援的最低 TLS 版本是 TLS 1.2。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我正嘗試升級或調整，卻收到 `"Changing property 'imageReference' is not allowed"` 錯誤。 如何修正此問題？

您可能會因為修改了 AKS 叢集內代理程式節點中的標記而收到此錯誤。 修改或刪除 MC_* 資源群組中資源的標記與其他屬性，可能會導致非預期的結果。 在 AKS 叢集中更改 MC_* 群組下的資源，即會中斷服務等級目標 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>我收到錯誤，指出我的叢集處於失敗狀態，必須等到修正之後，才能升級或調整規模

您可以從 https://aka.ms/aks-cluster-failed 獲得這個疑難排解協助

當叢集因多種原因而進入失敗狀態時，就會發生此錯誤。 重試先前失敗的作業之前，請先遵循下列步驟來解決叢集失敗狀態：

1. 在叢集離開 `failed` 狀態之前，`upgrade` 和 `scale` 作業都將失敗。 常見的根本問題與解決方式包括：
    * 進行調整時**計算 (CRP) 配額不足**。 若要解決此問題，先將叢集調整回配額內穩定的目標狀態。 然後，遵循這些[步驟來要求提高計算配額](../azure-portal/supportability/resource-manager-core-quotas-request.md)，然後再次嘗試擴大以超過初始配額限制。
    * 使用進階網路和**不足的子網路 (網路) 資源**來調整叢集。 若要解決此問題，先將叢集調整回配額內穩定的目標狀態。 然後，遵循[這些步驟來要求提高資源配額](../azure-resource-manager/templates/error-resource-quota.md#solution)，然後再次嘗試擴大以超過初始配額限制。
2. 解決升級失敗的根本原因之後，您的叢集應該會處於成功狀態。 確認成功狀態之後，請重試原始作業。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>我在嘗試升級或調整規模時收到錯誤，指出我的叢集正在升級或升級失敗

您可以從 https://aka.ms/aks-pending-upgrade 獲得這個疑難排解協助

 您不能同時升級和調整叢集或節點集區。 而是每個作業類型都必須在目標資源上完成，然後才能在該相同資源上進行下一個要求。 因此，在進行或嘗試作用中的升級或調整作業時，作業會受到限制。 

若要協助診斷此問題，請執行 `az aks show -g myResourceGroup -n myAKSCluster -o table` 以擷取叢集上的詳細狀態。 根據結果：

* 如果叢集正在進行升級，請等候作業完成。 如果成功，再次重試先前失敗的作業。
* 如果叢集升級失敗，則遵循上一節所述的步驟。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>我是否可以將叢集移至不同的訂用帳戶，或將含有叢集的訂用帳戶新增至新的租用戶？

如果您已將 AKS 叢集移至不同的訂用帳戶，或將叢集的訂用帳戶移至新的租用戶，該叢集將因缺少叢集身分識別權限而無法運作。 由於這個限制式，因此 **AKS 不支援在訂用帳戶或租用戶之間移動叢集**。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>我在嘗試使用需要虛擬機器擴展集的功能時收到錯誤

您可以從 aka.ms/aks-vmss-enablement 獲得這個疑難排解協助

您可能會收到錯誤，指出您的 AKS 叢集不在虛擬機器擴展集上，如下列範例所示：

**AgentPool `<agentpoolname>` 已將自動調整規模設定為啟用，但其不在虛擬機器擴展集上**

叢集自動調整程式或多個節點集區之類的功能都需要虛擬機器擴展集作為 `vm-set-type`。

遵循適當文件中的「在您開始之前」步驟，正確建立 AKS 叢集：

* [使用叢集自動調整程式](cluster-autoscaler.md)
* [建立並使用多個節點集區](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS 資源和參數會強制執行哪些命名限制？

您可以從 aka.ms/aks-naming-rules 獲得這個疑難排解協助

命名限制會透過 Azure 平台和 AKS 來實作。 如果資源名稱或參數違反這其中一個限制，即會傳回錯誤，要求您提供不同的輸入。 下列一般命名指導方針適用：

* 叢集名稱必須是 1-63 個字元。 唯一允許的字元是字母、數字、破折號和底線。 第一個和最後一個字元必須是字母或數字。
* AKS 節點/*MC_* 資源群組名稱會合併資源群組名稱與資源名稱。 自動產生的 `MC_resourceGroupName_resourceName_AzureRegion` 語法不得超過 80 個字元。 視需要縮短資源群組名稱或 AKS 叢集名稱的長度。 您也可以[自訂節點資源群組名稱](cluster-configuration.md#custom-resource-group-name)。
* *dnsPrefix* 必須以英數字元值開頭和結束，且必須介於 1-54 個字元之間。 有效字元包含英數字元值和連字號 (-)。 *dnsPrefix* 不能包含特殊字元，例如句號 (.)。
* 針對 Linux 節點集區，AKS 節點集區名稱必須全部小寫且為 1-11 個字元，針對 Windows 節點集區則是 1-6 個字元。 名稱的開頭必須是字母，而唯一允許的字元為字母與數字。
* 系統管理員使用者 *名稱*（設定 Linux 節點的系統管理員使用者名稱）必須以字母開頭，且只可包含字母、數位、連字號和底線，且長度上限為64個字元。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>我在嘗試建立、更新、調整、刪除或升級叢集時收到錯誤，指出因為另一個作業正在進行，所以不允許該作業。

您可以從 aka.ms/aks-pending-operation 獲得這個疑難排解協助

若先前的作業仍在進行，叢集作業就會受到限制。 若要擷取叢集的詳細狀態，請使用 `az aks show -g myResourceGroup -n myAKSCluster -o table` 命令。 視需要使用您自己的資源群組和 AKS 叢集名稱。

根據叢集狀態的輸出：

* 如果叢集處於「成功」或「失敗」以外的任何佈建狀態，請等候作業 (升級 / 更新 / 建立 / 調整 / 刪除 / 移轉) 完成。 當先前的作業完成時，重試最後一個叢集作業。

* 如果叢集升級失敗，請遵循概述[我收到錯誤，指出我的叢集處於失敗狀態，必須等到修正之後，才能升級或調整規模](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)的步驟。

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>當我嘗試建立新叢集時收到錯誤，指出找不到我的服務主體或其無效。

建立 AKS 叢集時，其需要服務主體或受控識別，以代表您建立資源。 AKS 可以在建立叢集時自動建立新的服務主體，或接收現有的服務主體。 使用自動建立的服務主體時，Azure Active Directory 需要將其傳播到每個區域，如此便能成功建立。 若傳播時間過長，叢集將無法建立驗證，因為找不到可用的服務主體來執行此動作。 

針對此問題使用下列因應措施：
* 使用現有的服務主體，其已經在區域之間傳播且存在，可在叢集建立時傳入到 AKS。
* 如果使用自動化指令碼，請在建立服務主體與建立 AKS 叢集之間新增時間延遲。
* 如果使用 Azure 入口網站，需在建立期間返回叢集設定，並在幾分鐘之後重試驗證頁面。

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>我 `"AADSTS7000215: Invalid client secret is provided."` 在使用 AKS API 時遇到。 我該怎麼辦？

這通常是因為服務主體認證過期所致。 [更新 AKS 叢集的認證。](update-credentials.md)

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>我在限制連出流量之後收到錯誤

限制來自 AKS 叢集的連出流量時，有[必要和選用的建議](limit-egress-traffic.md)輸出連接埠 / 網路規則，以及適用於 AKS 的 FQDN / 應用程式規則。 如果您的設定與上述任何規則發生衝突，某些 `kubectl` 命令將無法正確運作。 您可能也會在建立 AKS 叢集時看到錯誤。

確認您的設定不會與任何必要或選用的建議輸出連接埠 / 網路規則和 FQDN / 應用程式規則相衝突。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure 儲存體和 AKS 疑難排解

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>哪些是建議針對 Azure 磁碟使用的 Kubernetes 穩定版本？ 

| Kubernetes 版本 | 建議的版本 |
|--|:--:|
| 1.12 | 1.12.9 或更新版本 |
| 1.13 | 1.13.6 或更新版本 |
| 1.14 | 1.14.2 或更新版本 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>針對 Azure 磁碟的 WaitForAttach 失敗: 剖析 "/dev/disk/azure/scsi1/lun1": 無效的語法

在 Kubernetes 1.10 版中，MountVolume.WaitForAttach 可能會因為 Azure 磁碟重新掛接而失敗。

在 Linux 上，您可能會看到不正確的 DevicePath 格式錯誤。 例如：

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

在 Windows 上，您可能會看到錯誤的 DevicePath(LUN) 數字錯誤。 例如：

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

下列 Kubernetes 版本已修正此問題：

| Kubernetes 版本 | 已修正的版本 |
|--|:--:|
| 1.10 | 1.10.2 或更新版本 |
| 1.11 | 1.11.0 或更新版本 |
| 1.12 及更新版本 | N/A |


### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>在適用於 Azure 磁碟的 mountOptions 中設定 uid 和 gid 時失敗

Azure 磁碟預設會使用 ext4,xfs 檔案系統，而 mountOptions (例如 uid=x,gid=x) 無法在掛接時設定。 例如，如果您嘗試設定 mountOptions uid=999,gid=999，就會看到類似下列的錯誤：

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

您可以藉由執行下列其中一個選項來減輕此問題：

* 藉由在 runAsUser 中設定 uid 並在 fsGroup 中設定 gid 來[設定 Pod 的資訊安全內容](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) \(英文\)。 例如，下列設定將以根目錄執行 Pod，使其可供任意檔案存取：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > 因為 gid 和 uid 預設會以根目錄或 0 形式掛接。 如果 gid 或 uid 並未設定為根目錄，例如 1000，則 Kubernetes 將使用 `chown` 來變更該磁碟底下的所有目錄和檔案。 此作業可能非常耗時，而且可能會讓磁碟的載入速度變慢。

* 使用 initContainers 中的 `chown` 來設定 gid 和 uid。 例如：

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure 磁碟中斷連結失敗，會導致潛在的競爭條件問題與無效的資料磁碟清單

若 Azure 磁碟無法中斷連結，其最多將重試六次，使用指數倒回的方式來中斷連接磁碟。 其也將在資料磁碟清單上保留大約 3 分鐘的節點層級鎖定。 如果在這段時間內手動更新磁碟清單，則其將導致節點層級鎖定所持有的磁碟清單過時，並導致節點不穩定。

下列 Kubernetes 版本已修正此問題：

| Kubernetes 版本 | 已修正的版本 |
|--|:--:|
| 1.12 | 1.12.9 或更新版本 |
| 1.13 | 1.13.6 或更新版本 |
| 1.14 | 1.14.2 或更新版本 |
| 1.15 及更新版本 | N/A |

如果您使用的 Kubernetes 版本沒有此問題的修正程式，而且您的節點具有過時的磁碟清單，則您能以大量作業形式從 VM 中斷連結所有不存在的磁碟來減輕。 **個別中斷連結不存在的磁碟可能會失敗。**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>大量 Azure 磁碟導致連結/中斷連結變慢

以單一節點 VM 為目標的 Azure 磁碟連結/中斷連結作業數目大於 10，或在以單一虛擬機器擴展集集區為目標時大於 3，則其速度可能比預期還慢，因其會依序完成。 此問題是已知的限制，目前沒有任何因應措施。 [使用者語音項目支援超過某個數字的平行連結/中斷連結](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for)。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure 磁碟中斷連結失敗，會導致潛在的節點 VM 處於失敗狀態

在某些邊緣案例中，Azure 磁碟中斷連結可能部分失敗，並讓節點 VM 處於失敗狀態。

下列 Kubernetes 版本已修正此問題：

| Kubernetes 版本 | 已修正的版本 |
|--|:--:|
| 1.12 | 1.12.10 或更新版本 |
| 1.13 | 1.13.8 或更新版本 |
| 1.14 | 1.14.4 或更新版本 |
| 1.15 及更新版本 | N/A |

如果您使用的 Kubernetes 版本沒有此問題的修正程式，而且您的節點處於失敗狀態，則您可以使用下列其中一種方式，手動更新 VM 狀態來減輕：

* 針對以可用性設定組為基礎的叢集：
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* 針對以 VMSS 為基礎的叢集：
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure 檔案儲存體和 AKS 疑難排解

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>哪些是建議針對 Azure 檔案使用的 Kubernetes 穩定版本？
 
| Kubernetes 版本 | 建議的版本 |
|--|:--:|
| 1.12 | 1.12.6 或更新版本 |
| 1.13 | 1.13.4 或更新版本 |
| 1.14 | 1.14.0 或更新版本 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>使用 Azure 檔案儲存體時，預設的 mountOptions 為何？

建議的設定：

| Kubernetes 版本 | fileMode 和 dirMode 值|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 及更新版本 | 0777 |

您可以在儲存類別物件上指定掛接選項。 下列範例會設定 0777：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

一些其他有用的 *mountOptions* 設定：

* *mfsymlinks* 將讓 Azure 檔案儲存體掛接 (cifs) 支援符號連結
* *nobrl* 會防止將位元組範圍鎖定要求傳送至伺服器。 對於以 cifs 樣式強制位元組範圍鎖定中斷的特定應用程式而言，這是必要的設定。 大部分的 cifs 伺服器尚未支援要求諮詢位元組範圍鎖定。 如果未使用 *nobrl*，以 cifs 樣式強制位元組範圍鎖定中斷的應用程式可能會導致類似下列的錯誤訊息：
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>使用 Azure 檔案儲存體時發生「無法變更權限」的錯誤

在 Azure 檔案儲存體外掛程式上執行 PostgreSQL 時，您可能會看到類似下列的錯誤：

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

此錯誤是由使用 cifs/SMB 通訊協定的 Azure 檔案儲存體外掛程式所造成。 使用 cifs/SMB 通訊協定時，無法在掛接之後變更檔案和目錄權限。

若要解決此問題，請搭配 Azure 磁碟外掛程式使用 *subPath*。 

> [!NOTE] 
> 針對 ext3/4 磁碟類型，在將磁碟格式化之後，即會有 lost+found 目錄。

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>處理許多小型檔案時，相較於 Azure 磁碟，Azure 檔案儲存體具有高延遲

在某些情況下 (例如處理許多小型檔案)，相較於 Azure 磁碟，使用 Azure 檔案儲存體時，您可能會遇到高延遲的情況。

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>在儲存體帳戶上啟用「允許從選取的網路存取」設定時發生錯誤

如果您在 AKS 中用來動態佈建的儲存體帳戶上啟用「允許從選取的網路存取」，則您將在 AKS 建立檔案共用時收到錯誤：

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

此錯誤是因為在設定「允許從選取的網路存取」時，Kubernetes *persistentvolume-controller* 不在選擇的網路上。

您可以使用[靜態佈建搭配 Azure 檔案儲存體](azure-files-volume.md)來減輕此問題。

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure 檔案儲存體無法在 Windows Pod 中重新掛接

如果刪除了具有 Azure 檔案儲存體掛接的 Windows Pod，然後排程在相同節點上重新建立，則掛接將會失敗。 發生此失敗的原因是 `New-SmbGlobalMapping` 命令會因為 Azure 檔案儲存體掛接已經掛接於節點上而失敗。

例如，您可能會看到類似下列的錯誤：

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

下列 Kubernetes 版本已修正此問題：

| Kubernetes 版本 | 已修正的版本 |
|--|:--:|
| 1.12 | 1.12.6 或更新版本 |
| 1.13 | 1.13.4 或更新版本 |
| 1.14 及更新版本 | N/A |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Azure 檔案儲存體掛接因為儲存體帳戶金鑰已變更而失敗

如果您的儲存體帳戶金鑰已變更，您可能就會看到 Azure 檔案儲存體掛接失敗。

您可以使用 base64 編碼的儲存體帳戶金鑰，以手動方式在 Azure 檔案祕密中手動更新 `azurestorageaccountkey` 欄位來減輕。

若要以 base64 來為您的儲存體帳戶金鑰進行編碼，您可以使用 `base64`。 例如：

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

若要更新您的 Azure 祕密檔案，請使用 `kubectl edit secret`。 例如：

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

幾分鐘後，代理程式節點將會以更新的儲存體金鑰重試 Azure 檔案儲存體掛接。


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>叢集自動調整程式無法調整，且發生無法修正節點群組大小的錯誤

如果您的叢集自動調整程式並未擴大/縮小，而且您在[叢集自動調整程式記錄][view-master-logs]上看到類似下列的錯誤。

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

此錯誤的發生原因是上游叢集自動調整程式競爭條件。 在這種情況下，叢集自動調整程式的結尾值會與實際在叢集中的值不同。 若要離開此狀態，請停用再重新啟用[叢集自動調整程式][cluster-autoscaler]。

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>磁碟連結變慢，GetAzureDiskLun 需要花費 10 到 15 分鐘的時間，而您會收到錯誤

在**早於 1.15.0** 的 Kubernetes 版本上，您可能會收到類似下列的錯誤：**錯誤 WaitForAttach 找不到磁碟的 Lun**。  此問題的因應措施是大約等候 15 分鐘，然後重試。

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md

### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>使用具有 kubernetes.io 前置詞的節點標籤時，Kubernetes 1.16 升級失敗的原因

從 Kubernetes [1.16](https://v1-16.docs.kubernetes.io/docs/setup/release/notes/)開始，kubelet 至節點 [只能套用 kubernetes.io 前置詞所定義的標籤子集](https://github.com/kubernetes/enhancements/blob/master/keps/sig-auth/0000-20170814-bounding-self-labeling-kubelets.md#proposal) 。 AKS 無法在未同意的情況下代表您移除使用中標籤，因為這可能會造成受影響工作負載的停機時間。

因此，若要減輕這個問題，您可以：

1. 將您的叢集控制平面升級為1.16 或更高版本
2. 在1.16 或更高版本上新增 nodepoool，而不支援 kubernetes.io 標籤
3. 刪除較舊的 nodepool

AKS 正在調查在 nodepool 上改變使用中標籤的功能，以改善這項緩和措施。
