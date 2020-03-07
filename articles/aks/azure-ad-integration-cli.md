---
title: 整合 Azure Active Directory 與 Azure Kubernetes Service
description: 瞭解如何使用 Azure CLI 建立和 Azure Active Directory 啟用的 Azure Kubernetes Service （AKS）叢集
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395365"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>使用 Azure CLI 整合 Azure Active Directory 與 Azure Kubernetes Service

Azure Kubernetes Service (AKS) 可以設定為使用 Azure Active Directory (AD) 進行使用者驗證。 在此設定中，您可以使用 Azure AD 驗證權杖來登入 AKS 叢集。 叢集操作員也可以根據使用者的身分識別或目錄群組成員資格，設定 Kubernetes 角色型存取控制（RBAC）。

本文說明如何建立必要的 Azure AD 元件，然後部署已啟用 Azure AD 的叢集，並在 AKS 叢集中建立基本的 RBAC 角色。 您也可以[使用 Azure 入口網站來完成這些步驟][azure-ad-portal]。

如需本文中使用的完整範例腳本，請參閱[Azure CLI 範例-AKS 與 Azure AD 整合][complete-script]。

套用下列限制：

- 只有建立啟用 RBAC 功能的新叢集時，才能啟用 Azure AD。 您無法在現有的 AKS 叢集上啟用 Azure AD。

## <a name="before-you-begin"></a>開始之前

您需要安裝並設定 Azure CLI 版本2.0.61 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

請移至[https://shell.azure.com](https://shell.azure.com) ，以在瀏覽器中開啟 Cloud Shell。

為求一致，並協助執行本文中的命令，請為您想要的 AKS 叢集名稱建立一個變數。 下列範例會使用名稱*myakscluster*：

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 驗證總覽

透過 OpenID Connect 對 AKS 叢集提供 Azure AD 驗證。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。 如需 OpenID Connect 的詳細資訊，請參閱[OPEN ID connect 檔][open-id-connect]。

從 Kubernetes 叢集內部，Webhook 權杖驗證用來確認驗證權杖。 Webhook 權杖驗證已設定並當作 AKS 叢集的一部分管理。 如需 Webhook 權杖驗證的詳細資訊，請參閱[webhook 驗證檔][kubernetes-webhook]。

> [!NOTE]
> 設定 Azure AD 以進行 AKS 驗證時，會設定兩個 Azure AD 應用程式。 這項作業必須由 Azure 租用戶系統管理員完成。

## <a name="create-azure-ad-server-component"></a>建立 Azure AD 伺服器元件

若要與 AKS 整合，您可以建立並使用作為身分識別要求端點的 Azure AD 應用程式。 您需要的第一個 Azure AD 應用程式會取得使用者 Azure AD 群組成員資格。

使用[az ad app create][az-ad-app-create]命令來建立伺服器應用程式元件，然後使用[az ad app update][az-ad-app-update]命令來更新群組成員資格宣告。 下列範例會使用 [在[您開始前](#before-you-begin)] 區段中定義的*aksname*變數，並建立變數

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

現在，使用[az ad sp create][az-ad-sp-create]命令來建立伺服器應用程式的服務主體。 此服務主體是用來在 Azure 平臺內自行驗證。 然後，使用[az ad sp credential reset][az-ad-sp-credential-reset]命令取得服務主體秘密，並將指派給名為*serverApplicationSecret*的變數，以用於下列其中一個步驟：

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD 需要執行下列動作的許可權：

* 讀取目錄資料
* 登入及讀取使用者設定檔

使用[az ad app 許可權 add][az-ad-app-permission-add]命令來指派這些許可權：

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

最後，使用[az ad app 許可權 grant][az-ad-app-permission-grant]命令，授與在上一個步驟中為伺服器應用程式指派的許可權。 如果目前的帳戶不是租使用者系統管理員，則此步驟會失敗。您也需要新增 Azure AD 應用程式的許可權，以要求可能需要使用[az AD app 許可權系統管理員][az-ad-app-permission-admin-consent]同意來進行系統管理同意的資訊：

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>建立 Azure AD 用戶端元件

當使用者使用 Kubernetes CLI （`kubectl`）登入 AKS 叢集時，會使用第二個 Azure AD 應用程式。 此用戶端應用程式會接受來自使用者的驗證要求，並驗證其認證和許可權。 使用[az AD app create][az-ad-app-create]命令，建立用戶端元件的 Azure AD 應用程式：

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

使用[az ad sp create][az-ad-sp-create]命令來建立用戶端應用程式的服務主體：

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

取得伺服器應用程式的 oAuth2 識別碼，以允許使用[az ad app show][az-ad-app-show]命令在兩個應用程式元件之間進行驗證流程。 在下一個步驟中會使用此 oAuth2 識別碼。

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

使用[az ad app 許可權 add][az-ad-app-permission-add]命令，新增用戶端應用程式和伺服器應用程式元件的許可權，以使用 oAuth2 通訊流程。 然後，使用[az ad app 許可權 grant][az-ad-app-permission-grant]命令，授與用戶端應用程式與伺服器應用程式通訊的許可權：

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>部署叢集

建立兩個 Azure AD 應用程式後，現在建立 AKS 叢集本身。 首先，使用[az group create][az-group-create]命令來建立資源群組。 下列範例會在*EastUS*區域中建立資源群組：

建立叢集的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

使用[az account show][az-account-show]命令取得 Azure 訂用帳戶的租使用者識別碼。 然後，使用[az AKS create][az-aks-create]命令來建立 AKS 叢集。 建立 AKS 叢集的命令會提供伺服器和用戶端應用程式識別碼、伺服器應用程式服務主體密碼和您的租使用者識別碼：

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

最後，使用[az aks get-認證][az-aks-get-credentials]命令取得叢集系統管理員認證。 在下列其中一個步驟中，您會取得一般*使用者*叢集認證，以查看作用中的 Azure AD 驗證流程。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>建立 RBAC 繫結

必須先建立角色繫結或叢集角色繫結，Azure Active Directory 帳戶才能搭配 AKS 叢集使用。 「角色」會定義要授與的權限，而「繫結」會將角色套用至需要的使用者。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

使用[az ad 登入-user show][az-ad-signed-in-user-show]命令，取得目前登入之使用者的使用者主體名稱（UPN）。 在下一個步驟中，此使用者帳戶已啟用 Azure AD 整合。

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> 如果您授與的 RBAC 系結的使用者位於相同的 Azure AD 租使用者中，請根據*userPrincipalName*指派許可權。 如果使用者位於不同的 Azure AD 租使用者中，請改為查詢並使用*objectId*屬性。

建立名為 `basic-azure-ad-binding.yaml` 的 YAML 資訊清單，並貼上下列內容。 在最後一行上，將*userPrincipalName_or_objectId*取代為上一個命令中的 UPN 或物件識別碼輸出：

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

使用[kubectl apply][kubectl-apply]命令來建立 ClusterRoleBinding，並指定 YAML 資訊清單的檔案名：

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>透過 Azure AD 存取叢集

現在讓我們來測試 AKS 叢集的 Azure AD authentication 整合。 將 `kubectl` config 內容設定為使用一般使用者認證。 此內容會透過 Azure AD 將所有驗證要求傳遞回來。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

現在，請使用[kubectl get][kubectl-get] pod 命令來跨所有命名空間來查看 pod：

```console
kubectl get pods --all-namespaces
```

您會收到登入提示，您可以使用網頁瀏覽器來使用 Azure AD 認證來進行驗證。 成功驗證之後，`kubectl` 命令會顯示 AKS 叢集中的 pod，如下列範例輸出所示：

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

已快取為 `kubectl` 所收到的驗證權杖。 只有在權杖過期或重新建立 Kubernetes config 檔案時，才會重複提示登入。

如果您在使用網頁瀏覽器成功登入後看到授權錯誤訊息，如下列範例輸出所示，請檢查下列可能的問題：

```output
error: You must be logged in to the server (Unauthorized)
```

* 您已定義適當的物件識別碼或 UPN，視使用者帳戶是否位於相同的 Azure AD 租使用者而定。
* 使用者不是 200 個以上的群組成員。
* 在伺服器的應用程式註冊中定義的密碼符合使用 `--aad-server-app-secret` 設定的值

## <a name="next-steps"></a>後續步驟

如需包含本文所示命令的完整腳本，請參閱[AKS 範例存放庫中的 Azure AD 整合腳本][complete-script]。

若要使用 Azure AD 的使用者和群組來控制叢集資源的存取權，請參閱[使用角色型存取控制來控制對叢集資源的存取，並在 AKS 中 Azure AD][azure-ad-rbac]身分識別。

如需如何保護 Kubernetes 叢集的詳細資訊，請參閱[AKS 的存取和身分識別選項）][rbac-authorization]。

如需身分識別和資源控制的最佳做法，請參閱[AKS 中驗證和授權的最佳作法][operator-best-practices-identity]。

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
