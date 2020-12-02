---
title: 設定適用於 Azure Red Hat OpenShift 4 的內建容器登錄
description: 設定適用於 Azure Red Hat OpenShift 4 的內建容器登錄
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 9ed53721b66dc03bad24e0510e0c8a970c61aec1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492415"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>設定適用於 Azure Red Hat OpenShift 4 的內建容器登錄

Azure Red Hat OpenShift 提供整合的容器映射登錄，稱為 [OpenShift Container registry (OCR) ](https://docs.openshift.com/aro/4/registry/architecture-component-imageregistry.html) 可讓您視需要自動布建新的映射存放庫。 這會為使用者提供內建的應用程式組建位置，以推送產生的影像。

在本文中，您將設定 Azure Red Hat OpenShift (ARO) 4 叢集的內建容器映射登錄。 您將學習如何：

> [!div class="checklist"]
> * 設定 Azure AD
> * 設定 OpenID Connect
> * 存取內建容器映射登錄

## <a name="before-you-begin"></a>開始之前

本文假設您已有現有的 ARO 叢集。 如果您需要 ARO 叢集，請參閱 ARO 教學課程， [建立 Azure Red Hat OpenShift 4](./tutorial-create-cluster.md)叢集。 請務必使用的 `--pull-secret` 引數來建立叢集 `az aro create` 。  這是設定 Azure Active Directory authentication 和內建容器登錄的必要步驟。

當您擁有叢集之後，請遵循 [連接至 Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md)叢集中的步驟，連接到叢集。
   * 請務必遵循「安裝 OpenShift CLI」中的步驟，因為我們將 `oc` 在本文稍後使用命令。
   * 記下叢集主控台 URL，如下所示 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 。 和的值 `<random>` `<region>` 將在本文稍後使用。
   * 請注意 `kubeadmin` 認證。 本文稍後也會用到這些專案。

### <a name="configure-azure-active-directory-authentication"></a>設定 Azure Active Directory 驗證 

Azure Active Directory (Azure AD) OpenID Connect (OIDC) 。 OIDC 可讓您使用 Azure AD 登入 ARO 叢集。 依照設定 [Azure Active Directory authentication](configure-azure-ad-cli.md) 中的步驟來設定您的叢集。

## <a name="access-the-built-in-container-image-registry"></a>存取內建容器映射登錄

現在您已設定 ARO 叢集的驗證方法，讓我們啟用內建登錄的存取。

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>將 Azure AD 使用者定義為系統管理員

1. 使用 Azure AD 使用者的認證，從瀏覽器登入 OpenShift web 主控台。 我們將利用 OpenShift OpenID 驗證進行 Azure Active Directory，以使用 OpenID 來定義系統管理員。

   1. 您可以使用 InPrivate、Incognito 或其他對等的瀏覽器視窗功能來登入主控台。 在啟用 OIDC 之後，此視窗看起來會不同。
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect 已啟用登入] 視窗。":::
   1. 選取 **openid**

   > [!NOTE]
   > 記下您用來登入的使用者名稱和密碼。 此使用者名稱和密碼會以系統管理員的身分來執行，以及其他文章中的其他動作。
2. 使用 OpenShift CLI 登入，方法是使用下列步驟。  針對討論，此程式稱為 `oc login` 。
   1. 在 web 主控台的右上方，展開已登入使用者的內容功能表，然後選取 [ **複製登** 入] 命令。
   2. 如有必要，請使用相同的使用者登入新的索引標籤視窗。
   3. 選取 [ **顯示標記**]。
   4. 將下列以 **此權杖登** 入的值複製到剪貼簿，然後在 shell 中執行，如下所示。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. `oc whoami`在主控台中執行，並記下輸出，如下所示 **\<aad-user>** 。  我們稍後將在本文中使用此值。
4. 登出 OpenShift web 主控台。 選取標示為的瀏覽器視窗右上角的按鈕 **\<aad-user>** ，然後選擇 [ **登出**]。


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>將登錄互動的必要角色授與 Azure AD 使用者

1. 使用認證，從瀏覽器登入 OpenShift web 主控台 `kubeadmin` 。
1. 遵循上述步驟，使用的權杖登入 OpenShift CLI `kubeadmin` `oc login` ，但在使用登入 web 主控台後執行這些步驟 `kubeadmin` 。
1. 執行下列命令，以啟用 **aad 使用者** 的內建登錄存取權。

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
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

現在您已設定內建容器映射登錄，接下來您可以在 OpenShift 上部署應用程式來開始著手。 若為 JAVA 應用程式，請參閱 [在 Azure Red Hat OpenShift 4 叢集上使用開放式的自由/WebSphere 來部署 java 應用程式](howto-deploy-java-liberty-app.md)。