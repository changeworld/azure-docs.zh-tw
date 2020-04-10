---
title: 在 Azure 庫伯奈斯服務 (AKS) 中使用窗格安全原則
description: 瞭解如何在 Azure 庫伯奈斯服務 (AKS) 中使用 PodSecurity 策略來控制 pod 准入
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998350"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>預覽 ─使用 Azure 函式庫伯奈斯服務 (AKS) 中的窗格安全原則保護群集

為了提高 AKS 群集的安全性,可以限制可以計劃哪些窗格。 請求不允許的資源的 Pod 無法在 AKS 群集中運行。 您可以使用 pod 安全原則定義此訪問。 本文介紹如何使用 pod 安全策略來限制在 AKS 中部署 pod。

> [!IMPORTANT]
> AKS 預覽功能是自助服務加入宣告。 預覽版提供「根據」和「可用」,不在服務層級協定和有限保修中。 在盡力的基礎上,客戶支援部分覆蓋了 AKS 預覽。 因此,這些功能不適合生產用途。 有關詳細資訊,請參閱以下支援文章:
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您需要 Azure CLI 版本 2.0.61 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

要使用 pod 安全原則,您需要*aks 預覽*CLI 擴展版本 0.4.1 或更高版本。 使用[az 延伸新增][az-extension-add]指令安裝*aks 預覽*Azure CLI 擴充,然後使用[az 延伸更新][az-extension-update]指令檢查任何可用的更新:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>註冊窗格安全原則功能提供者

要創建或更新 AKS 群集以使用 pod 安全原則,請先在訂閱上啟用功能標誌。 要註冊*PodSecurityPolicy 預覽*功能標誌,請使用[az 要素寄存器][az-feature-register]命令,如以下範例所示:

> [!CAUTION]
> 在訂閱上註冊功能時,當前無法取消註冊該功能。 啟用某些預覽功能後,可能會將預設值用於訂閱中創建的所有 AKS 群集。 不要在生產訂閱上啟用預覽功能。 使用單獨的訂閱測試預覽功能並收集反饋。

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

## <a name="overview-of-pod-security-policies"></a>Pod 安全原則概述

在 Kubernetes 群集中,在建立資源時,允許控制器用於攔截對 API 伺服器的請求。 然後,准入控制器可以根據一組規則*驗證*資源請求,或*更改*資源以更改部署參數。

*PodSecurityPolicy*是一個允許控制器,用於驗證 Pod 規範是否滿足您定義的要求。 這些要求可能會限制特權容器的使用、對某些類型的存儲的訪問,或者容器可以按該容器運行的使用者或組。 當您嘗試部署 pod 規範不符合 pod 安全原則中概述的要求的資源時,請求將被拒絕。 這種控制 AKS 群集中可以安排哪些 pod 的能力可防止某些可能的安全漏洞或許可權升級。

在 AKS 群集中啟用 pod 安全原則時,將應用一些預設策略。 這些預設策略提供了現成的體驗,用於定義可以計劃哪些窗格。 但是,群集使用者在定義自己的策略之前,在部署 pod 時可能會遇到問題。 建議的方法是:

* 建立 AKS 叢集
* 定義您自己的 pod 安全原則
* 開啟吊艙安全原則功能

為了顯示預設策略如何限制 pod 部署,在本文中,我們首先啟用 pod 安全策略功能,然後創建自定義策略。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>在 AKS 叢集上啟用 pod 安全原則

您可以使用[az aks 更新][az-aks-update]命令啟用或關閉 pod 安全原則。 下面的範例在名為*myResourceGroup*的資源組中的群組名稱*myAKSCluster*上啟用 pod 安全原則。

> [!NOTE]
> 對於實際使用,在定義自己的自定義策略之前,不要啟用 pod 安全策略。 在本文中,您將啟用 pod 安全原則作為查看預設策略如何限制 pod 部署的第一步。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>預設 AKS 政策

啟用 pod 安全原則時,AKS 將創建一個名為*特權 的*預設策略。 不要編輯或刪除預設策略。 相反,請創建自己的策略來定義要控制的設置。 讓我們先看看這些預設策略如何影響 pod 部署。

要檢視可用的策略,請使用[kubectl get psp][kubectl-get]命令,如以下範例所示

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*特權*pod 安全原則應用於 AKS 群集中的任何經過身份驗證的使用者。 此分配由群集角色和群集角色綁定控制。 使用[kubectl 取得角色繫結][kubectl-get]指令並搜尋*預設:特權:* 在*kube 系統*命名空間中的綁定:

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

如下壓縮輸出所示 *,psp:受限*群集角色分配給任何*系統:經過身份驗證*的使用者。 此功能提供基本級別的限制,而無需定義您自己的策略。

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

在開始創建自己的 pod 安全策略之前,瞭解這些預設策略如何與使用者請求交互以計劃 pod 非常重要。 在接下來的幾節中,讓我們安排一些窗格,以查看這些預設策略的運行情況。

## <a name="create-a-test-user-in-an-aks-cluster"></a>在 AKS 群組集中建立測試使用者

默認情況下,當您使用 az [aks 獲取憑據][az-aks-get-credentials]命令時,AKS 群集的*管理員*認證將添加到配置`kubectl`中。管理員用戶繞過了 pod 安全策略的強制。 如果對 AKS 群集使用 Azure 活動目錄集成,則可以使用非管理員使用者的認證登錄以查看策略的實施情況。 在本文中,讓我們在 AKS 群集中創建一個可以使用的測試使用者帳戶。

使用[kubectl 建立命名空間][kubectl-create]指令為測試資源創建名為*psp-aks*的範例命名空間。 然後,使用[kubectl 建立服務帳戶][kubectl-create]指令建立名為*非管理員使用者的*服務帳戶:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

接下來,為*非管理員使用者*創建角色綁定,以便使用[kubectl 建立角色綁定][kubectl-create]命令在命名空間中執行基本操作:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>管理員與非管理員使用者建立別名指令

要突出顯示使用`kubectl`時的常規管理員使用者與在前面的步驟中創建的非管理員使用者之間的差異,請創建兩個命令行別名:

* **kubectl-admin**別名適用於常規管理員使用者,並限定為*psp-aks*命名空間。
* **kubectl-非管理員用戶**別名適用於在上一步驟中創建*的非管理員使用者*,並限定為*psp-aks*命名空間。

建立以下兩個別名,如以下命令所示:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>測試建立權表窗格

讓我們首先測試在計劃具有的安全上下文的`privileged: true`窗格時會發生什麼情況。 此安全上下文會升級窗格的許可權。 在上一節顯示預設 AKS pod 安全原則時,*受限*策略應拒絕此請求。

建立名為`nginx-privileged.yaml`的檔案並貼上以下 YAML 清單:

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

使用[kubectl 應用程式][kubectl-apply]來建立 pod 並指定 YAML 清單的名稱:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

無法排程 Pod,例如以下範例輸出所示:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

pod 未達到計劃階段,因此在繼續之前沒有要刪除的資源。

## <a name="test-creation-of-an-unprivileged-pod"></a>測試建立無權限 podd

在前面的示例中,pod 規範請求特權升級。 預設*受限*pod安全原則拒絕此請求,因此無法計劃該窗格。 現在,讓我們嘗試運行同一 NGINX 窗格,而無需許可權升級請求。

建立名為`nginx-unprivileged.yaml`的檔案並貼上以下 YAML 清單:

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

使用[kubectl 應用程式][kubectl-apply]來建立 pod 並指定 YAML 清單的名稱:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

無法排程 Pod,例如以下範例輸出所示:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

pod 未達到計劃階段,因此在繼續之前沒有要刪除的資源。

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>測試使用特定使用者的檔案的 pod 的建立

在前面的範例中,容器映射自動嘗試使用root將NGINX綁定到埠 80。 默認*受限*pod安全策略拒絕了此請求,因此該窗格無法啟動。 現在,讓我們嘗試使用特定的使用者上下文(如`runAsUser: 2000`)運行同一 NGINX 窗格。

建立名為`nginx-unprivileged-nonroot.yaml`的檔案並貼上以下 YAML 清單:

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

使用[kubectl 應用程式][kubectl-apply]來建立 pod 並指定 YAML 清單的名稱:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

無法排程 Pod,例如以下範例輸出所示:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

pod 未達到計劃階段,因此在繼續之前沒有要刪除的資源。

## <a name="create-a-custom-pod-security-policy"></a>建立自訂 pod 安全原則

現在,您已經看到預設 pod 安全原則的行為,讓我們為*非管理員使用者*提供一種成功計劃 pod 的方法。

讓我們創建一個策略來拒絕請求特權訪問的窗格。 其他選項(如*runAsUser*或允許*的卷*)不會顯式限制。 這種類型的策略拒絕特權訪問請求,但允許群集運行請求的 pod。

建立名為`psp-deny-privileged.yaml`的檔案並貼上以下 YAML 清單:

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

使用[kubectl 應用程式][kubectl-apply]使用指令建立政策並指定 YAML 清單的名稱:

```console
kubectl apply -f psp-deny-privileged.yaml
```

要查看可用的策略,請使用[kubectl get psp][kubectl-get]命令,如以下範例所示。 將*psp 拒絕特權*策略與前面範例中為創建 pod 而強制執行的預設*受限*策略進行比較。 只有您的策略拒絕使用*PRIV*升級。 *對於 psp 拒絕特權*策略的使用者或組沒有限制。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>允許使用者帳戶使用自訂 pod 安全原則

在上一步中,您創建了一個 pod 安全原則來拒絕請求特權存取的 pod。 要允許使用策略,請建立*角色*或*群組角色*。 然後,使用*角色綁定*或*群集角色綁定*關聯這些角色之一。

在此範例中,創建一個群集角色,允許您*使用*上一步中創建的*psp 拒絕特權*策略。 建立名為`psp-deny-privileged-clusterrole.yaml`的檔案並貼上以下 YAML 清單:

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

使用[kubectl 應用程式][kubectl-apply]使用指令建立群組角色並指定 YAML 清單的名稱:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

現在創建一個群集角色綁定,以使用在上一步中創建的群集角色。 建立名為`psp-deny-privileged-clusterrolebinding.yaml`的檔案並貼上以下 YAML 清單:

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

使用[kubectl 應用程式][kubectl-apply]使用指令建立叢集角色的主用, 並指定 YAML 清單的名稱:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 在本文的第一步中,在 AKS 群集上啟用了 pod 安全策略功能。 建議的做法是僅在定義自己的策略後啟用 pod 安全策略功能。 這是啟用 pod 安全策略功能的階段。 已定義一個或多個自定義策略,並且使用者帳戶已與這些策略關聯。 現在,您可以安全地啟用 pod 安全策略功能,並將預設策略引起的問題降至最低。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>重新測試建立無特權 pod

應用自定義 pod 安全原則併為使用者帳戶使用該策略綁定後,讓我們嘗試再次創建無特權 pod。 使用相同的`nginx-privileged.yaml`清單使用[kubectl 應用程式][kubectl-apply]來建立 pod:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

已成功計劃窗格。 當您使用[kubectl 取得窗格][kubectl-get]指令檢查 pod 的狀態時,該窗格正在*執行*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

此示例演示如何創建自定義 pod 安全原則,以定義對不同使用者或組 AKS 群集的訪問。 默認 AKS 策略對哪些 pod 可以運行提供嚴格控制,因此請建立自己的自訂策略,然後正確定義所需的限制。

使用[kubectl 刪除][kubectl-delete]指令刪除 NGINX 無特權 pod 並指定 YAML 清單的名稱:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>清除資源

要關閉 pod 安全原則,請再次使用[az aks 更新][az-aks-update]命令。 以下範例關閉名為*myResourceGroup*的資源群組中的群組名稱*myAKSCluster*上的 pod 安全原則:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

接下來,刪除群集角色和群集角色綁定:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

使用[kubectl 刪除指令刪除][kubectl-delete]安全原則並指定 YAML 清單的名稱:

```console
kubectl delete -f psp-deny-privileged.yaml
```

最後,刪除*psp-aks*命名空間:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>後續步驟

本文介紹如何創建 pod 安全策略以防止使用特權訪問。 策略可以強制執行許多功能,例如卷類型或 RunAs 使用者。 有關可用選項的詳細資訊,請參閱[Kubernetes pod 安全原則參考文檔][kubernetes-policy-reference]。

有關限制 Pod 網路流量的詳細資訊,請參閱[使用 AKS 中的網路政策在 Pod 之間安全流量][network-policies]。

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
