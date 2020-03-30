---
title: 在 Azure 庫伯奈斯服務 （AKS） 中使用庫貝內斯服務 （AKS） 中更新和重新開機 Linux 節點
description: 瞭解如何更新 Linux 節點，並在 Azure 庫伯奈斯服務 （AKS） 中使用庫雷德自動重新開機它們
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 74b12c1bc6e2a88582cc357c8091b5590e6bf3cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191277"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>將安全和內核更新應用於 Azure 庫伯奈斯服務 （AKS） 中的 Linux 節點

為了保護群集，安全更新會自動應用於 AKS 中的 Linux 節點。 這些更新包括 OS 安全性修正或核心更新。 這其中有一些更新需要重新啟動節點，才能完成此程序。 AKS 不會自動重新開機這些 Linux 節點以完成更新過程。

使 Windows Server 節點（當前在 AKS 中預覽）保持最新的過程略有不同。 Windows 伺服器節點不會接收每日更新。 相反，您將執行 AKS 升級，該升級部署具有最新基本視窗伺服器映射和修補程式的新節點。 有關使用 Windows 伺服器節點的 AKS 群集，請參閱[升級 AKS 中的節點池][nodepool-upgrade]。

本文介紹如何使用開源[庫雷德（KUbernetes REboot守護進程）][kured]來監視需要重新開機的 Linux 節點，然後自動處理正在運行的 pod 和節點重新開機過程的重新調度。

> [!NOTE]
> `Kured` 是由 Weaveworks 所提供的開放原始碼專案。 我們會盡力在 AKS 中提供對此專案的支援。 可在#weave社區 Slack 通道中找到其他支援。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您還需要 Azure CLI 版本 2.0.59 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="understand-the-aks-node-update-experience"></a>了解 AKS 節點更新體驗

在 AKS 叢集中，您的 Kubernetes 節點會當作 Azure 虛擬機器 (VM) 來執行。 這些 Linux 型 VM 會使用 Ubuntu 映像，其 OS 已設定為每晚自動檢查更新。 如果有安全性或核心更新可供使用，就會自動下載並安裝它們。

![使用 Kured 的 AKS 節點更新並重新啟動程序](media/node-updates-kured/node-reboot-process.png)

某些安全性更新 (例如核心更新) 需要重新啟動節點，才能完成此程序。 需要重新開機的 Linux 節點創建一個名為 */var/運行/重新開機所需的*檔。 此重新啟動程序不會自動執行。

您可以使用自己的工作流程和程序來處理節點重新啟動，或使用 `kured` 來協調此程序。 使用`kured`時，將部署一個守護進程，該[DaemonSet][DaemonSet]在群集中的每個 Linux 節點上運行一個 pod。 守護程式中的這些窗格監視*存在 /var/運行/重新開機所需的*檔，然後啟動進程以重新開機節點。

### <a name="node-upgrades"></a>節點升級

AKS 中有一個額外的程序可讓您的「升級」** 叢集。 升級通常會移至較新版的 Kubernetes，而不只是套用節點安全性更新。 AKS 升級會執行下列動作：

* 利用已套用的最新安全性更新與 Kubernetes 版本來部署新節點。
* 舊節點會遭到封鎖並清空。
* 在新節點上為 Pod 設定排程。
* 刪除舊節點。

在升級事件期間，您不能保持在同一個 Kubernetes 版本。 您必須指定較新版的 Kubernetes。 若要升級至最新版的 Kubernetes，您可以[升級 AKS 叢集][aks-upgrade]。

## <a name="deploy-kured-in-an-aks-cluster"></a>在 AKS 叢集中部署 Kured

要部署`kured`守護進程集，請安裝以下官方的 Kured Helm 圖表。 這將創建角色和群集角色、綁定和服務帳戶，然後使用`kured`部署守護進程集。

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

您也可以設定適用於 `kured` 的其他參數，例如，與 Prometheus 或 Slack 整合。 有關其他配置參數的詳細資訊，請參閱[kured Helm 圖表][kured-install]。

## <a name="update-cluster-nodes"></a>更新叢集節點

預設情況下，AKS 中的 Linux 節點每天晚上檢查更新。 如果您不想等待，則可手動執行更新以檢查 `kured` 能夠正確執行。 首先，遵循步驟以 [SSH 到您的其中一個 AKS 節點][aks-ssh]。 與 Linux 節點建立 SSH 連接後，請檢查更新並應用如下：

```console
sudo apt-get update && sudo apt-get upgrade -y
```

如果已套用需要重新啟動節點的更新，即會將檔案寫入 */var/run/reboot-required*。 `Kured` 預設每隔 60 分鐘就會檢查需要重新啟動的節點。

## <a name="monitor-and-review-reboot-process"></a>監視和檢閱重新啟動程序

已偵測到 DaemonSet 內的其中一個複本需要重新啟動節點時，就會透過 Kubernetes API 在節點上放置一個鎖定。 此鎖定可防止在節點上為其他 Pod 設定排程。 此鎖定也指出一次應該只能重新啟動一個節點。 封鎖節點之後，即會清空執行中的 Pod，並將節點重新啟動。

您可以使用 [kubectl get nodes][kubectl-get-nodes] 命令來監視節點狀態。 下列範例輸出會在節點準備好進行重新啟動程序時，顯示狀態為 *SchedulingDisabled* 的節點：

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

當更新程序完成之後，您就可以使用 [kubectl get nodes][kubectl-get-nodes] 命令搭配 `--output wide` 參數來檢視節點狀態。 這個額外的輸出可讓您查看 *KERNEL-VERSION* 基礎節點中的差異，如下列範例輸出所示。 *aks-nodepool1-28993262-0*在上一步中更新，並顯示內核版本*4.15.0-1039-azure*。 尚未更新的節點*aks-nodepool1-28993262-1*顯示內核版本*4.15.0-1037-azure*。

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>後續步驟

本文詳細介紹了如何使用`kured`重新開機 Linux 節點作為安全更新過程的一部分。 若要升級至最新版的 Kubernetes，您可以[升級 AKS 叢集][aks-upgrade]。

有關使用 Windows 伺服器節點的 AKS 群集，請參閱[升級 AKS 中的節點池][nodepool-upgrade]。

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
