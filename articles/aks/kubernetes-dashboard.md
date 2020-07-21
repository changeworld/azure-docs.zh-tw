---
title: 使用 Web 儀表板來管理 Azure Kubernetes Service 叢集
description: 了解如何使用內建的 Kubernetes Web UI 儀表板來管理 Azure Kubernetes Service (AKS) 叢集
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 69e60c3e4ac91a5d0ca9a0245dc61f090c625c60
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499854"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>存取 Azure Kubernetes Service (AKS) 中的 Kubernetes Web 儀表板

Kubernetes 包含的 Web 儀表板可用來執行基本的管理作業。 此儀表板可讓您檢視您應用程式的基本健全狀況狀態和計量、建立和部署服務，以及編輯現有的應用程式。 本文將說明如何使用 Azure CLI 來存取 Kubernetes 儀表板，然後引導您完成一些基本的儀表板作業。

如需 Kubernetes 儀表板的詳細資訊，請參閱[Kubernetes WEB UI 儀表板][kubernetes-dashboard]。 AKS 使用2.0 版和更高版本的開放原始碼儀表板。

> [!WARNING]
> **AKS 儀表板附加元件已設定為取代。** 
> * 針對執行 Kubernetes 版本小於1.18 的叢集，預設會啟用 Kubernetes 儀表板。
> * 針對在 Kubernetes 1.18 或更新版本上建立的所有新叢集，預設會停用儀表板附加元件。 
 > * 從 Kubernetes 1.19 預覽版開始，AKS 將不再支援安裝受管理的 kube 儀表板附件。 
 > * 已啟用附加元件的現有叢集將不會受到影響。 使用者會繼續以使用者安裝的軟體的形式，手動安裝 [開放原始碼] 儀表板。

## <a name="before-you-begin"></a>開始之前

本檔中詳述的步驟假設您已建立 AKS 叢集，並已建立與叢集的連線 `kubectl` 。 如果您需要建立 AKS 叢集，請參閱[快速入門：使用 Azure CLI 部署 Azure Kubernetes Service][aks-quickstart]叢集。

您也需要安裝並設定 Azure CLI 版本2.6.0 或更新版本。 執行  `az --version`  以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="disable-the-kubernetes-dashboard"></a>停用 Kubernetes 儀表板

在**K8s 1.18 之前**的叢集上，預設會啟用 kube 儀表板附件。 您可以執行下列命令來停用此附加元件。

``` azure-cli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>啟動 Kubernetes 儀表板

若要在叢集上啟動 Kubernetes 儀表板，請使用[az aks browse][az-aks-browse]命令。 此命令需要在叢集上安裝 kube-儀表板附加元件，此附加元件預設會包含在執行任何比 Kubernetes 1.18 舊版本的叢集上。

下列範例會在名為 myResourceGroup** 的資源群組中，針對名為 myAKSCluster** 的叢集開啟儀表：

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

此命令會在您的開發系統與 Kubernetes API 之間建立 Proxy，並開啟 Kubernetes 儀表板的網頁瀏覽器。 如果 Web 瀏覽器未開啟至 Kubernetes 儀表板，請複製並貼上 Azure CLI 中所註明的 URL 位址，通常是 `http://127.0.0.1:8001`。

> [!NOTE]
> 如果您沒有看到 [儀表板]， `http://127.0.0.1:8001` 您可以手動路由傳送到下列位址。 1.16 或更高版本上的叢集使用 HTTPs，而且需要個別的端點。
> * K8s 1.16 或更新版本：`http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 和以下：`http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>登入儀表板（kubernetes 1.16 +）

> [!IMPORTANT]
> [從 Kubernetes 儀表板](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1)或 Kubernetes v 1.16 的 v 1.10.1 版，因為[該版本中的安全性修正，](https://github.com/kubernetes/dashboard/pull/3400)所以無法再使用服務帳戶「Kubernetes-儀表板」來抓取資源。 因此，沒有 auth 資訊的要求會傳回401未經授權的錯誤。 從服務帳戶抓取的持有人權杖仍可在此[Kubernetes 儀表板範例](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui)中使用，但這會影響儀表板附加元件的登入流程（相較于舊版本）。
>
>如果您仍然執行1.16 之前的版本，您仍然可以將許可權授與「kubernetes-儀表板」服務帳戶，但**不建議**您這樣做：
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

顯示的初始畫面需要 kubeconfig 或 token。 這兩個選項都需要資源許可權，才能在儀表板中顯示這些資源。

![登入畫面](./media/kubernetes-dashboard/login.png)

**使用 kubeconfig**

Azure AD 啟用和非 Azure AD 啟用的叢集，可以傳入 kubeconfig。 請確認存取權杖有效，如果您的權杖已過期，您可以透過 kubectl 重新整理權杖。

1. 將 admin kubeconfig 設定為`az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. 選取 `Kubeconfig` 並按一下 `Choose kubeconfig file` 以開啟 [檔案選取器]
1. 選取您的 kubeconfig 檔案（預設為 $HOME/.kube/config）
1. 按一下 `Sign In`

**使用權杖**

1. 針對**未啟用 Azure AD**的叢集，請執行 `kubectl config view` 並複製與叢集的使用者帳戶相關聯的權杖。
1. 在登入時，貼到 [權杖] 選項。    
1. 按一下 `Sign In`

對於已啟用 Azure AD 的叢集，請使用下列命令來取出 AAD 權杖。 驗證您已取代命令中的資源群組和叢集名稱。

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

成功之後，將會顯示類似下面的頁面。

![Kubernetes Web 儀表板的概觀頁面](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>建立應用程式

下列步驟需要使用者擁有個別資源的許可權。 

為了了解 Kubernetes 儀表板如何降低管理工作的複雜性，我們將建立一個應用程式。 您可以從 Kubernetes 儀表板提供文字輸入、YAML 檔案或透過圖形化精靈來建立應用程式。

若要建立應用程式，請完成下列步驟：

1. 選取右上方視窗中的 [建立]**** 按鈕。
1. 若要使用圖形化精靈，請選擇 [建立應用程式]****。
1. 為部署提供名稱，例如 *nginx*
1. 輸入要使用之容器映像的名稱，例如 *nginx:1.15.5*
1. 若要公開連接埠 80 以供 Web 流量使用，您需建立 Kubernetes 服務。 在 [服務]**** 底下，選取 [外部]****，然後針對連接埠和目標連接埠輸入 **80**。
1. 準備就緒時，選取 [部署]**** 來建立應用程式。

![在 Kubernetes Web 儀表板中建立應用程式](./media/kubernetes-dashboard/create-app.png)

將公用外部 IP 位址指派給 Kubernetes 服務需要一兩分鐘的時間。 在左側的 [探索與負載平衡]**** 底下，選取 [服務]****。 其中會列出您的應用程式 (包括 [外部端點]**)，如以下範例所示：

![檢視服務與端點清單](./media/kubernetes-dashboard/view-services.png)

選取端點位址以將網頁瀏覽器視窗開啟至預設的 NGINX 頁面：

![檢視所部署應用程式的預設 NGINX 頁面](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>檢視 Pod 資訊

Kubernetes 儀表板可以提供基本監視計量，以及針對資訊 (例如記錄) 進行疑難排解。

若要查看有關您應用程式 Pod 的詳細資訊，請選取左側功能表中的 [Pod]****。 隨即會顯示可用的 Pod 清單。 請選擇您的 *nginx* Pod 以檢視資訊，例如資源耗用量：

![檢視 Pod 資訊](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>編輯應用程式

除建立和檢視應用程式之外，Kubernetes 儀表板還可用來編輯和更新應用程式部署。 為了為應用程式提供額外的備援，我們將增加 NGINX 複本的數目。

編輯部署：

1. 選取左側功能表中的 [部署]****，然後選擇您的 *nginx* 部署。
1. 選取右上方導覽列中的 [編輯]****。
1. 找出 `spec.replica` 值 (大約在第 20 行)。 若要增加應用程式複本的數目，請將此值從 *1* 變更為 *3*。
1. 就緒後請選取 [更新]****。

![編輯部署以更新複本數目](./media/kubernetes-dashboard/edit-deployment.png)

在複本集內建立新 Pod 需要一些時間。 請在左側功能表上，選擇 [複本集]****，然後選擇您的 *nginx* 複本集。 Pod 清單現在會反映已更新的複本計數，如以下範例輸出所示：

![檢視複本集的相關資訊](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>後續步驟

如需有關 Kubernetes 儀表板的詳細資訊，請參閱 [Kubernetes Web UI 儀表板][kubernetes-dashboard]。

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
