---
title: 整合 Azure Active Directory 與 Azure Kubernetes Service
description: 如何建立已啟用 Azure Active Directory 的 Azure Kubernetes Service （AKS）叢集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/02/2019
ms.author: mlearned
ms.openlocfilehash: 956cfbafeebadaa23ba3a48f7d38c6f95b412bd9
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158208"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>整合 Azure Active Directory 與 Azure Kubernetes Service

Azure Kubernetes Service （AKS）可以設定為使用 Azure Active Directory （Azure AD）進行使用者驗證。 在此設定中，您可以使用 Azure AD 驗證權杖來登入 AKS 叢集。

叢集系統管理員可以根據使用者的身分識別或目錄群組成員資格，設定 Kubernetes 角色型存取控制（RBAC）。

本文說明如何：

- 部署 AKS 和 Azure AD 的必要條件。
- 部署已啟用 Azure AD 的叢集。
- 使用 Azure 入口網站，在 AKS 叢集中建立基本的 RBAC 角色。

您也可以使用[Azure CLI][azure-ad-cli]來完成這些步驟。

> [!NOTE]
> 只有當您建立已啟用 RBAC 的新叢集時，才可以啟用 Azure AD。 您無法在現有的 AKS 叢集上啟用 Azure AD。

## <a name="authentication-details"></a>驗證詳細資料

Azure AD 驗證會提供給具有 OpenID Connect 的 AKS 叢集。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。

如需 OpenID Connect 的詳細資訊，請參閱[使用 Openid connect 和 Azure AD 授權存取 web 應用程式][open-id-connect]。

在 Kubernetes 叢集中，會使用 webhook 權杖驗證來驗證權杖。 Webhook 權杖驗證已設定並當作 AKS 叢集的一部分管理。

如需 webhook 權杖驗證的詳細資訊，請參閱 Kubernetes 檔中的[Webhook 權杖驗證][kubernetes-webhook]一節。

為了提供 AKS 叢集的 Azure AD 驗證，會建立兩個 Azure AD 的應用程式。 第一個應用程式是提供使用者驗證的伺服器元件。 第二個應用程式是一種用戶端元件，當 CLI 提示您進行驗證時，會使用它。 此用戶端應用程式會使用伺服器應用程式來實際驗證用戶端所提供的認證。

> [!NOTE]
> 當您設定 AKS authentication 的 Azure AD 時，會設定兩個 Azure AD 的應用程式。 為每個應用程式委派許可權的步驟必須由 Azure 租使用者系統管理員完成。

## <a name="create-the-server-application"></a>建立伺服器應用程式

會套用第一個 Azure AD 應用程式，以取得使用者的 Azure AD 群組成員資格。 若要在 Azure 入口網站中建立此應用程式：

1. 選取 [Azure Active Directory] > [應用程式註冊] > [新註冊]。

    a. 提供應用程式的名稱，例如*AKSAzureADServer*。

    b. 針對**支援的帳戶類型**，請選取 [**僅此組織目錄中的帳戶**]。
    
    c. 針對 [重新導向 URI 類型] 選擇 [ **Web** ]，然後輸入任何 URI 格式的值，例如 *https://aksazureadserver* 。

    d. 當您完成時，請選取 [**註冊**]。

2. 選取 [**資訊清單**]，然後編輯 [ **groupMembershipClaims：** ] 值為 [**全部**]。 當您完成更新時，請選取 [**儲存**]。

    ![將群組成員資格更新為全部](media/aad-integration/edit-manifest.png)

3. 在 Azure AD 應用程式的左窗格中，選取 [**憑證 & 秘密**]。

    a. 選取 [ **+ 新增用戶端密碼**]。

    b. 新增金鑰描述，例如*AKS Azure AD server*。 選擇 [到期時間]，然後選取 [**新增**]。

    c. 請注意，這次只會顯示金鑰值。 當您部署已啟用 Azure AD 的 AKS 叢集時，這個值稱為「伺服器應用程式密碼」。

4. 在 Azure AD 應用程式的左窗格中，選取 [ **API 許可權**]，然後選取 [ **+ 新增許可權**]。

    a. 在 [ **Microsoft api**] 底下，選取 [ **Microsoft Graph**]。

    b. 選取 [**委派的許可權**]，然後選取 [目錄] > [目錄] 旁的核取方塊。 [全部] **（讀取目錄資料）** 。

    c. 如果**使用者 > [讀取（登入和讀取使用者設定檔）** ] 的預設委派許可權不存在，請選取它旁邊的核取方塊。

    d. 選取 [**應用程式許可權**]，然後選取 [目錄] > [目錄] 旁的核取方塊 **。 [全部] （讀取目錄資料）** 。

    ![設定圖形許可權](media/aad-integration/graph-permissions.png)

    e. 選取 [**新增許可權**] 以儲存更新。

    f. 在 **[授與同意**] 底下，選取 **[授與系統管理員同意**]。 目前使用的帳戶未列為租使用者系統管理員，因此無法使用此按鈕。

    成功授與許可權後，入口網站中會顯示下列通知：

   ![成功授與權限的通知](media/aad-integration/permissions-granted.png)

5. 在 Azure AD 應用程式的左窗格中，選取 [**公開 API**]，然後選取 [ **+ 新增領域**]。
    
    a. 輸入**領域名稱**、系統**管理員同意顯示名稱**，以及系統**管理員同意描述**，例如*AKSAzureADServer*。

    b. 請確定 [**狀態**] 設定為 [**已啟用**]。

    ![將伺服器應用程式公開為與其他服務搭配使用的 API](media/aad-integration/expose-api.png)

    c. 選取 [**新增領域**]。

6. 返回 [應用程式**總覽**] 頁面，並記下**應用程式（用戶端）識別碼**。 當您部署已啟用 Azure AD 的 AKS 叢集時，這個值稱為伺服器應用程式識別碼。

    ![取得應用程式識別碼](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>建立用戶端應用程式

當您使用 Kubernetes CLI （kubectl）登入時，會使用第二個 Azure AD 應用程式。

1. 選取 [Azure Active Directory] > [應用程式註冊] > [新註冊]。

    a. 提供應用程式的名稱，例如*AKSAzureADClient*。

    b. 針對**支援的帳戶類型**，請選取 [**僅此組織目錄中的帳戶**]。

    c. 針對 [重新導向 URI 類型] 選取 [ **Web** ]，然後輸入任何 URI 格式的值，例如 *https://aksazureadclient* 。

    >[!NOTE]
    >如果您要建立啟用 RBAC 的新叢集以支援容器的 Azure 監視器，請將下列兩個額外的重新導向 Url 新增到此清單中，作為**Web**應用程式類型。 第一個 [基底 URL] 值應該是 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，而第二個 [基底 URL] 值應該是 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`。
    >
    >如果您在 Azure 中國使用這項功能，第一個 [基底 URL] 值應該是 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，而第二個 [基底 URL] 值應該是 [`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`]。
    >
    >如需進一步資訊，請參閱如何設定適用于容器的 Azure 監視器[即時資料（預覽）功能](../azure-monitor/insights/container-insights-livedata-setup.md)和[設定 AD 整合式驗證](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)下的驗證步驟一節。

    d. 當您完成時，請選取 [**註冊**]。

2. 在 Azure AD 應用程式的左窗格中，選取 [ **API 許可權**]，然後選取 [ **+ 新增許可權**]。

    a. 選取 [**我的 api**]，然後選擇您在上一個步驟中建立的 Azure AD 伺服器應用程式，例如*AKSAzureADServer*。

    b. 選取 [**委派的許可權**]，然後選取 Azure AD 伺服器應用程式旁的核取方塊。

    ![設定應用程式權限](media/aad-integration/select-api.png)

    c. 選取 [新增權限]。

    d. 在 **[授與同意**] 底下，選取 **[授與系統管理員同意**]。 如果目前的帳戶不是租使用者系統管理員，則無法使用此按鈕。授與許可權時，入口網站中會顯示下列通知：

    ![成功授與權限的通知](media/aad-integration/permissions-granted.png)

3. 在 Azure AD 應用程式的左窗格中，選取 [**驗證**]。

    - 在 [**預設用戶端類型**] 底下，選取 **[是]** ，將**用戶端視為公用用戶端**。

5. 在 Azure AD 應用程式的左窗格中，記下 [應用程式識別碼]。 當您部署已啟用 Azure AD 的 AKS 叢集時，這個值稱為用戶端應用程式識別碼。

   ![取得應用程式識別碼](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>取得租用戶識別碼

接下來，取得 Azure 租使用者的識別碼。 當您建立 AKS 叢集時，會使用此值。

從 Azure 入口網站中，選取  **Azure Active Directory** > **屬性**，並記下 **目錄識別碼**。 當您建立已啟用 Azure AD 的 AKS 叢集時，這個值稱為租使用者識別碼。

![取得 Azure 租用戶識別碼](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>部署 AKS 叢集

使用[az group create][az-group-create]命令來建立 AKS 叢集的資源群組。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用[az aks create][az-aks-create]命令來部署 aks 叢集。 接下來，取代下列範例命令中的值。 當您為伺服器應用程式識別碼、應用程式密碼、用戶端應用程式識別碼和租使用者識別碼建立 Azure AD 應用程式時，請使用所收集的值。

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

AKS 叢集需要幾分鐘的時間來建立。

## <a name="create-an-rbac-binding"></a>建立 RBAC 系結

> [!NOTE]
> 叢集角色系結名稱會區分大小寫。

使用 Azure Active Directory 帳戶搭配 AKS 叢集之前，您必須先建立角色系結或叢集角色系結。 角色會定義要授與的許可權，而系結會將其套用至所需的使用者。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

首先，使用[az aks get-認證][az-aks-get-credentials]命令搭配 `--admin` 引數，以使用系統管理員存取權登入叢集。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

接下來，針對您想要授與 AKS 叢集存取權的 Azure AD 帳戶建立 ClusterRoleBinding。 下列範例會為帳戶提供叢集中所有命名空間的完整存取權：

- 如果您授與的 RBAC 系結的使用者位於相同的 Azure AD 租使用者中，請根據使用者主要名稱（UPN）指派許可權。 繼續進行步驟，以建立 ClusterRoleBinding 的 YAML 資訊清單。

- 如果使用者位於不同的 Azure AD 租使用者中，請改為查詢並使用**objectId**屬性。 如有需要，請使用[az ad user show][az-ad-user-show]命令取得所需使用者帳戶的 objectId。 提供所需帳戶的使用者主要名稱（UPN）：

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

建立檔案（例如*rbac-aad-user. yaml*），然後貼上下列內容。 在最後一行中，將**userPrincipalName_or_objectId**取代為 UPN 或物件識別碼。 選擇取決於使用者是否為相同的 Azure AD 租使用者。

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

使用[kubectl apply][kubectl-apply]命令套用系結，如下列範例所示：

```console
kubectl apply -f rbac-aad-user.yaml
```

此外，也可以為 Azure AD 群組的所有成員建立角色繫結。 Azure AD 群組是使用群組物件識別碼所指定，如下列範例所示。

建立一個檔案（例如*yaml*），然後貼上下列內容。 以 Azure AD 租用戶中的群組物件識別碼來更新使用者名稱。

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

使用[kubectl apply][kubectl-apply]命令套用系結，如下列範例所示：

```console
kubectl apply -f rbac-aad-group.yaml
```

如需使用 RBAC 保護 Kubernetes 叢集的詳細資訊，請參閱[使用 Rbac 授權][rbac-authorization]。

## <a name="access-the-cluster-with-azure-ad"></a>使用 Azure AD 存取叢集

使用[az aks get-認證][az-aks-get-credentials]命令，提取非系統管理員使用者的內容。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

執行 `kubectl` 命令之後，系統會提示您使用 Azure 進行驗證。 依照畫面上的指示完成程式，如下列範例所示：

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

當程式完成時，就會快取驗證權杖。 當令牌過期時，系統只會提示您登入，或重新建立 Kubernetes 設定檔。

如果您在成功登入後看到授權錯誤訊息，請檢查下列準則：

```console
error: You must be logged in to the server (Unauthorized)
```


- 您已定義適當的物件識別碼或 UPN，視使用者帳戶是否位於相同的 Azure AD 租使用者而定。
- 使用者不是超過200個群組的成員。
- 在 [應用程式註冊] 中定義的秘密，會符合使用 `--aad-server-app-secret`所設定的值。

## <a name="next-steps"></a>後續步驟

若要使用 Azure AD 的使用者和群組來控制叢集資源的存取權，請參閱[使用角色型存取控制來控制對叢集資源的存取，並在 AKS 中 Azure AD][azure-ad-rbac]身分識別。

如需如何保護 Kubernetes 叢集的詳細資訊，請參閱[AKS 的存取和身分識別選項][rbac-authorization]。

若要深入瞭解身分識別和資源控制，請參閱[AKS 中驗證和授權的最佳作法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
