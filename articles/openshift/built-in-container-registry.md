---
title: 設定 Azure Red Hat OpenShift 4 的內建 container registry
description: 設定 Azure Red Hat OpenShift 4 的內建 container registry
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414411"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>設定 Azure Red Hat OpenShift 4 的內建 container registry

在本文中，您將設定 Azure Red Hat OpenShift (ARO) 4 叢集的內建容器映射登錄。 您將學習如何：

> [!div class="checklist"]
> * 設定 Azure AD
> * 設定 OpenID Connect
> * 存取內建容器映射登錄

## <a name="prerequisites"></a>先決條件

* 遵循 [建立 Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster)叢集中的步驟來建立叢集。 請務必使用的 `--pull-secret` 引數來建立叢集 `az aro create` 。  如果您想要設定登入 Azure AD （如本文所要求），則這是必要的。
* 遵循 [連接至 Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster)叢集中的步驟，連接到叢集。
   * 請務必遵循「安裝 OpenShift CLI」中的步驟，因為我們將 `oc` 在本文稍後使用命令。
   * 記下叢集主控台 URL，如下所示 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 。 和的值 `<random>` `<region>` 將在本文稍後使用。
   * 請注意 `kubeadmin` 認證。 本文稍後將會用到這些專案。

## <a name="set-up-azure-active-directory"></a>設定 Azure Active Directory

Azure Active Directory (Azure AD) OpenID Connect (OIDC) 。 OIDC 可讓您使用 Azure AD 登入 ARO 叢集。 請遵循下列步驟來設定 Azure AD。

1. 遵循 [快速入門：設定租](/azure/active-directory/develop/quickstart-create-new-tenant)使用者中的步驟來設定 Azure AD 租使用者。 您的 Azure 帳戶可能已有租使用者。 如果是這樣，如果您在租使用者中有足夠的許可權來執行這些步驟，您可以略過建立一個。 請注意您的 **租使用者識別碼** 。 稍候會用到此值。
2. 遵循 [ [新增或刪除使用 Azure Active Directory 的使用者](/azure/active-directory/fundamentals/add-users-azure-active-directory)] 中的步驟，建立至少一個 Azure AD 使用者。 您可以使用這些帳戶或您自己的帳戶來測試應用程式。 記下這些帳戶的電子郵件地址和密碼以進行登入。
3. 遵循 [快速入門：使用 Microsoft 身分識別平臺註冊應用程式](/azure/active-directory/develop/quickstart-register-app)中的步驟，在您的 Azure AD 租使用者中建立新的應用程式註冊。 
   1. 回想有關和值的必要條件中的注意事項 `<random>` `<region>` 。 使用這些值，根據下列公式建立 URI：

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. 當您到達處理 [重新 **導向 URI** ] 欄位的步驟時，請輸入上一個步驟中的 URI。
   1. 選取 [註冊]。
   1. 在應用程式的 [ **總覽** ] 頁面上，記下 [ **應用程式 (用戶端) 識別碼** ] 所顯示的值，如下所示。
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Azure AD 應用程式的 [總覽] 頁面。":::

4. 建立新的用戶端密碼。 
   1. 在新建立的應用程式註冊中，選取左側流覽窗格中的 [ **憑證 & 秘密** ]。
   1. 選取 [新增用戶端密碼]。
   1. 提供 **描述** ，然後選取 [ **新增** ]。
   1. 將產生的 **用戶端秘密** 值保留在一旁。 本文稍後將會用到此值。

### <a name="add-openid-connect-identity-provider"></a>新增 OpenID Connect 身分識別提供者

ARO 提供內建的容器登錄。  若要存取它，請遵循下列步驟，使用 Azure AD OIDC (IDP) 設定身分識別提供者。

1. 從您的瀏覽器登入 OpenShift web 主控台，如下所示 `kubeadmin` 。  這與執行 [教學課程：連線到 Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster)叢集中的步驟時所使用的程式相同。
1. 在左側流覽窗格中，選取 [ **管理** 叢集  >  **設定** ]。
1. 在頁面的中間，選取 [ **全域** 設定]。
1. 在資料表的設定 **資源** 資料行中尋找 **OAuth** ，然後選取它。
1. 在 [ **識別提供者** ] 下，選取 [ **新增** ]，然後選擇 **OpenID Connect** 。
1. 將 **名稱** 設定為 **openid** 。  此值可能已經填入。
1. 將 **用戶端識別碼** 和 **用戶端密碼** 設定為上一個步驟中的值。
1. 遵循此步驟來尋找 **簽發者 URL** 的值。
   1. 取代 **\<tenant-id>** 為在 URL 中 **設定 Azure Active Directory** 期間所儲存的內容 `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` 。
   1. 在您用來與 Azure 入口網站互動的相同瀏覽器中開啟 URL。
   1. 複製所傳回 JSON 主體中的屬性 **簽發者** 值，並將它貼到標示為 [ **簽發者 URL** ] 的文字方塊中。  **簽發者** 值看起來會像這樣 `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` 。
1. 移至頁面底部，然後選取 [ **新增** ]。
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Connect 在 OpenShift 中。":::
1. 選取瀏覽器視窗右上方的 [ **kube： admin** ] \ （系統管理員 \）按鈕，然後選擇 [ **登出** ]，登出 OpenShift web 主控台。

### <a name="access-the-built-in-container-image-registry"></a>存取內建容器映射登錄

下列指示可讓您存取內建登錄。

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>將 Azure AD 使用者定義為系統管理員

1. 使用 Azure AD 使用者的認證，從瀏覽器登入 OpenShift web 主控台。

   1. 您可以使用 InPrivate、Incognito 或其他對等的瀏覽器視窗功能來登入主控台。
   1. 在啟用 OIDC 之後，此視窗看起來會不同。
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect 已啟用登入] 視窗。":::
   1. 選取 **openid**

   > [!NOTE]
   > 記下您用來登入的使用者名稱和密碼。 此使用者名稱和密碼會以系統管理員的身分來執行，以及其他文章中的其他動作。
1. 使用 OpenShift CLI 登入，方法是使用下列步驟。  針對討論，此程式稱為 `oc login` 。
   1. 在 web 主控台的右上方，展開已登入使用者的內容功能表，然後選取 [ **複製登** 入] 命令。
   1. 如有必要，請使用相同的使用者登入新的索引標籤視窗。
   1. 選取 [ **顯示標記** ]。
   1. 將下列以 **此權杖登** 入的值複製到剪貼簿，然後在 shell 中執行，如下所示。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. `oc whoami`在主控台中執行，並記下輸出，如下所示 **\<aad-user>** 。  我們稍後將在本文中使用此值。
1. 登出 OpenShift web 主控台。 選取標示為的瀏覽器視窗右上角的按鈕 **\<aad-user>** ，然後選擇 [ **登出** ]。


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>將登錄互動的必要角色授與 Azure AD 使用者

1. 使用認證，從瀏覽器登入 OpenShift web 主控台 `kubeadmin` 。
1. 遵循上述步驟，使用的權杖登入 OpenShift CLI `kubeadmin` `oc login` ，但在使用登入 web 主控台後執行這些步驟 `kubeadmin` 。
1. 執行下列命令，以啟用 **aad 使用者** 的內建登錄存取權。

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   輸出看起來應該會如下所示。

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   輸出看起來應該會如下所示。

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   輸出看起來應該會如下所示。

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   輸出看起來應該會如下所示。

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>取得 container registry URL

使用 `oc get route` 下圖所示的命令來取得容器登錄 URL。

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > 請注意 **Container REGISTRY URL** 的主控台輸出。 它將用來作為本指南的完整登錄名稱，以及後續的登錄名稱。

## <a name="next-steps"></a>後續步驟

在 OpenShift 上部署應用程式，以使用內建的容器映射登錄。  若為 JAVA 應用程式，請遵循操作指南， [使用 Azure Red Hat OpenShift 4 叢集上的 Open 可自由/WebSphere 來部署 JAVA 應用程式](howto-deploy-java-liberty-app.md)。
