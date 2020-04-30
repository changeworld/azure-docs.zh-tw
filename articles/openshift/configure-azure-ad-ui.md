---
title: 執行 OpenShift 4 的 Azure Red Hat OpenShift-使用 Azure 入口網站和 OpenShift web 主控台來設定 Azure Active Directory 驗證
description: 瞭解如何使用 Azure 入口網站和 OpenShift web 主控台，為執行 OpenShift 4 的 Azure Red Hat OpenShift 叢集設定 Azure Active Directory 驗證
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc
ms.openlocfilehash: 6b6248aac35c22b9ffd2cd95df41e84986356259
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205309"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>設定 Azure Red Hat OpenShift 4 叢集的 Azure Active Directory 驗證（入口網站）

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 版2.0.75 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>開始之前

建立叢集的**OAuth 回呼 URL** ，並記下它。 請務必以您的資源群組名稱取代**aro-rg** ，並以您的叢集名稱取代**aro-cluster** 。

> [!NOTE]
> OAuth `AAD`回呼 URL 中的區段應符合您稍後將會設定的 oauth 身分識別提供者名稱。

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>建立用於驗證的 Azure Active Directory 應用程式

登入 Azure 入口網站，然後流覽至 [[應用程式註冊](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)] 分頁，然後按一下 [**新增註冊**] 以建立新的應用程式。

提供應用程式的名稱（例如， **aro-azuread-auth**），並使用您先前抓取的 OAUTH 回呼 URL 值填入重新**導向 URI** 。

![新增應用程式註冊](media/aro4-ad-registerapp.png)

流覽至 [**憑證 & 密碼**]，然後按一下 [**新增用戶端密碼**] 並填入詳細資料。 請記下金鑰值，因為您將在稍後的階段中使用它。 您將無法再重新取得。

![建立祕密](media/aro4-ad-clientsecret.png)

流覽至 [**總覽**]，並記下 [**應用程式（用戶端）識別碼**] 和 [**目錄（租使用者）] 識別碼**。 您在稍後的階段將需要這些專案。

![取得應用程式（用戶端）和目錄（租使用者）識別碼](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>設定選擇性宣告

應用程式開發人員可以在其 Azure AD 應用程式中使用[選擇性宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)，在傳送至其應用程式的權杖中指定他們想要的宣告。

您可以使用選擇性宣告來：

* 選取要包含在應用程式之權杖中的額外宣告。
* 變更 Azure AD 在權杖中傳回之特定宣告的行為。
* 新增和存取應用程式的自訂宣告。

我們會將 OpenShift 設定為使用`email`宣告，並藉由`upn`將新增`upn`為 Azure Active Directory 所傳回之識別碼權杖的一部分，來切換回以設定慣用的使用者名稱。

流覽至 **[權杖設定（預覽）** ]，然後按一下 [**新增選擇性**宣告]。 選取 [**識別碼**]，然後檢查**電子郵件**和**upn**宣告。

![建立祕密](media/aro4-ad-tokens.png)

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

您可以執行下列命令來尋找叢集主控台 URL，如下所示：`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

在瀏覽器中啟動主控台 URL，並使用`kubeadmin`認證登入。

流覽至 [系統**管理**]，按一下 [叢集設定]，然後選取 [**全域****設定**] 索引標籤。滾動以選取 [ **OAuth**]。

在 [**識別提供者**] 下向下選取 [**新增**]，然後選取 **[OpenID connect]**。
![從 [識別提供者] 下拉式清單中選取 [OpenID Connect]](media/aro4-oauth-idpdrop.png)

將名稱填入**AAD**、**用戶端識別碼**作為 [**應用程式識別碼**] 和 [**用戶端密碼**]。 **簽發者 URL**的格式如下： `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 以您稍早取得的租使用者識別碼取代預留位置。

![填寫 OAuth 詳細資料](media/aro4-oauth-idp-1.png)

向下流覽至 [**宣告**] 區段，並更新慣用的使用者**名稱**，以使用來自**upn**宣告的值。

![填寫宣告詳細資料](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>驗證透過 Azure Active Directory 登入

如果您現在登出 OpenShift Web 主控台並再次嘗試登入，您將會看到一個新選項，可供您使用**AAD**登入。 您可能需要等候幾分鐘的時間。

![具有 Azure Active Directory 選項的登入畫面](media/aro4-login-2.png)
