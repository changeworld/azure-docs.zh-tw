---
title: 針對常見的 Azure Kubernetes Service 問題進行疑難排解
description: 了解在使用 Azure Kubernetes Service (AKS) 時，如何針對常見問題進行疑難排解並加以解決
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
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
如果您在 Azure CLI 中部署 AKS 叢集，則每個節點的最大 Pod 數設定會預設為 110。 (確定您使用最新版的 Azure CLI)。 在 `az aks create` 命令中使用 `–-max-pods` 旗標，即可變更此預設設定。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>當我使用進階網路設定部署 AKS 叢集時，收到 insufficientSubnetSize 錯誤。 我該怎麼辦？

如果使用 Azure CNI（高級網路），AKS 將根據配置的每個節點的"最大 pods"分配 IP 位址。 根據每個節點配置的最大 pod，子網大小必須大於節點數和每個節點設置的最大 pod 的生成。 以下方程概述了這一點：

子網大小>群集中的節點數（考慮到未來的縮放要求） = 每個節點集的最大 pod。

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

如果看不到 Kubernetes 儀表板，請檢查`kube-proxy`該窗格是否在`kube-system`命名空間中運行。 如果它並未處於執行中狀態，請刪除該 Pod，而它會重新啟動。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>我無法使用 kubectl 記錄取得記錄，或無法連線到 API 伺服器。 我收到"來自伺服器的錯誤：撥號後端錯誤：撥號 tcp..." 我該怎麼辦？

確保未修改預設網路安全性群組，並且埠 22 和 9000 都打開以連接到 API 伺服器。 使用`kubectl get pods --namespace kube-system`命令`tunnelfront`檢查 pod 是否在*kube 系統*命名空間中運行。 若非如此，請強制刪除該 Pod，而它會重新啟動。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我試圖升級或縮放，並收到一條"消息：不允許更改屬性'圖像參考'"錯誤。 如何修正此問題？

您可能會因為修改了 AKS 叢集內代理程式節點中的標記而收到此錯誤。 修改和刪除 MC_* 資源群組中資源的標記和其他屬性可能會導致非預期的結果。 在 AKS 叢集中修改 MC_* 群組下的資源會破壞服務層級目標 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>我收到我的群集處於失敗狀態的錯誤，在修復群集之前，升級或縮放將不起作用

*此故障排除説明來自https://aka.ms/aks-cluster-failed*

當群集由於多種原因進入失敗狀態時，將發生此錯誤。 在重試以前失敗的操作之前，請按照以下步驟解決群集失敗狀態：

1. 在群集處於狀態之前`failed`，`upgrade``scale`操作不會成功。 常見的根問題和解決方法包括：
    * **計算不足 （CRP） 配額**的縮放。 要解決問題，請首先將群集縮放回配額內的穩定目標狀態。 然後按照[以下步驟請求增加計算配額，](../azure-portal/supportability/resource-manager-core-quotas-request.md)然後再嘗試再次擴展到初始配額限制之外。
    * 使用高級網路和**子網（網路）資源不足**的群集進行擴展。 要解決問題，請首先將群集縮放回配額內的穩定目標狀態。 然後按照[以下步驟請求增加資源配額，](../azure-resource-manager/templates/error-resource-quota.md#solution)然後再嘗試再次擴展到初始配額限制之外。
2. 解決升級失敗的根本原因後，群集應處於成功狀態。 驗證成功狀態後，重試原始操作。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>嘗試升級或縮放群集當前正在升級或升級失敗時收到錯誤

*此故障排除説明來自https://aka.ms/aks-pending-upgrade*

具有單個節點池的群集或具有[多個節點池](use-multiple-node-pools.md)的群集上的升級和縮放操作是互斥的。 不能同時升級和擴展群集或節點池。 相反，在同一資源上下一個請求之前，每個操作類型都必須在目標資源上完成。 因此，當發生或嘗試活動升級或縮放操作，隨後失敗時，操作將受到限制。 

説明診斷運行`az aks show -g myResourceGroup -n myAKSCluster -o table`問題以檢索群集上的詳細狀態。 根據結果：

* 如果群集正在主動升級，請等待操作終止。 如果成功，請重試以前失敗的操作。
* 如果群集升級失敗，請按照上一節中概述的步驟操作。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>是否可以將群集移動到其他訂閱，也可以將群集的訂閱移動到新租戶？

如果將 AKS 群集移動到其他訂閱或擁有訂閱的新租戶的群集，群集將由於丟失角色指派和服務主體許可權而喪失功能。 由於此約束 **，AKS 不支援跨訂閱或租戶移動群集**。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>我收到使用需要虛擬機器縮放集的功能的錯誤

*此故障排除説明由aka.ms/aks-vmss-enablement*

您可能會收到指示 AKS 群集不在虛擬機器規模集中的錯誤，例如以下示例：

**代理池"代理池"已設置自動縮放為已啟用，但不在虛擬機器縮放集**

要使用群集自動縮放器或多個節點池等功能，必須創建使用虛擬機器縮放集的 AKS 群集。 如果您嘗試使用依賴于虛擬機器縮放集的功能，並且目標是常規的非虛擬機器規模集 AKS 群集，則返回錯誤。

按照在相應文檔中*開始*步驟以正確創建 AKS 群集的步驟操作：

* [使用群集自動縮放器](cluster-autoscaler.md)
* [創建和使用多個節點池](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>對 AKS 資源和參數實施哪些命名限制？

*此故障排除説明來自aka.ms/aks-naming-rules*

命名限制由 Azure 平臺和 AKS 實現。 如果資源名稱或參數違反了這些限制之一，則返回一個錯誤，要求您提供不同的輸入。 以下常見命名準則適用：

* 群集名稱必須為 1-63 個字元。 唯一允許的字元是字母、數位、破折號和底線。 第一個和最後一個字元必須是字母或數位。
* *AKSMC_* 資源組名稱結合了資源組名稱和資源名稱。 的`MC_resourceGroupName_resourceName_AzureRegion`自動生成的語法不能大於 80 個字元。 如果需要，請縮短資源組名稱或 AKS 群集名稱的長度。
* *dnsPrefix*必須以字母數位值開始和結束，並且必須介於 1-54 個字元之間。 有效字元包括字母數位值和連字號 （-）。 *dnsPrefix*不能包括特殊字元，如句點 （.）。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>在嘗試創建、更新、縮放、刪除或升級群集時，我收到錯誤，該操作不允許，因為正在進行另一個操作。

*此故障排除説明由aka.ms/aks-pending-operation*

當以前的操作仍在進行時，群集操作受到限制。 若要檢索群集的詳細狀態，請使用 命令`az aks show -g myResourceGroup -n myAKSCluster -o table`。 根據需要使用您自己的資源組和 AKS 群集名稱。

根據群集狀態的輸出：

* 如果群集處於 *"成功*"或"*失敗*"以外的任何預配狀態，請等待操作（*升級/更新/創建/縮放/刪除/遷移*）終止。 完成上一個操作後，重新嘗試最新的群集操作。

* 如果群集的升級失敗，請按照概述的步驟，[我收到我的群集處於失敗狀態的錯誤，在修復之前，升級或縮放將不起作用](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)。

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>我收到錯誤，即當我嘗試創建新群集而不傳入現有群集時，未發現我的服務主體。

創建 AKS 群集時，需要服務主體代表您創建資源。 AKS 提供了在群集創建時創建新服務的功能，但這需要 Azure 活動目錄在合理的時間內充分傳播新服務主體，以使群集在創建中取得成功。 當此傳播花費太長時，群集將失敗驗證以創建，因為它找不到可用的服務主體來執行此操作。 

為此，請使用以下解決方法：
1. 使用已跨區域傳播且存在的現有服務主體在群集創建時傳遞到 AKS。
2. 如果使用自動化腳本，則在服務主體創建和 AKS 群集創建之間添加時間延遲。
3. 如果使用 Azure 門戶，請返回到群集設置，在創建期間，並在幾分鐘後重試驗證頁。

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>限制出口流量後，我收到錯誤

限制 AKS 群集的出站流量時，AKS 需要[和可選地推薦](limit-egress-traffic.md)出站埠/網路規則以及 FQDN / 應用程式規則。 如果您的設置與其中任何一個規則衝突，則可能無法運行某些`kubectl`命令。 創建 AKS 群集時，您可能還會看到錯誤。

驗證您的設置是否與任何必需或可選的推薦出站埠/網路規則和 FQDN / 應用程式規則衝突。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure 存儲和 AKS 故障排除

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Azure 磁片推薦的庫伯奈斯的穩定版本是什麼？ 

| 庫伯內斯版本 | 推薦版本 |
| -- | :--: |
| 1.12 | 1.12.9 或更高版本 |
| 1.13 | 1.13.6 或更高版本 |
| 1.14 | 1.14.2 或更高版本 |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>哪些版本的庫伯奈斯在主權雲上具有 Azure 磁片支援？

| 庫伯內斯版本 | 推薦版本 |
| -- | :--: |
| 1.12 | 1.12.0 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Azure 磁片的 WaitForAttach 失敗：分析"/dev/磁片/azure/scsi1/lun1"：無效語法

在 Kubernetes 版本 1.10 中，MountVolume.WaitForAttach 可能會失敗，Azure 磁片重新安裝。

在 Linux 上，您可能會看到不正確的設備路徑格式錯誤。 例如：

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

在 Windows 上，您可能會看到錯誤的設備路徑 （LUN） 編號錯誤。 例如：

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

此問題已在以下版本的 Kubernetes 中修復：

| 庫伯內斯版本 | 固定版本 |
| -- | :--: |
| 1.10 | 1.10.2 或更高版本 |
| 1.11 | 1.11.0 或更高版本 |
| 1.12 及更高版本 | N/A |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>在 Azure 磁片的裝載選項中設置 uid 和 gid 時失敗

預設情況下，Azure 磁片使用 ext4，xfs 檔案系統，並且無法在裝載時設置 uid_x、gid_x 等裝載選項。 例如，如果您嘗試設置裝載選項 uid_999，gid_999，將看到一個錯誤，如下所示：

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

您可以通過執行以下操作來緩解此問題：

* 通過在 runAsUser 中設置 uid 並在 fsGroup 中設置 gid 來[配置窗格的安全上下文](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)。 例如，以下設置將窗格設置為根運行，使其可供任何檔訪問：

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
  > 預設情況下，gid 和 uid 作為根或 0 裝載。 如果 gid 或 uid 設置為非根（例如 1000，Kubernetes）`chown`將用於更改該磁片下的所有目錄和檔。 此操作可能非常耗時，並且可能會使安裝磁片非常緩慢。

* 在`chown`init 容器中使用設置 gid 和 uid。 例如：

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>刪除窗格正在使用的 Azure 磁片持久卷聲明時出錯

如果嘗試刪除窗格正在使用的 Azure 磁片持久卷聲明，則可能會看到錯誤。 例如：

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

在 Kubernetes 版本 1.10 及更高版本中，預設情況下啟用了"持久卷聲明"保護功能，以防止出現此錯誤。 如果您使用的是 Kubernetes 版本，該版本沒有此問題的修復程式，則可以在刪除持久卷聲明之前使用持久卷聲明刪除窗格來緩解此問題。


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>將磁片連接到節點時出錯"找不到磁片 Lun"

將磁片附加到節點時，您可能會看到以下錯誤：

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

此問題已在以下版本的 Kubernetes 中修復：

| 庫伯內斯版本 | 固定版本 |
| -- | :--: |
| 1.10 | 1.10.10 或更高版本 |
| 1.11 | 1.11.5 或更高版本 |
| 1.12 | 1.12.3 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 及更高版本 | N/A |

如果您使用的是 Kubernetes 版本，該版本沒有解決此問題的修復程式，則可以通過等待幾分鐘並重試來緩解此問題。

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>在多個附加/分離操作期間，Azure 磁片附加/分離失敗、裝載問題或 I/O 錯誤

從 Kubernetes 版本 1.9.2 開始，當並行運行多個附加/分離操作時，您可能會看到以下磁片問題，因為 VM 緩存髒：

* 磁片連接/分離失敗
* 磁片 I/O 錯誤
* 來自 VM 的意外磁片分離
* 由於連接不存在的磁片而進入失敗狀態的 VM

此問題已在以下版本的 Kubernetes 中修復：

| 庫伯內斯版本 | 固定版本 |
| -- | :--: |
| 1.10 | 1.10.12 或更高版本 |
| 1.11 | 1.11.6 或更高版本 |
| 1.12 | 1.12.4 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 及更高版本 | N/A |

如果您使用的是 Kubernetes 版本，該版本沒有解決此問題的解決方法，則可以嘗試以下方法來緩解此問題：

* 如果磁片長時間等待分離，請嘗試手動分離磁片

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure 磁片等待無限期分離

在某些情況下，如果 Azure 磁片分離操作在第一次嘗試時失敗，它將不會重試分離操作，並且將繼續連接到原始節點 VM。 將磁片從一個節點移動到另一個節點時，可能會發生此錯誤。 例如：

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

此問題已在以下版本的 Kubernetes 中修復：

| 庫伯內斯版本 | 固定版本 |
| -- | :--: |
| 1.11 | 1.11.9 或更高版本 |
| 1.12 | 1.12.7 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 及更高版本 | N/A |

如果您使用的是 Kubernetes 版本，該版本沒有解決此問題的修復程式，則可以通過手動分離磁片來緩解此問題。

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure 磁片分離失敗導致潛在的競爭條件問題和不正確資料磁片清單

當 Azure 磁片無法分離時，它將重試多達六次，以便使用指數回退來分離磁片。 它還將在資料磁片清單中保留節點級鎖約 3 分鐘。 如果在此期間手動更新磁片清單（如手動附加或分離操作），則會導致節點級鎖持有的磁片清單過時，並導致節點 VM 不穩定。

此問題已在以下版本的 Kubernetes 中修復：

| 庫伯內斯版本 | 固定版本 |
| -- | :--: |
| 1.12 | 1.12.9 或更高版本 |
| 1.13 | 1.13.6 或更高版本 |
| 1.14 | 1.14.2 或更高版本 |
| 1.15 及更高版本 | N/A |

如果使用的 Kubernetes 版本沒有此問題的修復程式，並且節點 VM 具有過時的磁片清單，則可以通過將所有不存在的磁片從 VM 分離為單個批量操作來緩解此問題。 **單獨分離不存在的磁片可能會失敗。**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>大量 Azure 磁片導致連接/分離速度緩慢

當附加到節點 VM 的 Azure 磁片數大於 10 時，附加和分離操作可能很慢。 此問題是已知問題，目前沒有解決方法。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure 磁片分離失敗導致潛在節點 VM 處於故障狀態

在某些邊緣情況下，Azure 磁片分離可能會部分失敗，使節點 VM 處於失敗狀態。

此問題已在以下版本的 Kubernetes 中修復：

| 庫伯內斯版本 | 固定版本 |
| -- | :--: |
| 1.12 | 1.12.10 或更高版本 |
| 1.13 | 1.13.8 或更高版本 |
| 1.14 | 1.14.4 或更高版本 |
| 1.15 及更高版本 | N/A |

如果您使用的 Kubernetes 版本沒有此問題的修復程式，並且節點 VM 處於失敗狀態，則可以使用以下方法之一手動更新 VM 狀態來緩解此問題：

* 對於基於可用性集的群集：
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* 對於基於 VMSS 的群集：
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure 檔和 AKS 故障排除

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Azure 檔推薦的庫伯奈斯的穩定版本是什麼？
 
| 庫伯內斯版本 | 推薦版本 |
| -- | :--: |
| 1.12 | 1.12.6 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>哪些版本的庫伯奈斯在主權雲上支援 Azure 檔？

| 庫伯內斯版本 | 推薦版本 |
| -- | :--: |
| 1.12 | 1.12.0 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>使用 Azure 檔時，預設裝載選項是什麼？

推薦的設置：

| 庫伯內斯版本 | 檔案模式和 dirMode 值|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 及更高版本 | 0777 |

如果使用具有 Kubernetes 版本 1.8.5 或更大版本的群集，並使用存儲類動態創建持久卷，則可以在存儲類物件上指定裝載選項。 下列範例會設定 0777**：

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

一些其他有用的*安裝選項*設置：

* *mfsymlinks*將使 Azure 檔裝載（cIFs）支援符號連結
* *nobrl*將阻止向伺服器傳送的位元組範圍鎖定請求。 對於使用 cifs 樣式強制位元組範圍鎖中斷的某些應用程式，此設置是必需的。 大多數 cifs 伺服器還不支援請求諮詢位元組範圍鎖。 如果不使用*nobrl，* 使用 cifs 樣式強制位元組範圍鎖中斷的應用程式可能會導致類似于的錯誤訊息：
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>使用 Azure 檔時出錯"無法更改許可權"

在 Azure 檔外掛程式上運行 PostgreSQL 時，您可能會看到類似于：

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

此錯誤是由使用 cifs/SMB 協定的 Azure 檔外掛程式引起的。 使用 cIFs/SMB 協定時，在安裝後無法更改檔和目錄許可權。

要解決此問題，請使用*子路徑*與 Azure 磁片外掛程式一起使用。 

> [!NOTE] 
> 對於 ext3/4 磁片類型，在格式化磁片後，有一個丟失 -找到的目錄。

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>與 Azure 磁片相比，在處理許多小檔時，Azure 檔具有較高的延遲

在某些情況下，例如處理許多小檔，與 Azure 磁片相比，使用 Azure 檔時可能會遇到高延遲。

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>在存儲帳戶上啟用"允許訪問允許從所選網路訪問"設置時出錯

如果啟用允許在用於 AKS 中動態預配的存儲帳戶上的*選定網路進行訪問*，則 AKS 創建檔共用時將出現錯誤：

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

此錯誤是由於 Kubernetes*持久卷控制器*不在設置*允許從所選網路訪問*時選擇的網路上。

可以通過使用[Azure 檔的靜態預配](azure-files-volume.md)來緩解此問題。

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure 檔無法重新裝入 Windows 窗格

如果刪除具有 Azure 檔裝載的 Windows 窗格，然後計畫在同一節點上重新創建，則裝載將失敗。 此失敗是由於`New-SmbGlobalMapping`命令失敗，因為 Azure 檔裝載已裝載到節點上。

例如，您可能會看到類似于以下錯誤：

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

此問題已在以下版本的 Kubernetes 中修復：

| 庫伯內斯版本 | 固定版本 |
| -- | :--: |
| 1.12 | 1.12.6 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 及更高版本 | N/A |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>由於存儲帳戶金鑰已更改，Azure 檔裝載失敗

如果存儲帳戶金鑰已更改，您可能會看到 Azure 檔裝載失敗。

您可以通過使用 base64 編碼的存儲帳戶金鑰在 Azure 檔機密中手動更新 Azure 檔機密中的*Azure 存儲帳戶金鑰*欄位來緩解此問題。

要在 base64 中對存儲帳戶金鑰進行編碼，`base64`可以使用 。 例如：

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

要更新 Azure 機密檔，`kubectl edit secret`請使用 。 例如：

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

幾分鐘後，代理節點將使用更新的存儲金鑰重試 Azure 檔裝載。

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>群集自動縮放器無法縮放，無法修復節點組大小， 導致錯誤

如果您的群集自動縮放器未向上/向下縮放，並且在[群集自動縮放器日誌][view-master-logs]上看到如下所示的錯誤。

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

此錯誤是由於上游群集自動縮放器競爭條件，其中群集自動縮放器以與群集中實際的值不同的值結束。 要擺脫此狀態，只需禁用並重新啟用[群集自動縮放器][cluster-autoscaler]。

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>磁片連線速度慢，GetAzureDiskLun 需要 10 到 15 分鐘，您會收到錯誤

在庫伯奈斯**版本超過1.15.0，** 你可能會收到一個錯誤，如**錯誤等待附加找不到Lun的磁片**。  解決方法是等待大約 15 分鐘並重試。

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
