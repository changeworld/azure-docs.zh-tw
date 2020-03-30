---
title: 操作員最佳做法 - Azure Kubernetes Services (AKS) 中的叢集安全性
description: 了解叢集操作員在 Azure Kubernetes Service (AKS) 中管理叢集安全性和升級的最佳做法
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f02f6588946e2b63a1a092aba15603d1685e8207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594799"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中叢集安全性和升級的最佳做法

當您管理 Azure Kubernetes Service (AKS) 中的叢集時，工作負載和資料的安全性是主要考量。 尤其是在使用邏輯隔離執行多租用戶叢集時，您需要保護對資源和工作負載的存取。 為了降低攻擊的風險，您也需要確保您套用最新的 Kubernetes 和節點 OS 安全性更新。

本文著重在如何保護您的 AKS 叢集。 您會了解如何：

> [!div class="checklist"]
> * 使用 Azure Active Directory 和角色型存取控制來保護 API 伺服器存取
> * 保護容器對節點資源的存取
> * 將 AKS 叢集升級至最新版 Kubernetes
> * 使節點保持最新並自動應用安全修補程式

您也可以閱讀適用於[容器映像管理][best-practices-container-image-management]和 [Pod 安全性][best-practices-pod-security]的最佳做法。

您還可以使用 Azure [Kubernetes 服務與安全中心集成][security-center-aks]來説明檢測威脅並查看用於保護 AKS 群集的建議。

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>保護對 API 伺服器和叢集節點的存取

**最佳做法指導方針**：保護對 Kubernetes API 伺服器的存取是您要保護叢集可以執行的其中一項重要工作。 將 Kubernetes 角色型存取控制 (RBAC) 與 Azure Active Directory 整合可以控制對 API 伺服器的存取。 這些控制可讓您以保護 Azure 訂用帳戶存取的相同方式保護 AKS。

Kubernetes API 伺服器針對要求提供單一連線點，以在叢集內執行動作。 若要保護和稽核對 API 伺服器的存取，則必須限制存取並提供最低權限存取權限。 此方法不只適用於 Kubernetes，當 AKS 叢集是以邏輯方式隔離以供多租用戶使用時，此方法也特別重要。

Azure Active Directory (AD) 提供符合企業需求且能與 AKS 叢集整合的身分識別管理解決方案。 因為 Kubernetes 不提供身分識別管理解決方案，它也就難以提供精細的方式來限制對 API 伺服器的存取。 在 AKS 中使用與 Azure AD 整合的叢集，您就可以使用現有的使用者和群組帳戶，來對 API 伺服器驗證使用者。

![針對 AKS 叢集整合 Azure Active Directory](media/operator-best-practices-cluster-security/aad-integration.png)

使用 Kubernetes RBAC 和 Azure AD 整合來保護 API 伺服器，並提供存取限定範圍資源集合所需的最低權限，例如單一命名空間。 Azure AD 中的不同使用者或群組可被授與不同的 RBAC 角色。 這些細微的權限可讓您限制對 API 伺服器的存取，並提供所執行動作的清楚稽核記錄。

建議的最佳做法是使用群組提供檔案和資料夾的存取，而不使用個人身分識別來提供；使用 Azure AD「群組」** 成員資格將使用者繫結至 RBAC 角色，而非個別的「使用者」**。 由於使用者的群組成員資格會變更，其位在 AKS 叢集上的存取權限也會隨之變更。 如果您將使用者直接繫結在角色上，其工作職責可能會變更。 Azure AD 群組成員資格會更新，但 AKS 叢集上的權限不會反映該更新。 在這種情況下，使用者最後會被授與比所需更多的權限。

如需 Azure AD 整合和 RBAC 的詳細資訊，請參閱 [AKS 中驗證和授權的最佳做法][aks-best-practices-identity]。

## <a name="secure-container-access-to-resources"></a>保護容器對資源的存取

**最佳做法指導方針**：限制容器可執行的存取動作。 提供最低的權限，並且避免使用根/特殊權限提升。

就如同您應授與使用者或群組所需的最低權限一樣，也應限制容器只能執行所需的動作和處理。 為了將攻擊風險降到最低，請勿將應用程式和容器設定為需要提升權限或根存取。 例如，在 Pod 資訊清單中設定 `allowPrivilegeEscalation: false`。 這些「Pod 資訊安全內容」** 內建在 Kubernetes 中，並可讓您定義其他權限，例如要執行的使用者或群組身分，或要公開哪些 Linux 功能。 如需最佳做法詳細資訊，請參閱[保護 Pod 對資源的存取][pod-security-contexts]。

若要針對容器動作進行更細微的控制，您也可以使用內建的 Linux 安全性功能，例如 *AppArmor* 和 *Seccomp*。 這些功能在節點層級定義，然後透過 Pod 資訊清單實作。 內置 Linux 安全功能僅在 Linux 節點和 Pod 上可用。

> [!NOTE]
> 多租用戶如有惡意的使用，AKS 或其他位置中的 Kubernetes 環境就並不完全安全。 進一步其他安全功能 (例如 *AppArmor*、*seccomp*、*Pod 安全性原則*，或更精細的角色型存取控制 (RBAC)) 用於節點，可以提高攻擊的難度。 不過，在執行惡意的多租用戶工作負載時若要保有真正的安全性，Hypervisor 才是您唯一可信賴的安全性層級。 Kubernetes 的安全性網域會成為整個叢集，而非個別節點。 對於這些類型的惡意多租用戶工作負載，您應使用實際隔離的叢集。

### <a name="app-armor"></a>App Armor

若要限制容器可執行的動作，您可以使用 [AppArmor][k8s-apparmor] Linux 核心安全性模組。 AppArmor 提供用來作為基礎 AKS 節點 OS 的組件，且預設為啟用。 您可以建立 AppArmor 設定檔來限制動作 (例如讀取、寫入或執行)，或是限制系統功能 (例如裝載檔案系統)。 預設 AppArmor 設定檔會限制存取各種 `/proc` 和 `/sys` 位置，並提供方法以邏輯方式從基礎節點隔離容器。 AppArmor 適用於任何在 Linux 上執行的應用程式，而不只是 Kubernetes Pod。

![在 AKS 叢集中使用 AppArmor 設定檔限制容器動作](media/operator-best-practices-container-security/apparmor.png)

若要了解 AppArmor 如何運作，下列範例會建立防止寫入檔案的設定檔。 使用 [SSH][aks-ssh] 連線到 AKS 節點，然後建立名為 *deny-write.profile* 的檔案，並貼上下列內容：

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

使用 `apparmor_parser` 命令新增 AppArmor 設定檔。 將設定檔新增至 AppArmor，並指定在上個步驟中所建立之設定檔的名稱：

```console
sudo apparmor_parser deny-write.profile
```

如果設定檔已正確剖析且套用至 AppArmor，則不會傳回任何輸出。 您會返回命令提示字元。

現在，在本機電腦上建立名為 *aks-apparmor.yaml* 的 Pod 資訊清單，然後貼上下列內容。 此資訊清單會定義 `container.apparmor.security.beta.kubernetes` 的註釋，新增在上個步驟中建立的 *deny-write* 設定檔參考：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

使用 [kubectl apply][kubectl-apply] 命令部署範例 Pod：

```console
kubectl apply -f aks-apparmor.yaml
```

Pod 部署之後，使用 [kubectl exec][kubectl-exec] 命令來寫入檔案。 該命令無法執行，如下列範例輸出所示：

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

如需 AppArmor 的詳細資訊，請參閱 [Kubernetes 中的 AppArmor 設定檔][k8s-apparmor]。

### <a name="secure-computing"></a>安全運算

AppArmor 適用於任何的 Linux 應用程式，而 [Seccomp (安全運算****)][seccomp] 則適用於處理序層級。 Seccomp 也是 Linux 核心安全性模組，且 AKS 節點使用的 Docker 執行階段原生支援。 使用 Seccomp，容器可執行的處理序呼叫便會受限。 您要建立定義要允許或拒絕之動作的篩選，然後使用 Pod YAML 資訊清單中的註釋，來與 Seccomp 篩選建立關聯。 這符合僅授與容器執行所需之最低權限的最佳做法。

若要看 Seccomp 實際運作，請建立一個防止變更檔案權限的篩選。 使用 [SSH][aks-ssh] 連線到 AKS 節點，然後建立名為 */var/lib/kubelet/seccomp/prevent-chmod* 的 Seccomp 篩選，並貼上下列內容：

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

現在，在本機電腦上建立名為 *aks-seccomp.yaml* 的 Pod 資訊清單，然後貼上下列內容。 此資訊清單會定義 `seccomp.security.alpha.kubernetes.io` 的註釋，並參考在上個步驟中建立的 *prevent-chmod* 篩選：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

使用 [kubectl apply][kubectl-apply] 命令部署範例 Pod：

```console
kubectl apply -f ./aks-seccomp.yaml
```

使用 [kubectl get pods][kubectl-get] 命令檢視 Pod 的狀態。 Pod 會回報錯誤。 Seccomp 篩選會防止執行 `chmod` 命令，如下列範例輸出所示：

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

如需有關可用篩選的詳細資訊，請參閱[適用於 Docer 的 Seccomp 安全性設定檔][seccomp]。

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>定期更新至最新版的 Kubernetes

**最佳做法指導方針**：為了持續取得最新功能和錯誤 (bug) 修正，請定期將 AKS 叢集中的 Kubernetes 升級至最新版本。

Kubernetes 發行新功能的步調，比大部分傳統的基礎結構平台還快。 Kubernetes 更新包含新功能和錯誤 (bug) 或安全性修正。 新功能通常會會經過「Alpha 版本」**，接著「搶鮮版 (Beta)」**，然後才會成為「穩定版本」**，並提供給大眾和建議生產環境使用。 此發行週期應可讓您更新 Kubernetes，而不會定期發生中斷性變更，或調整部署和範本。

AKS 支援 Kubernetes 的四個次要版本。 這表示引入新的次要修補程式版本時，最舊的次要版本和支援的修補程式版本會被淘汰。 Kubernetes 的次要更新會定期發生。 請確定您有控管程序來檢查並視需要升級，如此才不會被排除在支援之外。 如需詳細資訊，請參閱[支援的 Kubernetes 版本 AKS][aks-supported-versions]

若要檢查可用於您叢集的版本，請使用 [az aks get-upgrades][az-aks-get-upgrades] 命令，如下列範例所示：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

接著，您可以使用 [az aks upgrade][az-aks-upgrade] 命令升級 AKS 叢集。 升級程序會依次安全地封鎖並清空一個節點，安排剩餘節點上的 Pod，然後部署執行最新版本 OS 和 Kubernetes 的新節點。

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

如需有關在 AKS 中升級的詳細資訊，請參閱 [AKS 中支援的 Kubernetes 版本][aks-supported-versions]和[升級 AKS 叢集][aks-upgrade]。

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>使用 kured 處理 Linux 節點更新和重新開機

**最佳實踐指南**- AKS 自動在每個 Linux 節點上下載並安裝安全修補程式，但必要時不會自動重新開機。 使用 `kured` 來監看擱置的重新啟動，然後安全地封鎖並清空節點，以讓節點重新啟動，套用更新，並盡可能如 OS 預期地安全。 對於 Windows Server 節點（當前在 AKS 中處於預覽狀態），定期執行 AKS 升級操作，以安全隔離和排空 pod 並部署更新的節點。

每天晚上，AKS 中的 Linux 節點都會通過其發行版本更新通道獲取可用的安全修補程式。 在節點部署到 AKS 叢集當中時，已自動設定此行為。 為了盡可能減少中斷，並降低對正在執行之工作負載的可能影響，如果安全性修補程式或核心更新需要重新啟動時，節點不會自動重新啟動。

Weaveworks 的開放原始碼 [Kured (KUbernetes REboot Daemon)][kured] 專案可監看擱置的節點重新啟動。 當 Linux 節點應用需要重新開機的更新時，該節點將安全封鎖並排空，以移動和安排群集中其他節點上的 pod。 一旦節點重新啟動之後，便會新增回叢集中，且 Kubernetes 會繼續安排其上的 Pod。 為了盡可能減少中斷，`kured` 一次只允許重新啟動一個節點。

![使用 Kured 的 AKS 節點重新啟動程序](media/operator-best-practices-cluster-security/node-reboot-process.png)

如果您想要更細微地控制重新啟動發生的事情，`kured` 可以與 Prometheus 整合，來防止重新啟動過程中可能發生的其他維護事件或叢集問題。 這項整合可以將重新啟動節點的額外複雜性降到最低，同時您可以針對其他問題主動進行疑難排解。

如需如何處理節點重新啟動的詳細資訊，請參閱[將安全性和核心更新套用至 AKS 中的節點][aks-kured]。

## <a name="next-steps"></a>後續步驟

本文著重在如何保護您的 AKS 叢集。 若要實作這些部分的一些內容，請參閱下列文章：

* [整合 Azure Active Directory 與 AKS][aks-aad]
* [將 AKS 叢集升級至最新版 Kubernetes][aks-upgrade]
* [使用 Kured 處理安全性更新和節點重新啟動][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: /azure/security-center/azure-kubernetes-service-integration
