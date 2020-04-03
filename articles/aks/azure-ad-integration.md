---
title: 整合 Azure Active Directory 與 Azure Kubernetes Service
description: 如何建立啟用 Azure 活動目錄的 Azure 庫伯奈斯服務 (AKS) 叢集
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617589"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>整合 Azure Active Directory 與 Azure Kubernetes Service

Azure 庫伯奈斯服務 (AKS) 可以配置為使用 Azure 活動目錄 (Azure AD) 進行使用者身份驗證。 在此配置中,可以使用 Azure AD 身份驗證權杖登錄到 AKS 叢集。

群集管理員可以基於使用者的身份或目錄組成員身份配置基於 Kubernetes 角色的訪問控制 (RBAC)。

本文說明如何：

- 部署 AKS 和 Azure AD 的先決條件。
- 部署啟用 Azure AD 的群集。
- 使用 Azure 門戶在 AKS 群集中創建基本 RBAC 角色。

您還可以使用[Azure CLI][azure-ad-cli]完成這些步驟。

> [!NOTE]
> 僅當創建新啟用 RBAC 的群集時,才能啟用 Azure AD。 您無法在現有的 AKS 叢集上啟用 Azure AD。

## <a name="authentication-details"></a>驗證詳細資料

Azure AD 認證提供給具有 OpenID 連線的 AKS 叢集。 OpenID Connect 是以 OAuth 2.0 通訊協定為建置基礎的身分識別層。

有關 OpenID 連線的詳細資訊,請參閱[使用 OpenID 連線與 Azure AD 授權存取 Web 應用程式][open-id-connect]。

在 Kubernetes 群集中,Webhook 權杖身份驗證用於身份驗證權杖。 Webhook 權杖驗證已設定並當作 AKS 叢集的一部分管理。

有關 Webhook 權杖身份驗證的詳細資訊,請參閱 Kubernetes 文檔中的[Webhook 權杖身份驗證][kubernetes-webhook]部分。

要為 AKS 群集提供 Azure AD 身份驗證,請建立兩個 Azure AD 應用程式。 第一個應用程式是提供使用者身份驗證的伺服器元件。 第二個應用程式是客戶端元件,在 CLI 提示您進行身份驗證時使用。 此用戶端應用程式使用伺服器應用程式對用戶端提供的憑據進行實際身份驗證。

> [!NOTE]
> 為 AKS 身份驗證配置 Azure AD 時,將配置兩個 Azure AD 應用程式。 委派每個應用程式的許可權的步驟必須由 Azure 租戶管理員完成。

## <a name="create-the-server-application"></a>建立伺服器應用程式

第一個 Azure AD 應用程式應用於獲取使用者的 Azure AD 組成員身份。 要在 Azure 門戶中建立此應用程式,請執行以下計劃:

1. 選擇**Azure 的動作目錄** > **套用** > **註冊新註冊**。

    a. 指定應用程式指定名稱,例如*AKSAzureADServer*。

    b. 對**支援的帳號型態 ,****選擇只有在此組織目錄中的帳號**。
    
    c. 為重定向 URI 型態選擇**Web,** 然後輸入任何*https://aksazureadserver*URI 格式的值,如 。

    d. 選擇**完成後註冊**。

2. 選擇**清單,** 然後編輯**組成員聲明:** 值為 **"全部**" 。 完成更新後,選擇 **「保存**」。。

    ![將群組成員資格更新為全部](media/aad-integration/edit-manifest.png)

3. 在 Azure AD 應用程式的左邊窗格中,選擇**憑證&機密**。

    a. 選擇 **= 新客戶端機密**。

    b. 新增金鑰說明,如*AKS Azure AD 伺服器*。 選擇過期時間,然後選擇 **"添加**"。

    c. 請注意鍵值,該值此時僅顯示。 部署啟用 Azure AD 的 AKS 群集時,此值稱為伺服器應用程式機密。

4. 在 Azure AD 應用程式的左邊窗格中,選擇**API 權限**,然後選擇 **「添加權限**」 。。

    a. 在**微軟 API**下,選擇**微軟圖形**。

    b. 選擇**委派許可權**,然後選擇**目錄>目錄旁邊的複選框。讀取.All(讀取目錄數據)。**

    c. 如果使用者 **>用户.Read(登錄和讀取使用者配置檔)** 的預設委派許可權不存在,請選擇其旁邊的複選框。

    d. 選擇**應用程式許可權**,然後選擇**目錄>目錄旁邊的複選框。讀取.All(讀取目錄數據)。**

    ![設定圖形權限](media/aad-integration/graph-permissions.png)

    e. 選擇 **「添加許可權**以保存更新」。

    f. 在**授予同意**下,選擇**授予管理員同意**。 此按鈕不可用,當前帳戶未列為租戶管理員。

    成功授予許可權后,門戶中將顯示以下通知:

   ![成功授與權限的通知](media/aad-integration/permissions-granted.png)

5. 在 Azure AD 應用程式的左邊窗格中,選擇 **「公開 API」,** 然後選擇 **「添加範圍**」 。。
    
    a. 輸入**範圍名稱**、**管理員同意顯示名稱**,然後輸入**管理員同意說明**(如*AKSAzureADServer)。*

    b. 確保**狀態**設置為 **「已啟用**」。

    ![將伺服器應用公開為其他服務的 API](media/aad-integration/expose-api.png)

    c. 選擇 **「添加範圍**」。

6. 傳回應用程式**概述**頁面並記下**應用程式(用戶端)ID**。 部署啟用 Azure AD 的 AKS 群集時,此值稱為伺服器應用程式 ID。

    ![取得應用程式識別碼](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>建立用戶端應用程式

使用庫伯奈茨 CLI (kubectl) 登錄時,將使用第二個 Azure AD 應用程式。

1. 選擇**Azure 的動作目錄** > **套用** > **註冊新註冊**。

    a. 指定應用程式指定名稱,例如*AKSAzureADClient*。

    b. 對**支援的帳號型態 ,****選擇只有在此組織目錄中的帳號**。

    c. 為重定向 URI 型態選擇**Web,** 然後輸入任何*https://aksazureadclient*URI 格式的值,如 。

    >[!NOTE]
    >如果要創建新的啟用 RBAC 的群集以支援容器的 Azure 監視器,請將以下兩個附加重定向 URL 作為**Web**應用程式類型添加到此清單。 第一個基本網址`https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`值 應為第二個基本網址值應為`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >如果在 Azure 中國中使用此功能,則第一個基本 URL`https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`值應為 ,第二個基本 URL 值應為`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >有關詳細資訊,請參閱如何為容器設置 Azure 監視器[的即時數據(預覽)功能](../azure-monitor/insights/container-insights-livedata-setup.md),以及[配置 AD 整合身份驗證](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)部分下配置身份驗證的步驟。

    d. 選擇**完成後註冊**。

2. 在 Azure AD 應用程式的左邊窗格中,選擇**API 權限**,然後選擇 **「添加權限**」 。。

    a. 選擇**我的 API**,然後選擇在上一步中建立的 Azure AD 伺服器應用程式,如*AKSAzureADServer*。

    b. 選擇**委派許可權**,然後選擇 Azure AD 伺服器應用旁邊的複選框。

    ![設定應用程式權限](media/aad-integration/select-api.png)

    c. 選取 [新增權限]****。

    d. 在**授予同意**下,選擇**授予管理員同意**。 如果當前帳戶不是租戶管理員,則此按鈕不可用。授予權限後,門戶中將顯示以下通知:

    ![成功授與權限的通知](media/aad-integration/permissions-granted.png)

3. 在 Azure AD 應用程式的左邊窗格中,選擇**認證**。

    - 在 **「預設的客戶端型態」** 下,選擇 **「是****「將客戶端視為公共用戶端**。

5. 在 Azure AD 應用程式的左側窗格中,請注意應用程式 ID。 部署啟用 Azure AD 的 AKS 群集時,此值稱為用戶端應用程式 ID。

   ![取得應用程式識別碼](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>取得租用戶識別碼

接下來,獲取 Azure 租戶的 ID。 創建 AKS 群集時使用此值。

在 Azure 門戶中,選擇**Azure 的目錄** > **屬性**並記下**目錄代碼**。 創建啟用 Azure AD 的 AKS 群集時,此值稱為租戶 ID。

![取得 Azure 租用戶識別碼](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>部署 AKS 叢集

使用 [az group create][az-group-create] 命令來建立 AKS 叢集的資源群組。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用[az aks 建立][az-aks-create]命令部署 AKS 群集。 接下來,替換以下示例命令中的值。 使用為伺服器應用 ID、應用機密、用戶端應用 ID 和租戶 ID 創建 Azure AD 應用程式時收集的值。

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

AKS 群集需要幾分鐘才能創建。

## <a name="create-an-rbac-binding"></a>建立 RBAC 繫結

> [!NOTE]
> 叢集角色綁定名稱區分大小寫。

在將 Azure 活動目錄帳戶與 AKS 群集一起使用之前,必須創建角色綁定或群集角色綁定。 「角色」會定義要授與的權限，而「繫結」會將角色套用至需要的使用者。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

首先,使用帶有參數的`--admin`az [aks 獲取憑據][az-aks-get-credentials]命令登錄到具有管理員訪問許可權的群集。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

接下來,為要授予 AKS 群集訪問許可權的 Azure AD 帳戶創建群集角色綁定。 以下範例允許帳戶完全存取群組的所有命名空間:

- 如果為其授予 RBAC 綁定的使用者位於同一 Azure AD 租戶中,則根據使用者主體名稱 (UPN) 分配許可權。 繼續執行步驟,為群集角色綁定創建 YAML 清單。

- 如果用戶位於其他 Azure AD 租戶中,請查詢並使用**objectId**屬性。 如果需要,請使用[az 廣告使用者顯示][az-ad-user-show]命令獲取所需使用者帳戶的物件 ID。 提供所需帳戶的使用者主體名稱 (UPN):

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

創建檔案,如*rbac-aad-user.yaml,* 然後貼上以下內容。 在最後一行,將**userPrincipalName_or_objectId**替換為 UPN 或對象 ID。 選擇取決於使用者是否為同一 Azure AD 租戶。

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

使用[kubectl 應用程式][kubectl-apply]應用程式,系統系統應用綁定,例如以下範例所示:

```console
kubectl apply -f rbac-aad-user.yaml
```

此外，也可以為 Azure AD 群組的所有成員建立角色繫結。 使用組物件 ID 指定 Azure AD 組,如以下範例所示。

創建檔案,如*rbac-aad-group.yaml,* 然後粘貼以下內容。 以 Azure AD 租用戶中的群組物件識別碼來更新使用者名稱。

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

使用[kubectl 應用程式][kubectl-apply]應用程式,系統系統應用綁定,例如以下範例所示:

```console
kubectl apply -f rbac-aad-group.yaml
```

如需有關使用 RBAC 保護 Kubernetes 叢集的詳細資訊，請參閱[使用 RBAC 授權][rbac-authorization]。

## <a name="access-the-cluster-with-azure-ad"></a>使用 Azure AD 存取叢集

使用[az aks 獲取認證的][az-aks-get-credentials]命令提取非管理員使用者的上下文。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

執行該`kubectl`指令後,系統將提示您使用 Azure 進行身份驗證。 按照螢幕上的說明完成此過程,如以下範例所示:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

該過程完成後,將緩存身份驗證權杖。 僅當權杖過期或重新創建 Kubernetes 配置檔時,才會提示您再次登錄。

如果您在成功登入後看到授權錯誤訊息,請檢查以下條件:

```console
error: You must be logged in to the server (Unauthorized)
```


- 定義適當的物件 ID 或 UPN,具體取決於使用者帳戶是否位於同一 Azure AD 租戶中。
- 使用者不是超過 200 個組的成員。
- 伺服器應用程式註冊中定義的機密與`--aad-server-app-secret`使用 配置的值匹配。

## <a name="next-steps"></a>後續步驟

要使用 Azure AD 使用者與群組來控制對叢集資源的存取,請參考[使用基於角色的存取控制和 AKS 中的 Azure AD 識別對叢集資源的控制存取][azure-ad-rbac]。

有關如何保護 Kubernetes 群集的詳細資訊,請參閱[AKS 的存取和識別選項][rbac-authorization]。

要瞭解有關識別和資源控制的更多資訊,請參閱[AKS 中身份驗證與授權的最佳做法][operator-best-practices-identity]。

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
