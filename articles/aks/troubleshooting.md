---
title: 針對常見的 Azure Kubernetes Service 問題進行疑難排解
description: 了解在使用 Azure Kubernetes Service (AKS) 時，如何針對常見問題進行疑難排解並加以解決
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368327"
---
# <a name="aks-troubleshooting"></a>AKS 疑難排解

當您建立或管理 Azure Kubernetes Service (AKS) 叢集時，可能偶爾會遇到問題。 本文將詳細說明一些常見問題與疑難排解步驟。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>我通常可以在哪裡找到針對 Kubernetes 進行偵錯問題的相關資訊？

嘗試[針對 Kubernetes 叢集進行疑難排解的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
此外，也有 Microsoft 工程師所發佈的[疑難排解指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，該指南可用來針對 Pod、節點、叢集和其他功能進行疑難排解。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>我在建立或升級期間收到「已超出配額」錯誤。 我該怎麼辦？ 

您需要[要求核心](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>對於 AKS，每個節點的最大 Pod 數設定為何？

如果您在 Azure 入口網站中部署 AKS 叢集，則每個節點的最大 Pod 數設定會預設為 30。
如果您在 Azure CLI 中部署 AKS 叢集，則每個節點的最大 Pod 數設定會預設為 110。 (確定您使用最新版的 Azure CLI)。 在 `–-max-pods` 命令中使用 `az aks create` 旗標，即可變更此預設設定。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>當我使用進階網路設定部署 AKS 叢集時，收到 insufficientSubnetSize 錯誤。 我該怎麼辦？

如果使用 Azure CNI （advanced 網路），AKS 會根據每個節點設定的「最大 pod」來配置 IP 位址。 根據每個節點設定的最大 pod 數，子網大小必須大於節點數目和每個節點的最大 pod 數設定的乘積。 下列方程式會概述這一點：

子網大小 > 叢集中的節點數目（考慮未來的調整需求） * 每個節點集的最大 pod 數。

如需詳細資訊，請參閱[為你的叢集規劃 IP 位址](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 會在 CrashLoopBackOff 模式中停滯。 我該怎麼辦？

有各種不同的原因可能會讓 Pod 在該模式中停滯。 您可以查看：

* Pod 本身 (使用 `kubectl describe pod <pod-name>`)。
* 記錄 (使用`kubectl logs <pod-name>`)。

如需有關如何針對 Pod 問題進行疑難排解的詳細資訊，請參閱[偵錯應用程式](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>我正嘗試在現有叢集上啟用 RBAC。 如何執行該作業？

很遺憾，目前不支援在現有叢集上啟用角色型存取控制 (RBAC)。 您必須明確地建立新的叢集。 如果您使用 CLI，預設會啟用 RBAC。 如果您使用 AKS 入口網站，可在建立工作流程中使用切換按鈕來啟用 RBAC。

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>我已使用 Azure CLI 搭配預設值或 Azure 入口網站來建立已啟用 RBAC 的叢集，但現在於 Kubernetes 儀表板上看到許多警告。 該儀表板一直可正常運作，而且未產生任何警告。 我該怎麼辦？

儀表板上警告的原因是因為根據預設，現在會透過 RBAC 啟用叢集並已停用對它的存取權。 這種方法通常是良好的做法，因為預設向叢集的所有使用者公開該儀表板可能會導致安全性威脅。 如果您仍然想要啟用該儀表板，請依照[這篇部落格文章](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) \(英文\) 中的步驟執行。

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>我無法連線到儀表板。 我該怎麼辦？

存取叢集外部服務的最簡單方法是執行 `kubectl proxy`，它會將傳送至 localhost 連接埠 8001 的要求 Proxy 到 Kubernetes API 伺服器。 API 伺服器可以從該處 Proxy 到您的服務：`http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`。

如果您沒有看到 Kubernetes 儀表板，請檢查 `kube-proxy` pod 是否正在 `kube-system` 命名空間中執行。 如果它並未處於執行中狀態，請刪除該 Pod，而它會重新啟動。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>我無法使用 kubectl 記錄取得記錄，或無法連線到 API 伺服器。 我收到「伺服器發生錯誤：撥接後端：撥接 tcp ...」。 我該怎麼辦？

請確定預設的網路安全性群組並未修改，而且埠22和9000已開放給 API 伺服器連接。 使用 `kubectl get pods --namespace kube-system` 命令，檢查 `tunnelfront` pod 是否正在*kube 系統*命名空間中執行。 若非如此，請強制刪除該 Pod，而它會重新啟動。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我正嘗試升級或調整，卻收到「訊息：不允許變更屬性 ' imageReference '」錯誤。 如何修正此問題？

您可能會因為修改了 AKS 叢集內代理程式節點中的標記而收到此錯誤。 修改和刪除 MC_* 資源群組中資源的標記和其他屬性可能會導致非預期的結果。 在 AKS 叢集中修改 MC_* 群組下的資源會破壞服務層級目標 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>我收到錯誤，指出我的叢集處於失敗狀態，而且升級或調整規模必須等到修正後才會生效

*此疑難排解協助是從 https://aka.ms/aks-cluster-failed 導向*

當叢集因多種原因而進入失敗狀態時，就會發生此錯誤。 在重試先前失敗的作業之前，請遵循下列步驟來解決叢集失敗狀態：

1. 在叢集 `failed` 狀態之前，`upgrade` 和 `scale` 作業將會失敗。 常見的根本問題和解決方式包括：
    * 使用**不足的計算（CRP）配額**進行調整。 若要解決此問題，請先將叢集調整回配額內的穩定目標狀態。 然後，請遵循下列[步驟來要求增加計算配額](../azure-portal/supportability/resource-manager-core-quotas-request.md)，然後再嘗試重新擴充超過初始配額限制。
    * 使用 advanced 網路和**不足的子網（網路）資源**來調整叢集。 若要解決此問題，請先將叢集調整回配額內的穩定目標狀態。 然後[，遵循下列步驟來要求增加資源配額，](../azure-resource-manager/templates/error-resource-quota.md#solution)再嘗試重新相應增加超過初始配額限制。
2. 解決升級失敗的根本原因之後，您的叢集應該會處於成功狀態。 一旦驗證成功狀態之後，請重試原始操作。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>我在嘗試升級或調整狀態時收到錯誤。我的叢集目前正在升級或升級失敗

*此疑難排解協助是從 https://aka.ms/aks-pending-upgrade 導向*

在具有單一節點集區或具有[多個節點](use-multiple-node-pools.md)集區之叢集的叢集上進行升級和調整作業是互斥的。 您不能同時升級和調整叢集或節點集區。 相反地，每個作業類型必須在目標資源上完成，然後才在該相同資源上進行下一個要求。 如此一來，當作用中的升級或調整作業進行中或已嘗試，且後續失敗時，作業就會受到限制。 

若要協助診斷問題，請執行 `az aks show -g myResourceGroup -n myAKSCluster -o table` 以取出叢集上的詳細狀態。 根據結果：

* 如果叢集正在主動升級，請等候作業終止。 如果成功，請再次嘗試先前失敗的操作。
* 如果叢集升級失敗，請遵循上一節中所述的步驟。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>我是否可以將叢集移至不同的訂用帳戶，或將我的訂用帳戶新增至新的租使用者？

如果您已將 AKS 叢集移至不同的訂用帳戶或主控訂用帳戶的叢集至新的租使用者，叢集將會因為遺失角色指派和服務主體許可權而失去功能。 AKS 不支援在訂用帳戶或租使用者**之間移動**叢集，因為此條件約束。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>我在嘗試使用需要虛擬機器擴展集的功能時收到錯誤

*此疑難排解協助是從 aka.ms/aks-vmss-enablement 導向*

您可能會收到錯誤，指出您的 AKS 叢集不在虛擬機器擴展集上，如下列範例所示：

**AgentPool ' AgentPool ' 已將自動調整設定為 [已啟用]，但不在虛擬機器擴展集**

若要使用叢集自動調整程式或多個節點集區之類的功能，您必須建立使用虛擬機器擴展集的 AKS 叢集。 如果您嘗試使用相依于虛擬機器擴展集的功能，並將目標設為一般非虛擬機器擴展集 AKS 叢集，則會傳回錯誤。

遵循適當檔中的 [*開始之前*] 步驟，以正確地建立 AKS 叢集：

* [使用叢集自動調整程式](cluster-autoscaler.md)
* [建立和使用多個節點集區](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS 資源和參數會強制執行哪些命名限制？

*此疑難排解協助是從 aka.ms/aks-naming-rules 導向*

命名限制是由 Azure 平臺和 AKS 所執行。 如果資源名稱或參數違反其中一個限制，則會傳回錯誤，要求您提供不同的輸入。 以下是適用的一般命名指導方針：

* 叢集名稱必須是1-63 個字元。 唯一允許的字元是字母、數位、破折號和底線。 第一個和最後一個字元必須是字母或數位。
* AKS *MC_* 資源組名結合了資源組名和資源名稱。 自動產生的 `MC_resourceGroupName_resourceName_AzureRegion` 語法必須不超過80個字元。 如有需要，請縮短您的資源組名或 AKS 叢集名稱的長度。
* *DnsPrefix*必須以英數位元值開頭和結尾，且必須介於1-54 個字元之間。 有效的字元包括英數位元值和連字號（-）。 *DnsPrefix*不能包含特殊字元，例如句號（.）。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>我在嘗試建立、更新、調整、刪除或升級叢集時收到錯誤，因為另一個作業正在進行中，所以不允許該作業。

*此疑難排解協助是從 aka.ms/aks-pending-operation 導向*

當先前的作業仍在進行中時，叢集作業會受到限制。 若要取得叢集的詳細狀態，請使用 `az aks show -g myResourceGroup -n myAKSCluster -o table` 命令。 視需要使用您自己的資源群組和 AKS 叢集名稱。

根據叢集狀態的輸出：

* 如果叢集處於 [*成功*] 或 [*失敗*] 以外的任何布建狀態，請等候作業（*升級/更新/建立/調整/刪除/遷移*）終止。 當先前的作業完成時，請重新嘗試最新的叢集操作。

* 如果叢集的升級失敗，請遵循我[收到的錯誤：「我的叢集處於失敗狀態，而且升級或調整無法正常執行，直到修正為止](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)」。

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>我收到錯誤，指出我在嘗試建立新叢集但未傳入現有叢集時，找不到我的服務主體。

建立 AKS 叢集時，它需要服務主體來代表您建立資源。 AKS 提供在叢集建立時建立新的功能，但這需要 Azure Active Directory 在合理的時間內完全傳播新的服務主體，才能讓叢集在建立時成功。 當此傳播時間過長時，叢集將無法建立驗證，因為它找不到可用的服務主體來執行此動作。 

針對此使用下列因應措施：
1. 使用現有的服務主體，其已在不同區域之間傳播，並在叢集建立時存在於傳入 AKS。
2. 如果使用自動化腳本，請在建立服務主體與建立 AKS 叢集之間新增時間延遲。
3. 如果使用 Azure 入口網站，請在建立期間返回叢集設定，然後在幾分鐘後重試驗證頁面。

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>我在限制輸出流量之後收到錯誤

限制來自 AKS 叢集的輸出流量時，有[必要和選用的建議](limit-egress-traffic.md)輸出埠/網路規則，以及適用于 AKS 的 FQDN/應用程式規則。 如果您的設定與上述任何規則發生衝突，您可能無法執行某些 `kubectl` 命令。 建立 AKS 叢集時，您可能也會看到錯誤。

請確認您的設定不會與任何必要或選用的建議輸出埠/網路規則和 FQDN/應用程式規則相衝突。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure 儲存體和 AKS 疑難排解

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>建議的 Azure 磁片 Kubernetes 穩定版本有哪些？ 

| Kubernetes 版本 | 建議的版本 |
| -- | :--: |
| 1.12 | 1.12.9 或更新版本 |
| 1.13 | 1.13.6 或更新版本 |
| 1.14 | 1.14.2 或更新版本 |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>哪些版本的 Kubernetes 在主權雲端上具有 Azure 磁片支援？

| Kubernetes 版本 | 建議的版本 |
| -- | :--: |
| 1.12 | 1.12.0 或更新版本 |
| 1.13 | 1.13.0 或更新版本 |
| 1.14 | 1.14.0 或更新版本 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Azure 磁片的 WaitForAttach 失敗：剖析 "/dev/disk/azure/scsi1/lun1"：不正確語法

在 Kubernetes 1.10 版中，MountVolume. WaitForAttach 可能會因為 Azure 磁片重新掛接而失敗。

在 Linux 上，您可能會看到不正確的 DevicePath 格式錯誤。 例如：

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

在 Windows 上，您可能會看到錯誤的 DevicePath （LUN）數位錯誤。 例如：

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

下列版本的 Kubernetes 已修正此問題：

| Kubernetes 版本 | 已修正版本 |
| -- | :--: |
| 1.10 | 1.10.2 或更新版本 |
| 1.11 | 1.11.0 或更新版本 |
| 1.12 和更新版本 | N/A |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>在 mountOptions for Azure 磁片中設定 uid 和 gid 時失敗

Azure 磁片預設會使用 ext4，xfs filesystem，而 mountOptions （例如 uid = x，gid = x）則不能在掛接時設定。 例如，如果您嘗試設定 mountOptions uid = 999，gid = 999，會看到類似下面的錯誤：

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

您可以執行下列其中一項動作來減輕此問題：

* 設定[pod 的安全性內容](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)，方法是在 fsGroup 中設定 runAsUser 和 gid 中的 uid。 例如，下列設定會將 pod 以 root 身分執行，使其可供任何檔案存取：

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
  > 由於 gid 和 uid 預設會以 root 或0的形式裝載。 如果 gid 或 uid 設定為非根目錄，例如1000，則 Kubernetes 會使用 `chown` 來變更該磁片下的所有目錄和檔案。 這種作業可能非常耗時，而且可能會使磁片的載入速度變慢。

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

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>刪除 pod 使用中的 Azure 磁片 PersistentVolumeClaim 時發生錯誤

如果您嘗試刪除 pod 所使用的 Azure 磁片 PersistentVolumeClaim，可能會看到錯誤。 例如：

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

在 Kubernetes 1.10 和更新版本中，預設會啟用 PersistentVolumeClaim 保護功能，以防止此錯誤。 如果您使用的 Kubernetes 版本沒有此問題的修正程式，您可以在刪除 PersistentVolumeClaim 之前，先使用 PersistentVolumeClaim 刪除 pod，以減輕這個問題。


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>將磁片連接至節點時，發生「找不到磁片的 Lun」錯誤

將磁片連接至節點時，您可能會看到下列錯誤：

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

下列版本的 Kubernetes 已修正此問題：

| Kubernetes 版本 | 已修正版本 |
| -- | :--: |
| 1.10 | 1.10.10 或更新版本 |
| 1.11 | 1.11.5 或更新版本 |
| 1.12 | 1.12.3 或更新版本 |
| 1.13 | 1.13.0 或更新版本 |
| 1.14 和更新版本 | N/A |

如果您使用的 Kubernetes 版本沒有此問題的修正程式，您可以等候幾分鐘再重試，以減輕問題。

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>在多個附加/卸離作業期間，Azure 磁片連結/卸離失敗、掛接問題或 i/o 錯誤

從 Kubernetes 版本1.9.2 開始，當平行執行多個附加/卸離作業時，您可能會看到下列磁片問題，原因是已變更 VM 快取：

* 磁片連接/卸離失敗
* 磁片 i/o 錯誤
* VM 中未預期的磁片表示中斷連結
* 因為附加不存在的磁片，導致 VM 進入失敗狀態

下列版本的 Kubernetes 已修正此問題：

| Kubernetes 版本 | 已修正版本 |
| -- | :--: |
| 1.10 | 1.10.12 或更新版本 |
| 1.11 | 1.11.6 或更新版本 |
| 1.12 | 1.12.4 或更新版本 |
| 1.13 | 1.13.0 或更新版本 |
| 1.14 和更新版本 | N/A |

如果您使用的 Kubernetes 版本沒有此問題的修正程式，您可以嘗試下列方法來減輕此問題：

* 如果有一段長時間的磁片正在等待卸離，請嘗試手動卸離磁片

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure 磁片正在等候無限期卸離

在某些情況下，如果第一次嘗試時 Azure 磁片中斷連結失敗，則不會重試卸離作業，且會繼續連接至原始節點 VM。 將磁片從一個節點移至另一個節點時，可能會發生此錯誤。 例如：

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

下列版本的 Kubernetes 已修正此問題：

| Kubernetes 版本 | 已修正版本 |
| -- | :--: |
| 1.11 | 1.11.9 或更新版本 |
| 1.12 | 1.12.7 或更新版本 |
| 1.13 | 1.13.4 或更新版本 |
| 1.14 和更新版本 | N/A |

如果您使用的 Kubernetes 版本沒有此問題的修正程式，您可以手動卸離磁片來減輕此問題。

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure 磁片中斷連結失敗，導致潛在的競爭情形問題和不正確資料磁片清單

當 Azure 磁片無法中斷連結時，將會重試最多六次，以使用指數退避來卸離磁片。 它也會在資料磁片清單上保留大約3分鐘的節點層級鎖定。 如果在這段時間內手動更新磁片清單（例如手動附加或卸離作業），這會導致節點層級鎖定所持有的磁片清單過時，並導致節點 VM 上的不穩定。

下列版本的 Kubernetes 已修正此問題：

| Kubernetes 版本 | 已修正版本 |
| -- | :--: |
| 1.12 | 1.12.9 或更新版本 |
| 1.13 | 1.13.6 或更新版本 |
| 1.14 | 1.14.2 或更新版本 |
| 1.15 和更新版本 | N/A |

如果您使用的 Kubernetes 版本沒有此問題的修正，且您的節點 VM 具有過時的磁片清單，您可以將所有非現有的磁片從 VM 卸離為單一的大量作業，以減輕此問題。 **個別卸離非現有的磁片可能會失敗。**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>大量的 Azure 磁片會導致附加/卸離緩慢

當連接至節點 VM 的 Azure 磁片數目大於10時，附加和卸離作業可能會變慢。 此問題是已知的問題，目前沒有任何因應措施。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure 磁片中斷連結失敗，導致潛在的節點 VM 處於失敗狀態

在某些邊緣案例中，Azure 磁片中斷連結可能會部分失敗，並讓節點 VM 處於失敗狀態。

下列版本的 Kubernetes 已修正此問題：

| Kubernetes 版本 | 已修正版本 |
| -- | :--: |
| 1.12 | 1.12.10 或更新版本 |
| 1.13 | 1.13.8 或更新版本 |
| 1.14 | 1.14.4 或更新版本 |
| 1.15 和更新版本 | N/A |

如果您使用的 Kubernetes 版本沒有此問題的修正，而您的節點 VM 處於失敗狀態，您可以使用下列其中一種方式，手動更新 VM 狀態來減輕此問題：

* 針對以可用性設定組為基礎的叢集：
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* 針對以 VMSS 為基礎的叢集：
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure 檔案儲存體和 AKS 疑難排解

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Azure 檔案服務的建議 Kubernetes 穩定版本有哪些？
 
| Kubernetes 版本 | 建議的版本 |
| -- | :--: |
| 1.12 | 1.12.6 或更新版本 |
| 1.13 | 1.13.4 或更新版本 |
| 1.14 | 1.14.0 或更新版本 |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>哪些版本的 Kubernetes 在主權雲端上有 Azure 檔案儲存體支援？

| Kubernetes 版本 | 建議的版本 |
| -- | :--: |
| 1.12 | 1.12.0 或更新版本 |
| 1.13 | 1.13.0 或更新版本 |
| 1.14 | 1.14.0 或更新版本 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>使用 Azure 檔案儲存體時的預設 mountOptions 為何？

建議的設定：

| Kubernetes 版本 | 不 dirMode 的值|
| -- | :--: |
| 1.12.0-1.12。1 | 0755 |
| 1.12.2 和更新版本 | 0777 |

如果使用 Kubernetes version 1.8.5 版或更高版本的叢集，並使用儲存類別動態建立持續性磁片區，則可以在儲存類別物件上指定掛接選項。 下列範例會設定 0777：

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

一些其他實用的*mountOptions*設定：

* *mfsymlinks*會讓 Azure 檔案儲存體掛接（cifs）支援符號連結
* *nobrl*會防止將位元組範圍鎖定要求傳送至伺服器。 對於以 cifs 樣式強制位元組範圍鎖定中斷的特定應用程式而言，這是必要的設定。 大部分的 cifs 伺服器尚未支援要求諮詢位元組範圍鎖定。 如果不使用*nobrl*，使用 cifs 樣式強制位元組範圍鎖定來中斷的應用程式可能會造成類似下列的錯誤訊息：
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>使用 Azure 檔案儲存體時發生「無法變更許可權」錯誤

在 Azure 檔案儲存體外掛程式上執行于 postgresql 時，您可能會看到類似下列的錯誤：

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

此錯誤是由使用 cifs/SMB 通訊協定的 Azure 檔案儲存體外掛程式所造成。 使用 cifs/SMB 通訊協定時，無法在掛接後變更檔案和目錄許可權。

若要解決此問題，請搭配 Azure 磁片外掛程式使用子*路徑*。 

> [!NOTE] 
> 針對 ext3/4 磁片類型，在格式化磁片之後，會遺失 + 找到的目錄。

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>當處理許多小型檔案時，與 Azure 磁片相較之下，Azure 檔案儲存體具有高延遲

在某些情況下，例如處理許多小型檔案，相較于 Azure 磁片，使用 Azure 檔案儲存體時，您可能會遇到高延遲的情況。

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>在儲存體帳戶上啟用 [允許從選取的網路存取] 設定時發生錯誤

如果您在 AKS 中啟用了用於動態布建之儲存體帳戶上的 [*允許從選取的網路存取*]，當您 AKS 建立檔案共用時，將會收到錯誤：

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

此錯誤是因為在設定 [*允許從選取的網路存取*] 時，Kubernetes *persistentvolume 控制器*不在所選擇的網路上。

您可以使用靜態布建[與 Azure 檔案儲存體](azure-files-volume.md)來減輕此問題。

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure 檔案儲存體無法在 Windows pod 中重新掛接

如果已刪除具有 Azure 檔案儲存體掛接的 Windows pod，然後排程在相同節點上重新建立，則掛接將會失敗。 發生此失敗的原因是 `New-SmbGlobalMapping` 命令失敗，因為 Azure 檔案儲存體掛接已裝載于節點上。

例如，您可能會看到類似下列的錯誤：

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

下列版本的 Kubernetes 已修正此問題：

| Kubernetes 版本 | 已修正版本 |
| -- | :--: |
| 1.12 | 1.12.6 或更新版本 |
| 1.13 | 1.13.4 或更新版本 |
| 1.14 和更新版本 | N/A |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>因為儲存體帳戶金鑰已變更，所以 Azure 檔案儲存體掛接失敗

如果您的儲存體帳戶金鑰已變更，您可能會看到 Azure 檔案儲存體掛接失敗。

您可以使用 base64 編碼的儲存體帳戶金鑰，在 Azure 檔案密碼中手動更新*azurestorageaccountkey*欄位，以減輕問題。

若要以 base64 編碼您的儲存體帳戶金鑰，您可以使用 `base64`。 例如：

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

若要更新您的 Azure 秘密檔案，請使用 `kubectl edit secret`。 例如：

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

幾分鐘後，代理程式節點會以更新的儲存體金鑰重試 azure 檔案掛接。

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>叢集自動調整程式無法調整，因為發生錯誤而無法修正節點群組大小

如果您的叢集自動調整程式未相應增加/減少，而且您在叢集[自動調整程式記錄][view-master-logs]中看到類似下面的錯誤。

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

此錯誤的原因是上游叢集自動調整程式競爭情況，其中叢集自動調整程式的值會與實際上在叢集中的不同。 若要離開此狀態，只需要停用再重新啟用叢集[自動調整程式][cluster-autoscaler]。

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>緩慢的磁片附件，GetAzureDiskLun 需要10到15分鐘的時間，而且您會收到錯誤訊息

在**1.15.0 之前**的 Kubernetes 版本中，您可能會收到錯誤，例如「**錯誤 WaitForAttach 找不到磁片的 Lun**」。  解決此問題的因應措施是等候大約15分鐘，然後重試。

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
