---
title: 在 Azure Kubernetes Service 中使用 pod 安全性原則（AKS）
description: 瞭解如何在 Azure Kubernetes Service 中使用 PodSecurityPolicy 控制 pod 許可（AKS）
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998350"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>預覽-在 Azure Kubernetes Service 中使用 pod 安全性原則保護您的叢集（AKS）

若要改善 AKS 叢集的安全性，您可以限制可以排程的 pod。 要求您不允許之資源的 pod 無法在 AKS 叢集中執行。 您可以使用 pod 安全性原則來定義此存取權。 本文說明如何使用 pod 安全性原則來限制 AKS 中的 pod 部署。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您需要安裝並設定 Azure CLI 版本2.0.61 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要使用 pod 安全性原則，您需要*aks-preview* CLI 擴充功能版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>註冊 pod 安全性原則功能提供者

若要建立或更新 AKS 叢集以使用 pod 安全性原則，請先在您的訂用帳戶上啟用功能旗標。 若要註冊*PodSecurityPolicyPreview*功能旗標，請使用[az feature register][az-feature-register]命令，如下列範例所示：

> [!CAUTION]
> 當您在訂用帳戶上註冊功能時，目前無法取消註冊該功能。 啟用一些預覽功能之後，預設值可能會用於在訂用帳戶中建立的所有 AKS 叢集。 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的訂用帳戶來測試預覽功能並收集意見反應。

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」**。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

準備就緒時，使用 [az provider register][az-provider-register] 命令重新整理 *Microsoft.ContainerService* 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 安全性原則總覽

在 Kubernetes 叢集中，當建立資源時，會使用「許可控制站」攔截對 API 伺服器的要求。 然後，「許可控制」可以針對一組規則來*驗證*資源要求，*或將*資源變動以變更部署參數。

*PodSecurityPolicy*是驗證 pod 規格符合您定義之需求的「許可控制」。 這些需求可能會限制使用特殊許可權的容器、存取特定類型的存放裝置，或容器可執行檔使用者或群組。 當您嘗試部署的資源中的 pod 規格不符合 pod 安全性原則中所述的需求時，就會拒絕該要求。 控制可在 AKS 叢集中排程哪些 pod 的功能，可防止一些可能的安全性弱點或許可權提升。

當您在 AKS 叢集中啟用 pod 安全性原則時，會套用一些預設原則。 這些預設原則提供現成的體驗，以定義可排程的 pod。 不過，在您定義自己的原則之前，叢集使用者可能會遇到部署 pod 的問題。 建議的方法是：

* 建立 AKS 叢集
* 定義您自己的 pod 安全性原則
* 啟用 pod 安全性原則功能

為了顯示預設原則如何限制 pod 部署，在本文中，我們會先啟用 pod 安全性原則功能，然後建立自訂原則。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>在 AKS 叢集上啟用 pod 安全性原則

您可以使用[az aks update][az-aks-update]命令來啟用或停用 pod 安全性原則。 下列範例會在名為*myResourceGroup*的資源群組中的叢集名稱*myAKSCluster*上啟用 pod 安全性原則。

> [!NOTE]
> 在實際使用的情況下，除非您已定義自己的自訂原則，否則請不要啟用 pod 安全性原則。 在本文中，您會啟用 pod 安全性原則作為第一個步驟，以查看預設原則如何限制 pod 部署。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>預設 AKS 原則

當您啟用 pod 安全性原則時，AKS 會建立一個名為 [特殊*許可權*] 的預設原則。 請勿編輯或移除預設原則。 相反地，請建立您自己的原則，以定義您想要控制的設定。 讓我們先看看這些預設原則對 pod 部署的影響為何。

若要查看可用的原則，請使用[kubectl get psp][kubectl-get]命令，如下列範例所示

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

具有特殊*許可權*的 pod 安全性原則會套用至 AKS 叢集中任何已驗證的使用者。 此指派是由 ClusterRoles 和 ClusterRoleBindings 所控制。 使用[kubectl get rolebindings][kubectl-get]命令，並在*kube 系統*命名空間中搜尋*default：具有許可權的：* binding：

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

如下列壓縮輸出所示， *psp：受限制*的 ClusterRole 會指派給任何*系統：已驗證*的使用者。 這項功能可提供基本層級的限制，而不需要定義您自己的原則。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

在您開始建立自己的 pod 安全性原則之前，請務必先瞭解這些預設原則如何與使用者要求進行排程。 在接下來的幾節中，讓我們來排程一些 pod，以查看這些預設原則的實際運作。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 叢集中建立測試使用者

根據預設，當您使用[az aks get-認證][az-aks-get-credentials]命令時，aks 叢集的系統*管理員*認證會新增至您`kubectl`的設定。系統管理員使用者略過 pod 安全性原則的強制執行。 如果您使用 AKS 叢集的 Azure Active Directory 整合，您可以使用非系統管理員使用者的認證來登入，以查看原則的強制執行。 在本文中，我們將在您可以使用的 AKS 叢集中建立測試使用者帳戶。

使用[kubectl create namespace][kubectl-create]命令，為測試資源建立名為*psp aks*的範例命名空間。 然後，使用[kubectl create serviceaccount][kubectl-create]命令，建立名為*nonadmin 的*服務帳戶：

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下來，使用[kubectl create 接著][kubectl-create]命令建立*Nonadmin 使用者*的接著，以在命名空間中執行基本動作：

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>為系統管理員和非系統管理員使用者建立別名命令

若要在使用`kubectl`和先前步驟中建立的非系統管理員使用者時，反白顯示一般系統管理使用者之間的差異，請建立兩個命令列別名：

* **Kubectl-admin**別名適用于一般管理使用者，且範圍設定為*psp aks*命名空間。
* **Kubectl-nonadminuser**別名適用于在上一個步驟中建立的*nonadmin 使用者*，且範圍限定為*psp aks*命名空間。

建立這兩個別名，如下列命令所示：

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>測試特殊許可權 pod 的建立

讓我們先測試當您使用的安全性內容來排程 pod 時，會`privileged: true`發生什麼事。 此安全性內容會擴大 pod 的許可權。 在上一節中，顯示預設 AKS pod 安全性原則時，*受限制*的原則應該會拒絕此要求。

建立名為`nginx-privileged.yaml`的檔案，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

使用[kubectl apply][kubectl-apply]命令建立 pod，並指定 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

無法排程 pod，如下列範例輸出所示：

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod 不會到達排程階段，因此在您繼續之前，不會刪除任何資源。

## <a name="test-creation-of-an-unprivileged-pod"></a>測試建立不具特殊許可權的 pod

在上述範例中，pod 規格要求特殊許可權擴大。 預設*限制*pod 安全性原則會拒絕此要求，因此無法排程 pod。 現在，讓我們試著在沒有許可權擴大要求的情況下，執行相同的 NGINX pod。

建立名為`nginx-unprivileged.yaml`的檔案，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

使用[kubectl apply][kubectl-apply]命令建立 pod，並指定 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

無法排程 pod，如下列範例輸出所示：

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod 不會到達排程階段，因此在您繼續之前，不會刪除任何資源。

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>使用特定使用者內容來測試 pod 的建立

在上述範例中，容器映射會自動嘗試使用 root 來將 NGINX 系結至埠80。 預設*限制*pod 安全性原則拒絕此要求，因此無法啟動 pod。 現在，讓我們試著使用特定的使用者內容來執行相同的 NGINX pod `runAsUser: 2000`，例如。

建立名為`nginx-unprivileged-nonroot.yaml`的檔案，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

使用[kubectl apply][kubectl-apply]命令建立 pod，並指定 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

無法排程 pod，如下列範例輸出所示：

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod 不會到達排程階段，因此在您繼續之前，不會刪除任何資源。

## <a name="create-a-custom-pod-security-policy"></a>建立自訂 pod 安全性原則

既然您已瞭解預設 pod 安全性原則的行為，讓我們為*nonadmin 使用者*提供一種方式來成功排程 pod。

讓我們建立一個原則來拒絕要求特殊許可權存取的 pod。 其他選項（例如*runAsUser*或允許的*磁片*區）並未明確受到限制。 這種原則會拒絕特殊許可權存取的要求，否則會讓叢集執行要求的 pod。

建立名為`psp-deny-privileged.yaml`的檔案，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

使用[kubectl apply][kubectl-apply]命令建立原則，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged.yaml
```

若要查看可用的原則，請使用[kubectl get psp][kubectl-get]命令，如下列範例所示。 將*psp-拒絕許可權*原則與先前範例中強制執行的預設*限制*原則做比較，以建立 pod。 您的原則只會拒絕使用*特權*擴大。 對*psp-拒絕許可權*原則的使用者或群組沒有任何限制。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允許使用者帳戶使用自訂 pod 安全性原則

在上一個步驟中，您已建立 pod 安全性原則來拒絕要求特殊許可權存取的 pod。 若要允許使用原則，您可以建立*角色*或*ClusterRole*。 然後，您可以使用*接著*或*ClusterRoleBinding*來建立其中一個角色的關聯。

在此範例中，建立可讓您*使用*在上一個步驟中建立之*psp-拒絕許可權*原則的 ClusterRole。 建立名為`psp-deny-privileged-clusterrole.yaml`的檔案，並貼上下列 YAML 資訊清單：

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

使用[kubectl apply][kubectl-apply]命令來建立 ClusterRole，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

現在，請建立 ClusterRoleBinding 以使用在上一個步驟中建立的 ClusterRole。 建立名為`psp-deny-privileged-clusterrolebinding.yaml`的檔案，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

使用[kubectl apply][kubectl-apply]命令來建立 ClusterRoleBinding，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在本文的第一個步驟中，已在 AKS 叢集上啟用 pod 安全性原則功能。 建議的作法是只在定義您自己的原則之後，才啟用 pod 安全性原則功能。 這是您要啟用 pod 安全性原則功能的階段。 已定義一或多個自訂原則，且使用者帳戶已與這些原則相關聯。 現在您可以安全地啟用 pod 安全性原則功能，並將預設原則所造成的問題降到最低。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>再次測試不具特殊許可權的 pod 建立

套用您的自訂 pod 安全性原則和使用者帳戶的系結以使用原則時，讓我們再次嘗試建立無特殊許可權的 pod。 使用相同`nginx-privileged.yaml`的資訊清單，使用[kubectl apply][kubectl-apply]命令來建立 pod：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

已成功排程 pod。 當您使用[kubectl get][kubectl-get] pod 命令檢查 pod 的*狀態時，pod 正在執行：*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

這個範例會示範如何建立自訂 pod 安全性原則，以定義不同使用者或群組的 AKS 叢集存取權。 預設的 AKS 原則會對 pod 可以執行的內容提供緊密的控制，因此，請建立您自己的自訂原則，然後正確地定義所需的限制。

使用[kubectl delete][kubectl-delete]命令刪除 NGINX 無許可權 pod，並指定 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清除資源

若要停用 pod 安全性原則，請再次使用[az aks update][az-aks-update]命令。 下列範例會在名為*myResourceGroup*的資源群組中，停用叢集名稱*myAKSCluster*上的 pod 安全性原則：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

接下來，刪除 ClusterRole 和 ClusterRoleBinding：

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

使用[kubectl delete][kubectl-delete]命令刪除安全性原則，並指定 YAML 資訊清單的名稱：

```console
kubectl delete -f psp-deny-privileged.yaml
```

最後，刪除*psp aks*命名空間：

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>後續步驟

本文說明如何建立 pod 安全性原則，以防止使用特殊許可權存取。 原則可以強制執行許多功能，例如磁片區類型或 RunAs 使用者。 如需可用選項的詳細資訊，請參閱[Kubernetes pod 安全性原則參考][kubernetes-policy-reference]檔。

如需限制 pod 網路流量的詳細資訊，請參閱[在 AKS 中使用網路原則來保護 pod 之間的流量][network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
