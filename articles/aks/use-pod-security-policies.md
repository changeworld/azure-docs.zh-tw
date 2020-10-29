---
title: 在 Azure Kubernetes Service (AKS) 中使用 pod 安全性原則
description: '瞭解如何在 Azure Kubernetes Service (AKS 中使用 PodSecurityPolicy 控制 pod 招生) '
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: a9f6ead7edea7a3a6240e116d3073ea01fa9f6bb
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900104"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>預覽-在 Azure Kubernetes Service (AKS) 中使用 pod 安全性原則來保護叢集

> [!WARNING]
> **這份檔中所述的功能（pod 安全性原則 (preview) ）已設定為取代，並將在2021年2月1日之後不再提供使用** ，以利 [AKS 的 Azure 原則](use-pod-security-on-azure-policy.md)。 淘汰日期已從2020年10月15日的先前日期延長。
>
> 淘汰 Pod 安全性原則 (預覽) 之後，您必須在任何使用已淘汰功能的現有叢集上停用此功能，以執行未來的叢集升級並保留在 Azure 支援的範圍中。
>
> 強烈建議您使用 Azure 原則 for AKS 來開始測試案例，其提供內建原則來保護 pod 和內建的應用程式，以對應至 pod 安全性原則。 按一下這裡以瞭解如何 [從 pod 安全性原則 (preview) 遷移至 Azure 原則 ](use-pod-security-on-azure-policy.md#migrate-from-kubernetes-pod-security-policy-to-azure-policy)。

若要改善 AKS 叢集的安全性，您可以限制可排程的 pod。 要求不允許資源的 pod 無法在 AKS 叢集中執行。 您可以使用 pod 安全性原則來定義此存取權。 本文說明如何使用 pod 安全性原則來限制 AKS 中的 pod 部署。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您必須安裝並設定 Azure CLI 版本 2.0.61 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 延伸模組

若要使用 pod 安全性原則，您需要 *aks-preview* CLI 擴充功能版本0.4.1 或更高版本。 請使用 [az extension add][az-extension-add] 命令安裝 aks-preview Azure CLI 擴充功能，然後使用 [az extension update][az-extension-update] 命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>註冊 pod 安全性原則功能提供者

**本檔和功能已在2020年10月15日淘汰。**

若要建立或更新 AKS 叢集以使用 pod 安全性原則，請先在您的訂用帳戶上啟用功能旗標。 若要註冊 *PodSecurityPolicyPreview* 功能旗標，請使用 [az feature register][az-feature-register] 命令，如下列範例所示：

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」  。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

當您準備好時，請使用 [az provider register][az-provider-register]命令重新整理 *>microsoft.containerservice* 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 安全性原則總覽

在 Kubernetes 叢集中，當建立資源時，會使用「許可控制器」來攔截對 API 伺服器的要求。 然後，「許可控制器」可以根據一組規則來 *驗證* 資源要求， *或變更* 資源以變更部署參數。

*PodSecurityPolicy* 是一種可驗證 pod 規格符合您定義之需求的許可控制器。 這些需求可能會限制使用具特殊許可權的容器、特定儲存體類型的存取權，或容器可執行檔使用者或群組。 當您嘗試部署的資源中的 pod 規格不符合 pod 安全性原則中所述的需求時，就會拒絕要求。 這項功能可控制 AKS 叢集中可排程的 pod，以防止某些可能的安全性弱點或許可權提升。

當您在 AKS 叢集中啟用 pod 安全性原則時，會套用一些預設原則。 這些預設原則會提供現成的體驗，以定義可以排程的 pod。 不過，在您定義自己的原則之前，叢集使用者可能會遇到部署 pod 的問題。 建議的方法是：

* 建立 AKS 叢集
* 定義您自己的 pod 安全性原則
* 啟用 pod 安全性原則功能

為了顯示預設原則如何限制 pod 部署，在本文中，我們會先啟用 pod 安全性原則功能，然後建立自訂原則。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>在 AKS 叢集上啟用 pod 安全性原則

您可以使用 [az aks update][az-aks-update] 命令來啟用或停用 pod 安全性原則。 下列範例會在名為 *myResourceGroup* 的資源群組中，于叢集名稱 *myAKSCluster* 上啟用 pod 安全性原則。

> [!NOTE]
> 在實際使用的情況下，除非您已定義自己的自訂原則，否則請勿啟用 pod 安全性原則。 在本文中，您會啟用 pod 安全性原則作為第一個步驟，以查看預設原則如何限制 pod 部署。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>預設 AKS 原則

當您啟用 pod 安全性原則時，AKS 會建立一個名為「特殊 *許可權* 」的預設原則。 請勿編輯或移除預設原則。 相反地，請建立您自己的原則來定義您想要控制的設定。 讓我們先看看這些預設原則如何影響 pod 部署。

若要查看可用的原則，請使用 [kubectl 取得 psp][kubectl-get] 命令，如下列範例所示

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

具有特殊 *許可權* 的 pod 安全性原則會套用至 AKS 叢集中任何已驗證的使用者。 這項指派是由 ClusterRoles 和 ClusterRoleBindings 所控制。 使用 [kubectl get rolebindings][kubectl-get]命令，並在 *kube-system* 命名空間中搜尋 *default：特殊許可權：* binding：

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

如下列壓縮輸出所示， *psp：特殊許可權* ClusterRole 會指派給任何 *系統：已驗證* 的使用者。 這項功能提供基本層級的許可權，而不需要定義您自己的原則。

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

請務必瞭解這些預設原則如何與使用者要求互動以排程 pod，然後再開始建立自己的 pod 安全性原則。 在接下來的幾節中，我們將排程一些 pod 來查看這些預設原則的作用。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 叢集中建立測試使用者

依預設，當您使用 [az aks 取得認證][az-aks-get-credentials] 命令時，會將 aks 叢集的系統 *管理員* 認證新增至您的設定 `kubectl` 。系統管理員使用者略過 pod 安全性原則的強制執行。 如果您使用 AKS 叢集的 Azure Active Directory 整合，您可以使用非系統管理員使用者的認證登入，以查看強制執行原則的動作。 在本文中，我們將在 AKS 叢集中建立可供您使用的測試使用者帳戶。

使用 [kubectl create namespace][kubectl-create]命令建立名為 *psp-aks* 的範例命名空間，以供測試資源使用。 然後，使用 [kubectl create serviceaccount][kubectl-create]命令建立名為 *nonadmin 的* 服務帳戶：

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下來，使用 [kubectl create RoleBinding][kubectl-create]命令建立 *Nonadmin 使用者* 的 RoleBinding，以在命名空間中執行基本動作：

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>為系統管理員和非系統管理員使用者建立別名命令

若要反白顯示一般系統管理使用者在使用 `kubectl` 和先前步驟中建立的非系統管理員使用者之間的差異，請建立兩個命令列別名：

* **Kubectl-admin** 別名適用于一般系統管理使用者，且範圍設定為 *psp aks* 命名空間。
* **Kubectl-nonadminuser** 別名適用于在上一個步驟中建立的 *nonadmin 使用者* ，且範圍設定為 *psp aks* 命名空間。

建立這兩個別名，如下列命令所示：

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>測試特殊許可權 pod 的建立

讓我們先測試當您排程具有安全性內容的 pod 時，會發生什麼事 `privileged: true` 。 此安全性內容會升級 pod 的許可權。 在顯示預設 AKS pod 安全性原則的上一節中， *許可權* 原則應該拒絕此要求。

建立名為的檔案 `nginx-privileged.yaml` ，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

使用 [kubectl apply][kubectl-apply] 命令建立 pod，並指定 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Pod 無法排程，如下列範例輸出所示：

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod 不會到達排程階段，因此在您繼續之前，不需要刪除任何資源。

## <a name="test-creation-of-an-unprivileged-pod"></a>無特殊許可權 pod 的測試建立

在上述範例中，pod 規格已要求授權擴大。 預設 *許可權* pod 安全性原則會拒絕此要求，所以 pod 無法排程。 現在，讓我們試著在沒有許可權擴大要求的情況下，執行相同的 NGINX pod。

建立名為的檔案 `nginx-unprivileged.yaml` ，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

使用 [kubectl apply][kubectl-apply] 命令建立 pod，並指定 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod 無法排程，如下列範例輸出所示：

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod 不會到達排程階段，因此在您繼續之前，不需要刪除任何資源。

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>使用特定使用者內容來測試建立 pod

在上述範例中，容器映射會自動嘗試使用 root 將 NGINX 系結到埠80。 預設 *許可權* pod 安全性原則已拒絕此要求，所以 pod 無法啟動。 現在，讓我們試著使用特定的使用者內容（例如）來執行相同的 NGINX pod `runAsUser: 2000` 。

建立名為的檔案 `nginx-unprivileged-nonroot.yaml` ，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

使用 [kubectl apply][kubectl-apply] 命令建立 pod，並指定 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Pod 無法排程，如下列範例輸出所示：

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod 不會到達排程階段，因此在您繼續之前，不需要刪除任何資源。

## <a name="create-a-custom-pod-security-policy"></a>建立自訂 pod 安全性原則

現在您已瞭解預設 pod 安全性原則的行為，讓我們為 *nonadmin 使用者* 提供一種方式來成功排程 pod。

讓我們建立原則來拒絕要求特殊許可權存取的 pod。 其他選項，例如 *runAsUser* 或允許的 *磁片* 區，則不會受到明確限制。 這種類型的原則會拒絕特殊許可權存取的要求，否則會讓叢集執行要求的 pod。

建立名為的檔案 `psp-deny-privileged.yaml` ，並貼上下列 YAML 資訊清單：

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

使用 [kubectl apply][kubectl-apply] 命令來建立原則，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged.yaml
```

若要查看可用的原則，請使用 [kubectl 取得 psp][kubectl-get] 命令，如下列範例所示。 將 *psp-拒絕許可權* 原則與先前範例中強制執行的預設 *許可權* 原則進行比較，以建立 pod。 您的原則將不會拒絕使用「 *特權* 提升」。 *Psp-拒絕許可權* 原則的使用者或群組沒有任何限制。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允許使用者帳戶使用自訂 pod 安全性原則

在上一個步驟中，您已建立 pod 安全性原則來拒絕要求特殊許可權存取的 pod。 若要允許使用原則，您可以建立 *角色* 或 *ClusterRole* 。 然後，您可以使用 *RoleBinding* 或 *ClusterRoleBinding* 讓這些角色的其中一個產生關聯。

在此範例中，請建立一個 ClusterRole，讓您 *使用* 在上一個步驟中建立的 *psp-拒絕許可權* 原則。 建立名為的檔案 `psp-deny-privileged-clusterrole.yaml` ，並貼上下列 YAML 資訊清單：

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

使用 [kubectl apply][kubectl-apply] 命令建立 ClusterRole，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

現在建立 ClusterRoleBinding，以使用在上一個步驟中建立的 ClusterRole。 建立名為的檔案 `psp-deny-privileged-clusterrolebinding.yaml` ，並貼上下列 YAML 資訊清單：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
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

使用 [kubectl apply][kubectl-apply] 命令建立 ClusterRoleBinding，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在本文的第一個步驟中，已在 AKS 叢集中啟用 pod 安全性原則功能。 建議的作法是在您定義自己的原則之後，才啟用 pod 安全性原則功能。 這是您要啟用 pod 安全性原則功能的階段。 已定義一或多個自訂原則，且使用者帳戶已與這些原則相關聯。 現在您可以安全地啟用 pod 安全性原則功能，並將預設原則所造成的問題降至最低。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>再次測試建立無特殊許可權的 pod

套用您的自訂 pod 安全性原則和使用者帳戶的系結來使用原則後，讓我們再次嘗試建立無特殊許可權的 pod。 使用相同 `nginx-privileged.yaml` 的資訊清單來建立使用 [kubectl apply][kubectl-apply] 命令的 pod：

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod 已成功排程。 當您使用 [kubectl get][kubectl-get] pod 命令檢查 pod 的 *狀態時，pod 正在執行：*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

此範例說明如何建立自訂 pod 安全性原則，以定義不同使用者或群組對 AKS 叢集的存取。 預設的 AKS 原則可讓您控制 pod 可執行檔內容，因此請建立您自己的自訂原則，以正確地定義您需要的限制。

使用 [kubectl delete][kubectl-delete] 命令刪除 NGINX 無特殊許可權的 pod，並指定 YAML 資訊清單的名稱：

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清除資源

若要停用 pod 安全性原則，請再次使用 [az aks update][az-aks-update] 命令。 下列範例會在名為 *myResourceGroup* 的資源群組中，停用叢集名稱 *myAKSCluster* 上的 pod 安全性原則：

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

使用 [kubectl delete][kubectl-delete] 命令刪除安全性原則，並指定 YAML 資訊清單的名稱：

```console
kubectl delete -f psp-deny-privileged.yaml
```

最後，刪除 *psp aks* 命名空間：

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>後續步驟

本文說明如何建立 pod 安全性原則，以防止使用特殊許可權存取。 原則可強制執行的功能有很多，例如磁片區類型或 RunAs 使用者。 如需可用選項的詳細資訊，請參閱 [Kubernetes pod 安全性原則參考][kubernetes-policy-reference]檔。

如需限制 pod 網路流量的詳細資訊，請參閱 [在 AKS 中使用網路原則來保護 pod 之間的流量][network-policies]。

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
