---
title: 整合 Azure Active Directory 與 Azure Kubernetes Service
description: 瞭解如何使用 Azure CLI 創建啟用 Azure 庫伯奈斯服務 （AKS） 群集
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253048"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>使用 Azure CLI 將 Azure 活動目錄與 Azure 庫伯奈斯服務集成

Azure Kubernetes Service (AKS) 可以設定為使用 Azure Active Directory (AD) 進行使用者驗證。 在此配置中，可以使用 Azure AD 身份驗證權杖登錄到 AKS 群集。 群集操作員還可以根據使用者的身份或目錄組成員身份配置基於 Kubernetes 角色的存取控制 （RBAC）。

本文介紹如何創建所需的 Azure AD 元件，然後部署啟用 Azure AD 的群集並在 AKS 群集中創建基本的 RBAC 角色。 您還可以使用[Azure 門戶完成這些步驟][azure-ad-portal]。

有關本文中使用的完整示例腳本，請參閱 Azure [CLI 示例 - AKS 與 Azure AD 的集成][complete-script]。

套用下列限制：

- 只有建立啟用 RBAC 功能的新叢集時，才能啟用 Azure AD。 您無法在現有的 AKS 叢集上啟用 Azure AD。

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 版本 2.0.61 或更高版本安裝和配置。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

轉到[https://shell.azure.com](https://shell.azure.com)在瀏覽器中打開雲外殼。

為保持一致性並説明運行本文中的命令，請為所需的 AKS 群集名稱創建一個變數。 下面的示例使用*名稱 myakscluster*：

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 身份驗證概述

透過 OpenID Connect 對 AKS 叢集提供 Azure AD 驗證。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。 有關 OpenID 連接的詳細資訊，請參閱[打開 ID 連接文檔][open-id-connect]。

從 Kubernetes 叢集內部，Webhook 權杖驗證用來確認驗證權杖。 Webhook 權杖驗證已設定並當作 AKS 叢集的一部分管理。 如需 Webhook 權杖驗證的詳細資訊，請參閱 [Webhook 驗證文件][kubernetes-webhook]。

> [!NOTE]
> 設定 Azure AD 以進行 AKS 驗證時，會設定兩個 Azure AD 應用程式。 這項作業必須由 Azure 租用戶系統管理員完成。

## <a name="create-azure-ad-server-component"></a>創建 Azure AD 伺服器元件

要與 AKS 集成，請創建並使用充當標識請求終結點的 Azure AD 應用程式。 您需要的第一個 Azure AD 應用程式獲取使用者的 Azure AD 組成員身份。

使用[az 廣告應用創建命令創建][az-ad-app-create]伺服器應用程式元件，然後使用[az 廣告應用更新][az-ad-app-update]命令更新組成員資格聲明。 下面的示例使用在["開始之前](#before-you-begin)"部分中定義的*aksname*變數，並創建一個變數

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

現在，使用[az ad sp 創建][az-ad-sp-create]命令為伺服器應用創建服務主體。 此服務主體用於在 Azure 平臺中驗證自身。 然後，使用[az ad sp 憑據重置][az-ad-sp-credential-reset]命令獲取服務主體機密，並分配給名為*serverApplicationSecret*的變數，以便執行以下步驟之一：

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD 需要許可權才能執行以下操作：

* 讀取目錄資料
* 登入及讀取使用者設定檔

使用[az 廣告應用許可權添加][az-ad-app-permission-add]命令分配這些許可權：

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

最後，使用[az ad 應用許可權授予][az-ad-app-permission-grant]命令授予在上一步中為伺服器應用程式分配的許可權。 如果當前帳戶不是租戶管理員，則此步驟將失敗。您還需要為 Azure AD 應用程式添加許可權，以便使用[az 廣告應用版權管理員同意][az-ad-app-permission-admin-consent]請求可能需要管理同意的資訊：

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>創建 Azure AD 用戶端元件

當使用者使用 Kubernetes CLI （）`kubectl`登錄到 AKS 群集時，將使用第二個 Azure AD 應用程式。 此用戶端應用程式從使用者獲取身份驗證請求並驗證其憑據和許可權。 使用[az 廣告應用創建][az-ad-app-create]命令為用戶端元件創建 Azure AD 應用：

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

使用[az ad sp 創建][az-ad-sp-create]命令為客戶應用程式創建服務主體：

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

獲取伺服器應用的 oAuth2 ID，以便使用[az 廣告應用顯示][az-ad-app-show]命令允許兩個應用元件之間的身份驗證流。 下一步將使用此 oAuth2 ID。

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

添加用戶端應用程式和伺服器應用程式元件的許可權，以便使用[az ad 應用許可權添加][az-ad-app-permission-add]命令使用 oAuth2 通信流。 然後，授予用戶端應用程式使用[az 廣告應用許可權授予][az-ad-app-permission-grant]命令與伺服器應用程式通信的許可權：

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>部署群集

創建兩個 Azure AD 應用程式後，現在創建 AKS 群集本身。 首先，使用[az 組創建命令創建][az-group-create]資源組。 下面的示例在*EastUS*區域中創建資源組：

為群集創建資源組：

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

使用[az 帳戶顯示][az-account-show]命令獲取 Azure 訂閱的租戶 ID。 然後，使用[az aks 創建命令創建][az-aks-create]AKS 群集。 創建 AKS 群集的命令提供伺服器和用戶端應用程式 ID、伺服器應用程式服務主體機密和租戶 ID：

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

最後，使用[az aks 獲取憑據][az-aks-get-credentials]命令獲取群集管理員憑據。 在以下步驟之一中，您將獲得常規*使用者*群集憑據，以查看 Azure AD 身份驗證流的運行情況。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>建立 RBAC 繫結

必須先建立角色繫結或叢集角色繫結，Azure Active Directory 帳戶才能搭配 AKS 叢集使用。 「角色」** 會定義要授與的權限，而「繫結」** 會將角色套用至需要的使用者。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

使用[az 廣告登錄使用者展示][az-ad-signed-in-user-show]命令獲取當前登錄的使用者的主名稱 （UPN）。 在下一步中，此使用者帳戶已為 Azure AD 集成啟用。

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> 如果為其授予 RBAC 綁定的使用者位於同一 Azure AD 租戶中，請根據*使用者主體名稱*分配許可權。 如果使用者位於其他 Azure AD 租戶中，請查詢並使用*objectId*屬性。

創建名為`basic-azure-ad-binding.yaml`YAML 的清單並粘貼以下內容。 在最後一行，將*userPrincipalName_or_objectId*替換為上一命令的 UPN 或物件識別碼 輸出：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

使用[kubectl 應用][kubectl-apply]命令創建群集角色綁定並指定 YAML 清單的檔案名：

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>透過 Azure AD 存取叢集

現在，讓我們測試 AKS 群集的 Azure AD 身份驗證集成。 將`kubectl`配置上下文設置為使用常規使用者憑據。 此上下文通過 Azure AD 傳遞所有身份驗證請求。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

現在使用[kubectl 獲取窗格][kubectl-get]命令查看所有命名空間中的窗格：

```console
kubectl get pods --all-namespaces
```

您會收到使用 Web 瀏覽器使用 Azure AD 憑據進行身份驗證的登錄提示。 成功進行身份驗證後，`kubectl`該命令將顯示 AKS 群集中的 pod，如以下示例輸出所示：

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

已緩存為其接收的`kubectl`身份驗證權杖。 僅當令牌過期或重新創建 Kubernetes 設定檔時，才會重新提示您登錄。

如果您在成功使用 Web 瀏覽器登錄後看到授權錯誤訊息，請檢查以下可能的問題：

```output
error: You must be logged in to the server (Unauthorized)
```

* 定義適當的物件識別碼 或 UPN，具體取決於使用者帳戶是否位於同一 Azure AD 租戶中。
* 使用者不是 200 個以上的群組成員。
* 伺服器應用程式註冊中定義的機密與使用配置的值匹配`--aad-server-app-secret`

## <a name="next-steps"></a>後續步驟

有關包含本文中顯示的命令的完整腳本，請參閱[AKS 示例回購中的 Azure AD 集成腳本][complete-script]。

要使用 Azure AD 使用者和組來控制對群集資源的訪問，請參閱[使用基於角色的存取控制和 AKS 中的 Azure AD 標識對群集資源的控制訪問][azure-ad-rbac]。

有關如何保護 Kubernetes 群集的詳細資訊，請參閱[AKS 的訪問和標識選項。][rbac-authorization]

有關標識和資源控制的最佳做法，請參閱[AKS 中身份驗證和授權的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
