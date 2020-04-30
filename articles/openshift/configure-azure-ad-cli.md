---
title: 執行 OpenShift 4 的 Azure Red Hat OpenShift-使用命令列設定 Azure Active Directory 驗證
description: 瞭解如何使用命令列為執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集設定 Azure Active Directory 驗證
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc
ms.openlocfilehash: 45da3034891e5a82fb8423adb6bcd5e867f9d4e2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204997"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>設定 Azure Red Hat OpenShift 4 叢集的 Azure Active Directory 驗證（CLI）

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 版2.0.75 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

抓取即將用來設定 Azure Active Directory 應用程式的叢集特定 Url。

建立叢集的 OAuth 回呼 URL，並將它儲存在變數**oauthCallbackURL**中。 請務必以您的資源群組名稱取代**aro-rg** ，並以您的叢集名稱取代**aro-cluster** 。

> [!NOTE]
> OAuth `AAD`回呼 URL 中的區段應符合您稍後將會設定的 oauth 身分識別提供者名稱。

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>建立用於驗證的 Azure Active Directory 應用程式

建立 Azure Active Directory 應用程式，並取出已建立的應用程式識別碼。 以安全密碼取代** \<ClientSecret>** 。

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

您應該會得到類似下面的內容。 請記下它，因為這是您在後續步驟中需要的**AppId** 。

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

取出擁有應用程式之訂用帳戶的租使用者識別碼。

```azure
az account show --query tenantId -o tsv
```

您應該會得到類似下面的內容。 請記下它，因為這是您在後續步驟中需要的**TenantId** 。

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>建立資訊清單檔，以定義要包含在識別碼權杖中的選擇性宣告

應用程式開發人員可以在其 Azure AD 應用程式中使用[選擇性宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)，在傳送至其應用程式的權杖中指定他們想要的宣告。

您可以使用選擇性宣告來：

- 選取要包含在應用程式之權杖中的額外宣告。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。

我們會將 OpenShift 設定為使用`email`宣告，並藉由`upn`將新增`upn`為 Azure Active Directory 所傳回之識別碼權杖的一部分，來切換回以設定慣用的使用者名稱。

建立**資訊清單 json**檔案來設定 Azure Active Directory 應用程式。

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>使用資訊清單更新 Azure Active Directory 應用程式的 optionalClaims

以您稍早所擁有的識別碼取代** \<AppID>** 。

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>更新 Azure Active Directory 應用程式範圍許可權

為了能夠從 Azure Active Directory 讀取使用者資訊，我們必須定義適當的範圍。

以您稍早所擁有的識別碼取代** \<AppID>** 。

Azure Active Directory Graph 的 [新增] 許可權。 [**使用者]** 的 [讀取範圍]，以啟用登入和讀取使用者設定檔。

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> 除非您是以此 Azure Active Directory 的全域管理員身分進行驗證，否則可以忽略訊息以授與同意，因為當您登入自己的帳戶時，系統會要求您執行此動作。

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>將使用者和群組指派給叢集（選擇性）

根據預設，在 Azure Active Directory (Azure AD) 租用戶中註冊的應用程式可供成功通過驗證的所有租用戶使用者使用。 Azure AD 可讓租用戶系統管理員和開發人員將應用程式限制為僅供租用戶中的一組特定使用者或安全性群組存取。

依照 Azure Active Directory 檔中的指示，[將使用者和群組指派給應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration)。

## <a name="configure-openshift-openid-authentication"></a>設定 OpenShift OpenID 驗證

取出`kubeadmin`認證。 執行下列命令來尋找`kubeadmin`使用者的密碼。

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

下列範例輸出顯示密碼將在中`kubeadminPassword`。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

使用下列命令登入 OpenShift 叢集的 API 伺服器。 `$apiServer`變數[先前]()已設定。 以您所抓取的密碼取代** \<kubeadmin password>** 。

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

建立 OpenShift 秘密來儲存 Azure Active Directory 應用程式密碼，將** \<ClientSecret>** 取代為您稍早取得的密碼。

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```    

建立**oidc yaml**檔案，針對 Azure Active Directory 設定 OpenShift OpenID 驗證。 以您稍早取得的值取代** \<AppID>** 和** \<TenantId>** 。

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: <AppId>
      clientSecret: 
        name: openid-client-secret-azuread
      extraScopes: 
      - email
      - profile
      extraAuthorizeParameters: 
        include_granted_scopes: "true"
      claims:
        preferredUsername: 
        - email
        - upn
        name: 
        - name
        email: 
        - email
      issuer: https://login.microsoftonline.com/<TenantId>
EOF
```

將設定套用至叢集。

```azurecli-interactive
oc apply -f oidc.yaml
```

您會收到如下所示的回應。

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>驗證透過 Azure Active Directory 登入

如果您現在登出 OpenShift Web 主控台並嘗試再次登入，您將會看到新的選項，可供您使用**AAD**登入。 您可能需要等候幾分鐘的時間。

![具有 Azure Active Directory 選項的登入畫面](media/aro4-login-2.png)
