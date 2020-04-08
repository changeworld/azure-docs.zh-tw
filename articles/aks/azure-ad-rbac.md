---
title: 對叢集使用 Azure AD 和 RBAC
titleSuffix: Azure Kubernetes Service
description: 瞭解如何使用 Azure 庫伯奈斯服務 (AKS) 中的基於角色的存取控制 (RBAC) 使用 Azure 活動目錄群組成員身份來限制對群集資源的存取
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: ad195085c049776bf0db418c57f2c72830f1adff
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803564"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>使用 Azure 庫伯奈斯服務中的基於角色的存取控制和 Azure 活動目錄識別控制對群集資源的存取

Azure Kubernetes Service (AKS) 可以設定為使用 Azure Active Directory (AD) 進行使用者驗證。 在此配置中,使用 Azure AD 身份驗證權杖登錄到 AKS 叢集。 您還可以配置基於 Kubernetes 角色的存取控制 (RBAC) 以限制基於用戶識別或組成員身份的群集資源的訪問。

本文介紹如何使用 Azure AD 組成員身份使用 AKS 群集中的 Kubernetes RBAC 控制對命名空間和群集資源的訪問。 範例群組和使用者在 Azure AD 建立,然後在 AKS 群集中建立角色和角色綁定,以授予創建和查看資源的相應許可權。

## <a name="before-you-begin"></a>開始之前

本文假定通過 Azure AD 整合啟用現有的 AKS 群集。 如果需要 AKS 群集,請參閱[將 Azure 活動目錄與 AKS 整合][azure-ad-aks-cli]。

您需要 Azure CLI 版本 2.0.61 或更高版本安裝和配置。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="create-demo-groups-in-azure-ad"></a>在 Azure AD 中建立展示群組

在本文中,讓我們創建兩個使用者角色,可用於顯示庫伯內斯 RBAC 和 Azure AD 如何控制對群集資源的訪問。 使用以下兩個範例角色:

* **應用程式開發人員**
    * 名為*aksdev*的使用者,它是*appdev*組的一部分。
* **現場可靠性工程師**
    * 名為*akssre*的使用者,該使用者是*opssre*組的一部分。

在生產環境中,可以使用 Azure AD 租戶中的現有使用者和組。

首先,使用[az aks show][az-aks-show]命令獲取 AKS 群集的資源 ID。 將資源 ID 分配給名為*AKS_ID*的變數,以便在其他命令中引用它。

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

使用[az 廣告組建立][az-ad-group-create]命令為應用程式開發人員創建 Azure AD 中的第一個範例群組。 下面的範例建立名為*appdev*的群組:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

現在,使用[az 角色分配創建][az-role-assignment-create]命令為*appdev*組創建 Azure 角色分配。 此分配允許組的任何成員使用`kubectl`通過授予它們 Azure *Kubernetes 服務群集使用者角色*來與 AKS 群集進行交互。

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> 如果收到錯誤(如`Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`,請等待 Azure AD 組物件 ID)在目錄中傳播幾秒鐘`az role assignment create`,然後重試該 命令。

建立第二個範例群組,此範例群組用於名為*opssre 的*SREs:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

同樣,創建 Azure 角色分配以授予組成員*Azure Kubernetes 服務群集使用者角色*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>在 Azure AD 建立展示使用者

在 Azure AD 中為應用程式開發人員和 SREs 創建了兩個範例組,現在讓我們創建兩個範例使用者。 要在本文末尾測試 RBAC 集成,請使用這些帳戶登錄到 AKS 群集。

使用[az 廣告使用者創建][az-ad-user-create]命令創建 Azure AD 中的第一個使用者帳戶。

下面的範例建立具有顯示名稱*AKS Dev*和的用戶主體名稱`aksdev@contoso.com`(UPN) 的使用者。 更新 UPN 以包括 Azure AD 租戶的已驗證網域(將*contoso.com*取代為您自己的`--password`網域),並提供您自己的安全 認證:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

現在,使用[az 廣告組成員添加][az-ad-group-member-add]指令將使用者添加到上一節中創建的*Appdev*群組:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

創建第二個使用者帳戶。 下面的範例建立一個具有顯示名稱*AKS SRE*與的使用者主體名稱 (UPN) 的使用者`akssre@contoso.com`。 同樣,更新 UPN 以包括 Azure AD 租戶的已驗證域(將*contoso.com*替換為您自己的域`--password`),並提供您自己的安全 認證:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>為應用程式開發建立 AKS 叢集資源

現在將創建 Azure AD 組和使用者。 為組成員創建了 Azure 角色分配,以便以常規使用者身份連接到 AKS 群集。 現在,讓我們配置 AKS 群集,以允許這些不同的組訪問特定資源。

首先,使用[az aks 獲取認證的][az-aks-get-credentials]命令獲取群集管理員認證。 在以下部分之一中,您將獲得常規*使用者*群集認證,以查看 Azure AD 身份驗證流的運行情況。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

使用[kubectl 建立命名空間][kubectl-create]命令在 AKS 群集中建立命名空間。 下面的範例建立命名空間*名稱開發*:

```console
kubectl create namespace dev
```

在 Kubernetes 中,*角色*定義授予的許可權,*角色綁定將*它們應用於所需的使用者或組。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

首先,為*開發*命名空間創建角色。 此角色授予命名空間的完整許可權。 在生產環境中,可以為不同的使用者或組指定更精細的許可權。

建立名為`role-dev-namespace.yaml`的檔案並貼上以下 YAML 清單:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

使用[kubectl 應用程式][kubectl-apply]指令建立角色並指定 YAML 清單的檔案名稱:

```console
kubectl apply -f role-dev-namespace.yaml
```

接下來,使用[az 廣告組顯示][az-ad-group-show]命令獲取*Appdev*組的資源 ID。 在下一步中,此組設置為角色綁定的主題。

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

現在,為*appdev*組創建角色綁定,以便使用以前創建的角色進行命名空間訪問。 建立名為 `rolebinding-dev-namespace.yaml` 的檔案，並貼上下列 YAML 資訊清單。 在最後一行,將*群組 ObjectId*取代為上一指令的組物件 ID 輸出:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

使用[kubectl 應用程式][kubectl-apply]指令建立角色繫結並指定 YAML 清單的檔名:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>建立 SR 建立 AKS 叢集資源

現在,重複前面的步驟,為 SR 創建命名空間、角色和角色綁定。

首先,使用[kubectl 建立命名空間][kubectl-create]指令為*sre*建立命名空間:

```console
kubectl create namespace sre
```

建立名為`role-sre-namespace.yaml`的檔案並貼上以下 YAML 清單:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

使用[kubectl 應用程式][kubectl-apply]指令建立角色並指定 YAML 清單的檔案名稱:

```console
kubectl apply -f role-sre-namespace.yaml
```

使用[az 廣告群組顯示][az-ad-group-show]指令取得*opssre*群組的資源代碼:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

為*操作組*創建角色綁定,以便使用以前創建的角色進行命名空間訪問。 建立名為 `rolebinding-sre-namespace.yaml` 的檔案，並貼上下列 YAML 資訊清單。 在最後一行,將*群組 ObjectId*取代為上一指令的組物件 ID 輸出:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

使用[kubectl 應用程式][kubectl-apply]指令建立角色繫結並指定 YAML 清單的檔名:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>使用 Azure AD 識別與叢集資源互動

現在,讓我們在創建和管理 AKS 群集中的資源時測試預期許可權的工作。 在這些範例中,您可以安排和查看使用者分配的命名空間中的窗格。 然後,您嘗試在分配的命名空間之外安排和查看窗格。

首先,使用[az aks 獲取憑據][az-aks-get-credentials]命令重置*kubeconfig*上下文。 在上一節中,使用群集管理員憑據設置上下文。 管理員用戶繞過 Azure AD 登錄提示。 如果沒有`--admin`參數,則應用使用者上下文,該上下文要求使用 Azure AD 對所有請求進行身份驗證。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

使用*開發*命名空間中的[kubectl 執行][kubectl-run]指令計劃基本 NGINX pod:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

作為登錄提示,輸入在文章開頭創建的您自己的`appdev@contoso.com`帳戶的認證。 成功登錄后,帳戶令牌將緩存以用於將來`kubectl`的命令。 NGINX 已成功計劃,如以下範例輸出所示:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

現在使用[kubectl 獲取窗格][kubectl-get]命令檢視*開發*命名空間中的窗格。

```console
kubectl get pods --namespace dev
```

如以下範例輸出所示,NGINX 窗格已成功*執行*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>在配置的名稱空間之外建立及檢視叢集資源

現在嘗試查看*開發*命名空間外部的窗格。 再次使用[kubectl 取得窗格][kubectl-get]指令,這一次`--all-namespaces`如下所示 :

```console
kubectl get pods --all-namespaces
```

使用者的組成員身份沒有允許此操作的 Kubernetes 角色,如以下範例輸出所示:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

同樣,嘗試在不同的命名空間(如*sre*命名空間)中安排一個窗格。 使用者的組成員身份與 Kubernetes 角色和角色綁定不對齊以授予這些許可權,如以下範例輸出所示:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>測試對 AKS 叢集資源的 SRE 存取

要確認我們的 Azure AD 組成員資格和 Kubernetes RBAC 在不同的使用者和組之間正常工作,請嘗試在以*操作使用者*身份登錄時嘗試前面的命令。

使用[az aks get 認證][az-aks-get-credentials]指令重置*kubeconfig*上下文,這個指令清除以前快取的*aksdev*使用者身份驗證權杖:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

嘗試在分配的*sre*命名空間中安排和查看窗格。 出現提示後,使用本文開頭創建`opssre@contoso.com`自己的憑據登錄:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

如以下範例輸出所示,您可以成功建立和檢視窗格:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

現在,嘗試在分配的 SRE 命名空間之外查看或計畫窗格:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

這些`kubectl`命令失敗,如以下範例輸出所示。 使用者的群組成員身份和 Kubernetes 角色和角色綁定不授予在其他命名空間中建立或管理資源的許可權:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>清除資源

在本文中,您在 AKS 群集中創建了資源,並在 Azure AD 中創建了使用者和組。 要清除所有這些資源,請執行以下命令:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>後續步驟

有關如何保護 Kubernetes 群集的詳細資訊,請參閱[AKS 的訪問和標識選項。][rbac-authorization]

有關識別和資源控制的最佳做法,請參閱[AKS 中身份驗證和授權的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
