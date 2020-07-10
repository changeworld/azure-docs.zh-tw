---
title: 在 Azure Kubernetes Service 中使用 Azure AD
description: '瞭解如何在 Azure Kubernetes Service (AKS 中使用 Azure AD) '
services: container-service
manager: gwallace
author: TomGeske
ms.topic: article
ms.date: 07/08/2020
ms.author: thomasge
ms.openlocfilehash: 9cacd2454dc987f7d507bb4b677e742f0be0d391
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86166496"
---
# <a name="aks-managed-azure-active-directory-integration-preview"></a>AKS-受控 Azure Active Directory 整合 (預覽) 

> [!NOTE]
> 具有 Azure Active Directory (Azure AD) 整合的現有 AKS (Azure Kubernetes Service) 叢集不會受到新的 AKS 管理 Azure AD 體驗的影響。

AKS 管理的 Azure AD 整合是設計用來簡化 Azure AD 整合體驗，其中使用者先前必須建立用戶端應用程式、伺服器應用程式，並要求 Azure AD 租使用者授與目錄讀取權限。 在新版本中，AKS 資源提供者會為您管理用戶端和伺服器應用程式。

## <a name="azure-ad-authentication-overview"></a>Azure AD 驗證總覽

叢集系統管理員可以根據使用者的身分識別或目錄群組成員資格，設定 Kubernetes 以角色為基礎的存取控制 (RBAC) 。 透過 OpenID Connect 對 AKS 叢集提供 Azure AD 驗證。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。 如需 OpenID Connect 的詳細資訊，請參閱[OPEN ID connect 檔][open-id-connect]。

若要深入瞭解 AAD 整合流程，請[參閱 Azure Active Directory 整合概念檔](concepts-identity.md#azure-active-directory-integration)。

> [!IMPORTANT]
> AKS preview 功能可在自助服務上自行選擇。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需詳細資訊，請參閱下列支援文章：
>
> - [AKS 支援原則](support-policies.md)
> - [Azure 支援常見問題集](faq.md)

## <a name="before-you-begin"></a>開始之前

* 流覽至 Azure 入口網站並選取 [Azure Active Directory > 內容] > [目錄識別碼]，找出您的 Azure 帳戶租使用者識別碼。

> [!Important]
> 您必須使用最低版本為1.18 的 Kubectl

您必須先安裝下列資源：

- Azure CLI 2.5.1 版或更新版本
- Aks-preview 0.4.38 延伸模組
- 最低版本為[1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)的 Kubectl

若要安裝/更新 aks-preview 擴充功能或更新版本，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

若要安裝 kubectl，請使用下列命令：

```azurecli
sudo az aks install-cli
kubectl version --client
```

請使用[這些指示](https://kubernetes.io/docs/tasks/tools/install-kubectl/)來進行其他作業系統。

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>建立已啟用 Azure AD 的 AKS 叢集

使用下列 CLI 命令來建立 AKS 叢集。

建立 Azure 資源群組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

您可以使用現有的 Azure AD 群組，或建立一個新的群組。 您需要 Azure AD 群組的物件識別碼。

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

若要為您的叢集系統管理員建立新的 Azure AD 群組，請使用下列命令：

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

建立 AKS 叢集，並啟用 Azure AD 群組的系統管理存取權

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

成功建立 AKS 管理的 Azure AD 叢集在回應本文中會有下列區段
```
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

叢集會在幾分鐘內建立。

## <a name="access-an-azure-ad-enabled-cluster"></a>存取已啟用 Azure AD 的叢集

您需要 Azure Kubernetes Service 叢集[使用者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role)內建角色，才能執行下列步驟。

取得用來存取叢集的使用者認證：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
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
[ (RBAC) ](https://docs.microsoft.com/azure/aks/azure-ad-rbac)設定以角色為基礎的存取控制，為您的叢集設定額外的安全性群組。

## <a name="troubleshooting-access-issues-with-azure-ad"></a>針對 Azure AD 的存取問題進行疑難排解

> [!Important]
> 下面所述的步驟會略過一般 Azure AD 群組驗證。 請只在緊急情況下使用它們。

如果您因為無法存取具有叢集存取權的有效 Azure AD 群組而永久封鎖，您仍然可以取得系統管理員認證來直接存取叢集。

若要執行這些步驟，您必須具備 Azure Kubernetes Service 叢集系統[管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role)內建角色的存取權。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

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
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

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

