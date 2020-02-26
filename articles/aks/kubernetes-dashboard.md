---
title: 使用 Web 儀表板來管理 Azure Kubernetes Service 叢集
description: 了解如何使用內建的 Kubernetes Web UI 儀表板來管理 Azure Kubernetes Service (AKS) 叢集
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595343"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>存取 Azure Kubernetes Service (AKS) 中的 Kubernetes Web 儀表板

Kubernetes 包含的 Web 儀表板可用來執行基本的管理作業。 此儀表板可讓您檢視您應用程式的基本健全狀況狀態和計量、建立和部署服務，以及編輯現有的應用程式。 本文將說明如何使用 Azure CLI 來存取 Kubernetes 儀表板，然後引導您完成一些基本的儀表板作業。

如需 Kubernetes 儀表板的詳細資訊，請參閱[Kubernetes WEB UI 儀表板][kubernetes-dashboard]。

## <a name="before-you-begin"></a>開始之前

本文件中詳述的步驟假設您已建立 AKS 叢集，並建立與叢集的 `kubectl` 連線。 如果您需要建立 AKS 叢集，請參閱[AKS 快速入門][aks-quickstart]。

您也必須安裝並設定 Azure CLI 版本 2.0.46 或更新版本。 執行  `az --version`  以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="start-the-kubernetes-dashboard"></a>啟動 Kubernetes 儀表板

若要啟動 Kubernetes 儀表板，請使用[az aks browse][az-aks-browse]命令。 下列範例會在名為 myResourceGroup 的資源群組中，針對名為 myAKSCluster 的叢集開啟儀表：

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

此命令會在您的開發系統與 Kubernetes API 之間建立 Proxy，並開啟 Kubernetes 儀表板的網頁瀏覽器。 如果網頁瀏覽器未開啟至 Kubernetes 儀表板，請複製並貼上 Azure CLI 所記下的 URL 位址，通常 `http://127.0.0.1:8001`。

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
-->

> [!IMPORTANT]
> 如果您的 AKS 叢集已使用 RBAC，必須先建立 ClusterRoleBinding，才能正確地存取儀表板。 根據預設，Kubernetes 儀表板會部署最低限度的讀取權限，並且會顯示 RBAC 存取錯誤。 Kubernetes 儀表板目前不支援透過使用者提供的認證，來決定存取層級，而是使用授與服務帳戶的角色。 叢集系統管理員可選擇將其他存取權授與 kubernetes-dashboard 服務帳戶，但這可能是權限提高的向量。 您也可以整合 Azure Active Directory 驗證，以提供更細微的存取層級。
> 
> 若要建立系結，請使用[kubectl create clusterrolebinding][kubectl-create-clusterrolebinding]命令。 下列範例顯示如何建立範例系結，不過，此範例系結不會套用任何其他驗證元件，而且可能會導致不安全的使用。 可存取此 URL 的任何人都可使用 Kubernetes 儀表板。 請不要讓 Kubernetes 儀表板可公開使用。
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> 如需使用不同驗證方法的詳細資訊，請參閱[存取控制][dashboard-authentication]上的 Kubernetes 儀表板 wiki。

![Kubernetes Web 儀表板的概觀頁面](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>建立應用程式

為了了解 Kubernetes 儀表板如何降低管理工作的複雜性，我們將建立一個應用程式。 您可以從 Kubernetes 儀表板提供文字輸入、YAML 檔案或透過圖形化精靈來建立應用程式。

若要建立應用程式，請完成下列步驟：

1. 選取右上方視窗中的 [建立] 按鈕。
1. 若要使用圖形化精靈，請選擇 [建立應用程式]。
1. 為部署提供名稱，例如 *nginx*
1. 輸入要使用之容器映像的名稱，例如 *nginx:1.15.5*
1. 若要公開連接埠 80 以供 Web 流量使用，您需建立 Kubernetes 服務。 在 [服務] 底下，選取 [外部]，然後針對連接埠和目標連接埠輸入 **80**。
1. 準備就緒時，選取 [部署] 來建立應用程式。

![在 Kubernetes Web 儀表板中建立應用程式](./media/kubernetes-dashboard/create-app.png)

將公用外部 IP 位址指派給 Kubernetes 服務需要一兩分鐘的時間。 在左側的 [探索與負載平衡] 底下，選取 [服務]。 其中會列出您的應用程式 (包括 [外部端點])，如以下範例所示：

![檢視服務與端點清單](./media/kubernetes-dashboard/view-services.png)

選取端點位址以將網頁瀏覽器視窗開啟至預設的 NGINX 頁面：

![檢視所部署應用程式的預設 NGINX 頁面](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>檢視 Pod 資訊

Kubernetes 儀表板可以提供基本監視計量，以及針對資訊 (例如記錄) 進行疑難排解。

若要查看有關您應用程式 Pod 的詳細資訊，請選取左側功能表中的 [Pod]。 隨即會顯示可用的 Pod 清單。 請選擇您的 *nginx* Pod 以檢視資訊，例如資源耗用量：

![檢視 Pod 資訊](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>編輯應用程式

除建立和檢視應用程式之外，Kubernetes 儀表板還可用來編輯和更新應用程式部署。 為了為應用程式提供額外的備援，我們將增加 NGINX 複本的數目。

編輯部署：

1. 選取左側功能表中的 [部署]，然後選擇您的 *nginx* 部署。
1. 選取右上方導覽列中的 [編輯]。
1. 找出 `spec.replica` 值 (大約在第 20 行)。 若要增加應用程式複本的數目，請將此值從 *1* 變更為 *3*。
1. 就緒後請選取 [更新]。

![編輯部署以更新複本數目](./media/kubernetes-dashboard/edit-deployment.png)

在複本集內建立新 Pod 需要一些時間。 請在左側功能表上，選擇 [複本集]，然後選擇您的 *nginx* 複本集。 Pod 清單現在會反映已更新的複本計數，如以下範例輸出所示：

![檢視複本集的相關資訊](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>後續步驟

如需 Kubernetes 儀表板的詳細資訊，請參閱[Kubernetes WEB UI 儀表板][kubernetes-dashboard]。

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
