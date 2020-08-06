---
title: 在 Azure Kubernetes Service 中使用 Azure AD
description: '瞭解如何在 Azure Kubernetes Service (AKS 中使用 Azure AD) '
services: container-service
ms.topic: article
ms.date: 07/27/2020
ms.author: thomasge
ms.openlocfilehash: afc20052680e7f3e5b7d3a6b7320b7ca3b10dbd5
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799852"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS-受控 Azure Active Directory 整合

AKS 管理的 Azure AD 整合是設計用來簡化 Azure AD 整合體驗，其中使用者先前必須建立用戶端應用程式、伺服器應用程式，並要求 Azure AD 租使用者授與目錄讀取權限。 在新版本中，AKS 資源提供者會為您管理用戶端和伺服器應用程式。

## <a name="azure-ad-authentication-overview"></a>Azure AD 驗證總覽

叢集系統管理員可以根據使用者的身分識別或目錄群組成員資格，設定 Kubernetes 以角色為基礎的存取控制 (RBAC) 。 透過 OpenID Connect 對 AKS 叢集提供 Azure AD 驗證。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。 如需 OpenID Connect 的詳細資訊，請參閱[OPEN ID connect 檔][open-id-connect]。

若要深入瞭解 AAD 整合流程，請[參閱 Azure Active Directory 整合概念檔](concepts-identity.md#azure-active-directory-integration)。

## <a name="region-availability"></a>區域可用性

AKS-受控 Azure Active Directory 整合適用于[支援 AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)的公用區域。

* 目前不支援 Azure Government。
* 目前不支援 Azure 中國的世紀。

## <a name="limitations"></a>限制 

* 無法停用 AKS 管理的 Azure AD 整合
* 非 RBAC 啟用的叢集不支援 AKS 管理的 AAD 整合
* 不支援變更與 AKS 管理的 AAD 整合相關聯的 Azure AD 租使用者

## <a name="prerequisites"></a>Prerequisites

* Azure CLI 版2.9.0 版或更新版本
* 最低版本為[1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)的 Kubectl

> [!Important]
> 您必須使用最低版本為1.18 的 Kubectl

若要安裝 kubectl，請使用下列命令：

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
```

請使用[這些指示](https://kubernetes.io/docs/tasks/tools/install-kubectl/)來進行其他作業系統。


## <a name="before-you-begin"></a>開始之前

針對您的叢集，您需要 Azure AD 群組。 需要此群組做為叢集的系統管理員群組，才能授與叢集系統管理員許可權。 您可以使用現有的 Azure AD 群組，或建立一個新的群組。 記錄 Azure AD 群組的物件識別碼。

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

若要為您的叢集系統管理員建立新的 Azure AD 群組，請使用下列命令：

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>建立已啟用 Azure AD 的 AKS 叢集

使用下列 CLI 命令來建立 AKS 叢集。

建立 Azure 資源群組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

建立 AKS 叢集，並啟用 Azure AD 群組的系統管理存取權

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

成功建立 AKS 管理的 Azure AD 叢集在回應本文中會有下列區段
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

建立叢集之後，您就可以開始存取叢集。

## <a name="access-an-azure-ad-enabled-cluster"></a>存取已啟用 Azure AD 的叢集

您需要 Azure Kubernetes Service 叢集[使用者](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role)內建角色，才能執行下列步驟。

取得用來存取叢集的使用者認證：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
依照指示進行登入。

使用 kubectl get 節點命令來查看叢集中的節點：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
[ (AZURE RBAC) 設定 azure 角色型存取控制](./azure-ad-rbac.md)，為您的叢集設定額外的安全性群組。

## <a name="troubleshooting-access-issues-with-azure-ad"></a>針對 Azure AD 的存取問題進行疑難排解

> [!Important]
> 下面所述的步驟會略過一般 Azure AD 群組驗證。 請只在緊急情況下使用它們。

如果您因為無法存取具有叢集存取權的有效 Azure AD 群組而永久封鎖，您仍然可以取得系統管理員認證來直接存取叢集。

若要執行這些步驟，您必須具備 Azure Kubernetes Service 叢集系統[管理員](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)內建角色的存取權。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>升級至 AKS 管理的 Azure AD 整合

如果您的叢集使用舊版 Azure AD 整合，您可以升級至 AKS 管理的 Azure AD 整合。

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

成功地遷移 AKS 管理的 Azure AD 叢集在回應本文中會有下列區段

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

如果您想要存取叢集，請依照[這裡][access-cluster]的步驟進行。

## <a name="non-interactive-sign-in-with-kubelogin"></a>使用 kubelogin 的非互動式登入

有些非互動式案例（例如持續整合管線）目前無法在 kubectl 中使用。 您可以使用 [`kubelogin`](https://github.com/Azure/kubelogin) 來存取具有非互動式服務主體登入的叢集。

## <a name="next-steps"></a>後續步驟

* 瞭解[適用于 Kubernetes 授權的 AZURE RBAC 整合][azure-rbac-integration]
* 深入瞭解[Azure AD 與 KUBERNETES RBAC 整合][azure-ad-rbac]。
* 使用[kubelogin](https://github.com/Azure/kubelogin)來存取 kubectl 中無法使用的 Azure 驗證功能。
* 深入瞭解[AKS 和 Kubernetes 身分識別概念][aks-concepts-identity]。
* 使用[Azure Resource Manager (ARM) 範本][aks-arm-template]來建立 AKS 管理的 Azure AD 啟用的叢集。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
