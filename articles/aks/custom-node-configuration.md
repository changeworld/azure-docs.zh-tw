---
title: '針對 Azure Kubernetes Service (AKS) 節點集區自訂節點設定 (預覽) '
description: 瞭解如何在 Azure Kubernetes Service (AKS) 叢集節點和節點集區自訂設定。
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: f1e9d65baacb9c712b92ef6f00abda169031b47e
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581870"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>針對 Azure Kubernetes Service (AKS) 節點集區自訂節點設定 (預覽) 

自訂您的節點設定可讓您設定或調整作業系統 (OS) 設定或 kubelet 參數，以符合工作負載的需求。 當您建立 AKS 叢集或將節點集區新增至叢集時，您可以自訂常用作業系統和 kubelet 設定的子集。 若要設定超出此子集的設定，請使用背景程式 [集來自訂所需的設定，而不會失去節點的 AKS 支援](support-policies.md#shared-responsibility)。

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>註冊 `CustomNodeConfigPreview` 預覽功能

若要建立可自訂 kubelet 參數或 OS 設定的 AKS 叢集或節點集區，您必須 `CustomNodeConfigPreview` 在訂用帳戶上啟用功能旗標。

`CustomNodeConfigPreview`使用[az feature register][az-feature-register]命令註冊功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

狀態需要幾分鐘的時間才會顯示「已註冊」。 使用 [az feature list][az-feature-list] 命令來確認註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

當您準備好時，請使用 [az provider register][az-provider-register]命令重新整理 *>microsoft.containerservice* 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 延伸模組

若要建立可自訂 kubelet 參數或 OS 設定的 AKS 叢集或節點集區，您需要最新的 *AKS-preview* Azure CLI 擴充功能。 使用 [az extension add][az-extension-add]命令安裝 *aks-preview* Azure CLI 擴充功能。 或使用 [az extension update][az-extension-update] 命令安裝任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>使用自訂節點設定

### <a name="kubelet-custom-configuration"></a>Kubelet 自訂設定

以下列出支援的 Kubelet 參數和接受的值。

| 參數 | 允許的值/間隔 | 預設 | 描述 |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | 無、靜態 | 無 | 靜態原則可讓具有整數 CPU 的可 [保證](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) pod 中的容器存取節點上的專有 cpu。 |
| `cpuCfsQuota` | true、false | true |  針對指定 CPU 限制的容器，啟用/停用 CPU CFS 配額強制。 | 
| `cpuCfsQuotaPeriod` | 以毫秒為單位的間隔 (ms)  | `100ms` | 設定 CPU CFS 配額期間值。 | 
| `imageGcHighThreshold` | 0-100 | 85 | 一律執行映射垃圾收集的磁片使用量百分比。 **將會** 觸發垃圾收集的最小磁片使用量。 若要停用映射垃圾收集，請設定為100。 | 
| `imageGcLowThreshold` | 0-100，不是高於 `imageGcHighThreshold` | 80 | 從未執行映射垃圾收集之前的磁片使用量百分比。 **可** 觸發垃圾收集的最小磁片使用量。 |
| `topologyManagerPolicy` | 無、盡力、受限、單一 numa 節點 | 無 | 將 NUMA 節點對齊優化，請參閱 [這裡](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/)的詳細資訊。 僅限 kubernetes v 1.18 +。 |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | 無 | 允許的 unsafe sysctls 或 unsafe sysctl 模式清單。 | 

### <a name="linux-os-custom-configuration"></a>Linux OS 自訂設定

以下列出支援的 OS 設定和接受的值。

#### <a name="file-handle-limits"></a>檔案控制代碼限制

當您提供大量的流量時，您所提供的流量通常是來自大量的本機檔案。 您可以調整下列核心設定和內建限制，讓您能夠處理更多的系統記憶體成本。

| 設定 | 允許的值/間隔 | 預設 | 描述 |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192-12000500 | 709620 | Linux 核心將配置的檔案控制碼數目上限，藉由增加此值，您可以增加允許的開啟檔案數目上限。 |
| `fs.inotify.max_user_watches` | 781250-2097152 | 1048576 | 系統允許的檔案監看式數目上限。 每個 *監看* 式在32位核心大約是90個位元組，在64位核心上大約是160個位元組。 | 
| `fs.aio-max-nr` | 65536-6553500 | 65536 | Aio （nr）會顯示目前全系統的非同步 io 要求數目。 aio-最大值，可讓您將 aio-nr 可成長的最大值變更為。 |
| `fs.nr_open` | 8192-20000500 | 1048576 | 進程可以配置的檔案控制代碼數目上限。 |


#### <a name="socket-and-network-tuning"></a>通訊端和網路調整

針對預期會處理大量並行會話的代理程式節點，您可以使用下方的 TCP 和網路選項子集，以針對每個節點集區進行調整。 

| 設定 | 允許的值/間隔 | 預設 | 描述 |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096-3240000 | 16384 | 可以排入任何指定接聽通訊端的連接要求數目上限。 傳遞給 [接聽 (2) ](http://man7.org/linux/man-pages/man2/listen.2.html) 函數之待處理專案（backlog）參數值的上限。 如果待處理專案（backlog）引數大於 `somaxconn` ，則會以無訊息模式截斷此限制。
| `net.core.netdev_max_backlog` | 1000-3240000 | 1000 | 當介面收到比核心可以處理的封包時，在輸入端排入佇列的最大封包數目。 |
| `net.core.rmem_max` | 212992-134217728 | 212992 | 接收通訊端緩衝區大小上限（以位元組為單位）。 |
| `net.core.wmem_max` | 212992-134217728 | 212992 | 傳送通訊端緩衝區大小上限（以位元組為單位）。 | 
| `net.core.optmem_max` | 20480-4194304 | 20480 | 每個通訊端允許的輔助緩衝區大小上限 (選項記憶體緩衝區) 。 在少數情況下，會使用通訊端選項記憶體來儲存與通訊端使用相關的額外結構。 | 
| `net.ipv4.tcp_max_syn_backlog` | 128-3240000 | 16384 | 尚未從連接的用戶端收到通知的佇列連接要求數目上限。 如果超過此數目，核心就會開始卸載要求。 |
| `net.ipv4.tcp_max_tw_buckets` | 8000-1440000 | 32768 | `timewait`系統同時保留的最大通訊端數目。 如果超過此數目，則會立即終結時間等候通訊端，並列印警告。 |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | 任何應用程式) 連接不再參考孤立 (的時間長度，在本機端中止之前將保持 FIN_WAIT_2 狀態。 |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | 啟用時 TCP 送出 `keepalive` 訊息的頻率 `keepalive` 。 |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | TCP 送 `keepalive` 出的探查數目，直到它決定連接中斷為止。 |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | 探查的傳送頻率。乘以 `tcp_keepalive_probes` 它會在探查開始之後，讓您的時間終止沒有回應的連接。 | 
| `net.ipv4.tcp_tw_reuse` | 0 或 1 | 0 | 當安全地從通訊協定的觀點來看，允許重複使用新連線的 `TIME-WAIT` 通訊端。 | 
| `net.ipv4.ip_local_port_range` | First： 1024-60999 和 Last： 32768-65000] | 第一個：32768和最後一個：60999 | TCP 和 UDP 流量用來選擇本機埠的本機埠範圍。 由兩個數字組成：第一個數位是代理程式節點上 TCP 和 UDP 流量允許的第一個本機埠，第二個是最後一個本機埠號碼。 | 
| `net.ipv4.neigh.default.gc_thresh1`|  128-80000 | 4096 | ARP 快取中可能的最小專案數目。 如果專案數低於此設定，則不會觸發垃圾收集。 | 
| `net.ipv4.neigh.default.gc_thresh2`|  512-90000 | 8192 | ARP 快取中可能的最大專案數。 這項設定最重要，因為在達到此軟性最大值之後，將會觸發 ARP 垃圾收集大約5秒。 |
| `net.ipv4.neigh.default.gc_thresh3`|  1024-100000 | 16384 | ARP 快取中的最大專案數。 |
| `net.netfilter.nf_conntrack_max` | 131072-589824 | 131072 | `nf_conntrack` 是一個模組，可追蹤 Linux 內 NAT 的連接專案。 此 `nf_conntrack` 模組會使用雜湊表來記錄 TCP 通訊協定的已 *建立連接* 記錄。 `nf_conntrack_max` 這是雜湊表中的節點數目上限，也就是模組所支援的最大連接數目 `nf_conntrack` 或連接追蹤資料表的大小。 | 
| `net.netfilter.nf_conntrack_buckets` | 65536-147456 | 65536 | `nf_conntrack` 是一個模組，可追蹤 Linux 內 NAT 的連接專案。 此 `nf_conntrack` 模組會使用雜湊表來記錄 TCP 通訊協定的已 *建立連接* 記錄。 `nf_conntrack_buckets` 這是雜湊表的大小。 | 

#### <a name="worker-limits"></a>背景工作角色限制

如同檔案描述項限制，進程可以建立的背景工作角色或執行緒數目會受到核心設定和使用者限制的限制。 AKS 上的使用者限制是無限制的。 

| 設定 | 允許的值/間隔 | 預設 | 描述 |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | 進程可以加速工作者執行緒。 可以建立的所有線程數目上限都是使用核心設定來設定 `kernel.threads-max` 。 | 

#### <a name="virtual-memory"></a>虛擬記憶體

您可以使用下列設定來微調 Linux 核心的虛擬記憶體 (VM) 子系統的作業，以及將中途 `writeout` 資料備份到磁片的操作。

| 設定 | 允許的值/間隔 | 預設 | 描述 |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530-262144 | 65530 | 此檔案包含進程可能具有的記憶體對應區域數目上限。 記憶體對應區域是用來做為直接呼叫、和的副作用， `malloc` `mmap` 也會 `mprotect` `madvise` 在載入共用程式庫時使用。 | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | 此百分比值控制核心回收記憶體的趨勢，而此記憶體是用來快取目錄和 inode 物件。 |
| `vm.swappiness` | 0 - 100 | 60 | 這個控制項是用來定義核心要如何交換記憶體頁面。 較高的值會增加提升的程度，較低的值會減少交換量。 值為0時，會指示核心不要起始交換，直到可用和檔案支援的頁面數量小於區域中的高水位線為止。 | 
| `swapFileSizeMB` | 1 MB- [暫存磁片](../virtual-machines/managed-disks-overview.md#temporary-disk) 的大小 (/dev/sdb)  | 無 | SwapFileSizeMB 指定將在此節點集區的代理程式節點上建立的交換檔案大小（以 MB 為單位）。 | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [透明 hugepage](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) 是一種 Linux 核心功能，可讓您更有效率地使用處理器的記憶體對應硬體，以改善效能。 啟用時，核心會盡可能嘗試配置 `hugepages` ，且如果 `mmap` 區域的大小為 2 mb，則任何 Linux 進程都會收到 2 mb 的頁面。 在某些情況下 `hugepages` ，當系統廣泛啟用時，應用程式最後可能會配置更多記憶體資源。 應用程式可能 `mmap` 是很大的區域，但只需要觸控1個位元組，在此情況下，可能會配置 2 MB 的頁面，而不是在沒有理由的情況下配置4k 頁面。 此案例是為什麼可以停 `hugepages` 用整個系統，或只在區域內有它們 `MADV_HUGEPAGE madvise` 。 | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | 此值控制核心是否應主動使用記憶體壓縮以提供更多 `hugepages` 可用。 | 

> [!IMPORTANT]
> 為了方便搜尋和可讀性，OS 設定會以名稱顯示在這份檔中，但應該使用 [camelCase 大寫慣例](https://docs.microsoft.com/dotnet/standard/design-guidelines/capitalization-conventions)新增至設定 json 檔案或 AKS API。

建立 `kubeletconfig.json` 具有下列內容的檔案：

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
建立 `linuxosconfig.json` 具有下列內容的檔案：

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

使用在上一個步驟中建立的 JSON 檔案，建立指定 kublet 和 OS 設定的新叢集。 

> [!NOTE]
> 當您建立叢集時，可以指定 kubelet 設定、OS 設定或兩者。 如果您在建立叢集時指定了設定，則只有初始節點集區中的節點會套用該設定。 未在 JSON 檔案中設定的任何設定都會保留預設值。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

使用您建立的 JSON 檔案，新增指定 Kubelet 參數的新節點集區。

> [!NOTE]
> 當您將節點集區新增至現有的叢集時，您可以指定 kubelet 設定、OS 設定或兩者。 如果您在新增節點集區時指定設定，則只有新節點集區中的節點會套用該設定。 未在 JSON 檔案中設定的任何設定都會保留預設值。

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>後續步驟

- 瞭解 [如何設定您的 AKS](cluster-configuration.md)叢集。
- 瞭解如何 [升級叢集中的節點映射](node-image-upgrade.md) 。
- 請參閱[升級 Azure Kubernetes Service (AKS) 叢集](upgrade-cluster.md)，了解如何將叢集升級至最新版本的 Kubernetes。
- 關於 AKS 常見問題的解答，請參閱 [AKS 的常見問題集](faq.md)。

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why