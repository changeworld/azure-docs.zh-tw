---
title: 使用叢集的 Azure AD 和 RBAC
titleSuffix: Azure Kubernetes Service
description: 瞭解如何使用 Azure Kubernetes Service （AKS）中的角色型存取控制（RBAC），以 Azure Active Directory 群組成員資格來限制對叢集資源的存取
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 646b1b5fb5079f0b959aaa2337c1dbab09ff4134
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87057334"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>使用 Azure Kubernetes Service 中的角色型存取控制和 Azure Active Directory 身分識別，來控制叢集資源的存取權

Azure Kubernetes Service (AKS) 可以設定為使用 Azure Active Directory (AD) 進行使用者驗證。 在此設定中，您會使用 Azure AD 驗證權杖來登入 AKS 叢集。 您也可以設定 Kubernetes 角色型存取控制（RBAC），以根據使用者的身分識別或群組成員資格來限制叢集資源的存取權。

本文說明如何使用 Azure AD 群組成員資格，在 AKS 叢集中使用 Kubernetes RBAC 來控制命名空間和叢集資源的存取權。 範例群組和使用者會在 Azure AD 中建立，然後在 AKS 叢集中建立角色和 RoleBindings，以授與建立和查看資源的適當許可權。

## <a name="before-you-begin"></a>開始之前

本文假設您已使用 Azure AD 整合啟用了現有的 AKS 叢集。 如果您需要 AKS 叢集，請參閱[整合 Azure Active Directory 與 AKS][azure-ad-aks-cli]。

您必須安裝並設定 Azure CLI 版本 2.0.61 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="create-demo-groups-in-azure-ad"></a>在 Azure AD 中建立示範群組

在本文中，我們將建立兩個可用來顯示 Kubernetes RBAC 和 Azure AD 如何控制叢集資源存取權的使用者角色。 使用下列兩個範例角色：

* **應用程式開發人員**
    * 名為*aksdev*的使用者，屬於*appdev*群組的一部分。
* **網站可靠性工程師**
    * 名為*akssre*的使用者，屬於*opssre*群組的一部分。

在生產環境中，您可以使用 Azure AD 租使用者內的現有使用者和群組。

首先，使用[az AKS show][az-aks-show]命令取得 AKS 叢集的資源識別碼。 將資源識別碼指派給名為*AKS_ID*的變數，讓它可以在其他命令中參考。

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

使用[az AD group create][az-ad-group-create]命令，為應用程式開發人員建立 Azure AD 中的第一個範例群組。 下列範例會建立名為*appdev*的群組：

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

現在，使用[az role 指派 create][az-role-assignment-create]命令來建立*Appdev*群組的 Azure 角色指派。 此指派可讓群組的任何成員使用 `kubectl` *Azure Kubernetes Service 叢集使用者角色*，來與 AKS 叢集互動。

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> 如果您收到類似的錯誤 `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` ，請等候幾秒鐘，讓 Azure AD 群組物件識別碼傳播到目錄中，然後 `az role assignment create` 再次嘗試命令。

建立第二個範例群組，這是名為*opssre*的 sre：

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

再次建立 Azure 角色指派，以將*Azure Kubernetes Service 叢集使用者角色*授與群組的成員：

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>在 Azure AD 中建立示範使用者

透過在 Azure AD 中為應用程式開發人員和 Sre 建立的兩個範例群組，現在讓我們建立兩個範例使用者。 若要測試本文結尾的 RBAC 整合，您可以使用這些帳戶來登入 AKS 叢集。

使用[az AD user create][az-ad-user-create]命令，在 Azure AD 中建立第一個使用者帳戶。

下列範例會建立使用者，其顯示名稱為*AKS Dev* ，使用者主體名稱（UPN）為 `aksdev@contoso.com` 。 更新 UPN 以包含 Azure AD 租使用者的已驗證網域（將*contoso.com*取代為您自己的網域），並提供您自己的安全 `--password` 認證：

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

現在，使用[az ad group member add][az-ad-group-member-add]命令，將使用者新增到上一節中建立的*appdev*群組：

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

建立第二個使用者帳戶。 下列範例會建立使用者，其顯示名稱為*AKS SRE* ，而使用者主體名稱（UPN）為 `akssre@contoso.com` 。 同樣地，更新 UPN 以包含 Azure AD 租使用者的已驗證網域（將*contoso.com*取代為您自己的網域），並提供您自己的安全 `--password` 認證：

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>建立應用程式開發人員的 AKS 叢集資源

現在會建立 Azure AD 群組和使用者。 系統會為群組成員建立 Azure 角色指派，以一般使用者身分連線至 AKS 叢集。 現在，讓我們設定 AKS 叢集，以允許這些不同的群組存取特定資源。

首先，使用[az aks get-認證][az-aks-get-credentials]命令取得叢集系統管理員認證。 在下列其中一節中，您會取得一般*使用者*叢集認證，以查看作用中的 Azure AD 驗證流程。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

使用[kubectl create namespace][kubectl-create]命令，在 AKS 叢集中建立命名空間。 下列範例會建立命名空間名稱*dev*：

```console
kubectl create namespace dev
```

在 Kubernetes 中，*角色*會定義要授與的許可權，而*RoleBindings*則會將其套用至所需的使用者或群組。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

首先，建立*dev*命名空間的角色。 此角色會授與命名空間的完整許可權。 在生產環境中，您可以為不同的使用者或群組指定更細微的許可權。

建立名為的檔案 `role-dev-namespace.yaml` ，並貼上下列 YAML 資訊清單：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
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

使用[kubectl apply][kubectl-apply]命令建立角色，並指定 YAML 資訊清單的檔案名：

```console
kubectl apply -f role-dev-namespace.yaml
```

接下來，使用[az ad group show][az-ad-group-show]命令來取得*appdev*群組的資源識別碼。 在下一個步驟中，此群組會設定為接著的主體。

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

現在，建立*appdev*群組的接著，以使用先前為命名空間存取所建立的角色。 建立名為 `rolebinding-dev-namespace.yaml` 的檔案，並貼上下列 YAML 資訊清單。 在最後一行中，將*g i d*取代為上一個命令中的群組物件識別碼輸出：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
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

使用[kubectl apply][kubectl-apply]命令來建立接著，並指定 YAML 資訊清單的檔案名：

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>建立 Sre 的 AKS 叢集資源

現在，重複上述步驟來建立 Sre 的命名空間、角色和接著。

首先，使用[kubectl create namespace][kubectl-create]命令來建立*sre*的命名空間：

```console
kubectl create namespace sre
```

建立名為的檔案 `role-sre-namespace.yaml` ，並貼上下列 YAML 資訊清單：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
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

使用[kubectl apply][kubectl-apply]命令建立角色，並指定 YAML 資訊清單的檔案名：

```console
kubectl apply -f role-sre-namespace.yaml
```

使用[az ad group show][az-ad-group-show]命令來取得*opssre*群組的資源識別碼：

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

建立*opssre*群組的接著，以使用先前為命名空間存取所建立的角色。 建立名為 `rolebinding-sre-namespace.yaml` 的檔案，並貼上下列 YAML 資訊清單。 在最後一行中，將*g i d*取代為上一個命令中的群組物件識別碼輸出：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
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

使用[kubectl apply][kubectl-apply]命令來建立接著，並指定 YAML 資訊清單的檔案名：

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>使用 Azure AD 身分識別與叢集資源互動

現在，當您在 AKS 叢集中建立和管理資源時，讓我們來測試預期的許可權工作。 在這些範例中，您會在使用者指派的命名空間中排程和觀看 pod。 然後，您會嘗試在指派的命名空間之外排程和觀看 pod。

首先，使用[az aks get-認證][az-aks-get-credentials]命令重設*kubeconfig*內容。 在上一節中，您會使用叢集系統管理員認證來設定內容。 系統管理員使用者略過 Azure AD 登入提示。 如果沒有 `--admin` 參數，則會套用使用者內容，要求所有要求都必須使用 Azure AD 進行驗證。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

使用*dev*命名空間中的[kubectl run][kubectl-run]命令來排程基本 NGINX pod：

```console
kubectl run nginx-dev --image=nginx --namespace dev
```

在登入提示中，輸入您自己在發行 `appdev@contoso.com` 項開始時所建立之帳戶的認證。 成功登入之後，就會快取帳戶權杖以供未來的命令之用 `kubectl` 。 已成功排程 NGINX，如下列範例輸出所示：

```console
$ kubectl run nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

現在，請使用[kubectl get][kubectl-get] pod 命令來查看*dev*命名空間中的 pod。

```console
kubectl get pods --namespace dev
```

如下列範例輸出所示，NGINX pod*已成功執行*：

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>建立和查看指派命名空間以外的叢集資源

現在，請嘗試在*dev*命名空間以外的地方觀看 pod。 再次使用[kubectl get][kubectl-get] pod 命令，這次看得到如下 `--all-namespaces` ：

```console
kubectl get pods --all-namespaces
```

使用者的群組成員資格沒有允許此動作的 Kubernetes 角色，如下列範例輸出所示：

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

以同樣的方式，嘗試在不同的命名空間（例如*sre*命名空間）中排程 pod。 使用者的群組成員資格不會與 Kubernetes 角色和接著一致，以授與這些許可權，如下列範例輸出所示：

```console
$ kubectl run nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>測試 SRE 對 AKS 叢集資源的存取

若要確認 Azure AD 群組成員資格和 Kubernetes RBAC 在不同的使用者和群組之間正常運作，請在以*opssre*使用者身分登入時嘗試先前的命令。

使用[az aks get-認證][az-aks-get-credentials]命令來重設*kubeconfig*內容，以清除先前針對*aksdev*使用者快取的驗證權杖：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

嘗試在指派的*sre*命名空間中排程和觀看 pod。 出現提示時，使用您在 `opssre@contoso.com` 本文開頭建立的自己認證登入：

```console
kubectl run nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

如下列範例輸出所示，您可以成功建立並觀看 pod：

```console
$ kubectl run nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

現在，嘗試在指派的 SRE 命名空間之外，查看或排程 pod：

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=nginx --namespace dev
```

這些 `kubectl` 命令會失敗，如下列範例輸出所示。 使用者的群組成員資格和 Kubernetes 角色和 RoleBindings 不會授與許可權來建立或管理其他命名空間中的資源：

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>清除資源

在本文中，您已在 AKS 叢集中建立資源，以及 Azure AD 中的使用者和群組。 若要清除所有這些資源，請執行下列命令：

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

## <a name="next-steps"></a>接下來的步驟

如需如何保護 Kubernetes 叢集的詳細資訊，請參閱[AKS 的存取和身分識別選項）][rbac-authorization]。

如需身分識別和資源控制的最佳做法，請參閱[AKS 中驗證和授權的最佳作法][operator-best-practices-identity]。

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
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
