---
title: 教學課程 - 使用 Jenkins 從 GitHub 部署至 Azure Kubernetes Service (AKS)
description: 設定 Jenkins 以進行從 GitHub 的持續整合 (CI) 以及對 Azure Kubernetes Service (AKS) 的持續部署 (CD)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: eb48a8558aab6c7a832efe45650686d9df0d7426
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624744"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>教學課程：使用 Jenkins 持續整合和部署從 GitHub 部署至 Azure Kubernetes Service (AKS)

本教學課程將以 Jenkins 設定持續整合 (CI) 和持續部署 (CD)，將範例應用程式從 GitHub 部署至 [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) 叢集。 這樣一來，當您透過將認可推送至 GitHub 來更新您的應用程式時，Jenkins 會自動執行新的容器組建、將容器映像推送至 Azure Container Registry (ACR)，然後在 AKS 中執行您的應用程式。 

在本教學課程中，您將完成下列工作：

> [!div class="checklist"]
> * 將範例 Azure 投票應用程式部署至 AKS 叢集。
> * 建立基本 Jenkins 專案。
> * 設定可供 Jenkins 與 ACR 進行互動的認證。
> * 建立 Jenkins 建置作業和 GitHub Webhook 來進行自動化建置。
> * 測試 CI/CD 管線以根據 GitHub 程式碼認可來更新 AKS 中的應用程式。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列項目：

- 對 Kubernetes、Git、CI/CD 及容器映像的基本了解

- [AKS 叢集](../aks/kubernetes-walkthrough.md) 及已設定 [AKS 叢集認證](/cli/azure/aks#az-aks-get-credentials)的 `kubectl`。

- [Azure Container Registry (ACR) 登錄](../container-registry/container-registry-get-started-azure-cli.md)、ACR 登入伺服器名稱，以及已設定成[向 ACR 登錄進行驗證](../aks/cluster-container-registry-integration.md)的 AKS 叢集。

- 安裝並設定 Azure CLI 2.0.46 版或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

- [安裝在](https://docs.docker.com/install/)開發系統上的 Docker

- 在開發系統上安裝 GitHub 帳戶[、GitHub 個人訪問權杖](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)和 Git 用戶端

- 如果您提供自己的 Jenkins 執行個體，而不是此範例指令碼方式來部署 Jenkins，則 Jenkins 執行個體需要[安裝及設定 Docker](https://docs.docker.com/install/) 和 [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

## <a name="prepare-your-app"></a>準備您的應用程式

在此文章中，您會使用一個範例 Azure 投票應用程式，此應用程式包含一個裝載在一或多個 Pod 中的 Web 介面，以及裝載 Redis 來作為暫時資料儲存體的第二個 Pod。 在您整合 Jenkins 與 AKS 來進行自動化部署之前，請先將 Azure 投票應用程式手動備妥並部署至 AKS 叢集。 此手動部署是該應用程式的第一版，可讓您了解該應用程式的實際運作情況。

> [!NOTE]
> 示例 Azure 投票應用程式使用計畫在 Linux 節點上運行的 Linux 窗格。 本文中概述的流也適用于在 Windows Server 節點上安排的 Windows Server pod。

為應用程式範例分叉以下 GitHub 存儲庫[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis)- 。 若要將存放庫分支至您自己的 GitHub 帳戶，請選取右上角的 [分支]**** 按鈕。

將該分岔複製到您的開發系統。 複製此存放庫時，請務必使用分岔的 URL：

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

變更至所複製分岔的目錄：

```console
cd azure-voting-app-redis
```

若要建立範例應用程式所需的容器映像，請搭配 `docker-compose` 使用 *docker-compose.yaml* 檔案：

```console
docker-compose up -d
```

這會提取所需的基底映像並建置應用程式容器。 您可以接著使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令來查看所建立的映像。 已下載或建立三個映像。 `azure-vote-front` 映像含有應用程式，並使用 `nginx-flask` 映像做為基礎映像。 `redis` 映像可用來啟動 Redis 執行個體：

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

您必須先使用 [az acr list](/cli/azure/acr#az-acr-list) 命令取得 ACR 登入伺服器，才能將 *azure-vote-front* 容器映像推送至 ACR。 下列範例會取得名為 *myResourceGroup* 之資源群組中某個登錄的 ACR 登入伺服器位址：

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

請使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 命令，來為映像標記 ACR 登入伺服器名稱和版本號碼 `v1`。 提供在上一個步驟中所取得您自己的 `<acrLoginServer>` 名稱：

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

最後，將 *azure-vote-front* 映像推送至您的 ACR 登錄。 同樣地，請將 `<acrLoginServer>` 取代成您自己 ACR 登錄的登入伺服器名稱，例如 `myacrregistry.azurecr.io`：

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>將範例應用程式部署至 AKS

若要將範例應用程式部署至您的 AKS 叢集，您可以使用 Azure 投票存放庫根目錄中的 Kubernetes 資訊清單檔。 請使用編輯器 (例如 `vi`) 來開啟 *azure-vote-all-in-one-redis.yaml* 資訊清單檔。 將 `microsoft` 取代為您的 ACR 登入伺服器名稱。 您可以在資訊清單檔的第 **47** 行找到這個值：

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

接著，使用 [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 命令將應用程式部署至您的 AKS 叢集：

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

系統會建立一個 Kubernetes 負載平衡器服務，以將應用程式公開至網際網路。 此程序需要數分鐘的時間。 若要監視負載平衡器部署的進度，請使用 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令搭配 `--watch` 引數。 當 *EXTERNAL-IP* 位址從 *pending* 變成一個 *IP 位址*之後，請使用 `Control + C` 來停止 kubectl 監看式流程。

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

若要查看應用程式的實際運作情況，請開啟網頁瀏覽器並瀏覽至服務的外部 IP 位址。 隨即會顯示 Azure 投票應用程式，如以下範例所示：

![在 AKS 中執行的 Azure 範例投票應用程式](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>將 Jenkins 部署至 Azure VM

若要快速部署 Jenkins 以在此文章中使用，您可以使用下列指令碼來部署 Azure 虛擬機器、設定網路存取，以及完成 Jenkins 的基本安裝。 針對 Jenkins 與 AKS 叢集之間的驗證，此指令碼會將 Kubernetes 設定檔從開發系統複製到 Jenkins 系統。

> [!WARNING]
> 此範例指令碼僅用於示範，說明如何快速佈建可在 Azure VM 上執行的 Jenkins 環境。 它會使用 Azure 自訂指令碼擴充功能來設定 VM，然後顯示所需的認證。 您的 ~/.kube/config** 會複製到 Jenkins VM。

請執行下列命令來下載及執行指令碼。 在運行腳本之前，應查看任何腳本的內容 。 [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

建立 VM 並為 Docker 和 Jenkins 部署必要的元件需要幾分鐘的時間。 當指令碼完成時，它會輸出 Jenkins 伺服器的位址，以及可將儀表板解除鎖定的金鑰，如以下範例輸出所示：

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

請開啟網頁瀏覽器並瀏覽至所顯示的 URL，然後輸入解除鎖定金鑰。 依照畫面上的提示完成 Jenkins 設定：

- 選擇 [Install suggested plugins] \(安裝建議的外掛程式\)****
- 建立第一位管理使用者。 輸入使用者名稱 (例如 *azureuser*)，然後提供您自己的安全密碼。 最後，輸入完整名稱和電子郵件地址。
- 選取 [儲存並結束]****
- Jenkins 準備就緒之後，選取 [開始使用 Jenkins]****
    - 如果您開始使用 Jenkins 時，網頁瀏覽器顯示空白頁面，請重新啟動 Jenkins 服務。 若要重新啟動服務，請透過 SSH 連線至 Jenkins 執行個體的公用 IP 位址，然後輸入 `sudo service jenkins restart`。 服務重新啟動之後，請重新整理您的網頁瀏覽器。
- 使用您在安裝程序中建立的使用者名稱和密碼來登入 Jenkins。

## <a name="create-a-jenkins-environment-variable"></a>建立 Jenkins 環境變數

Jenkins 環境變數可用來存放 ACR 登入伺服器名稱。 進行 Jenkins 組建作業期間會參考此變數。 若要建立此環境變數，請完成下列步驟：

- 在 Jenkins 門戶的左側，選擇 **"管理詹金斯** > **配置系統**"
- 在 [Global Properties] \(全域屬性\)**** 底下，選取 [Environment variables] \(環境變數\)****。 新增名稱為 `ACR_LOGINSERVER` 的變數，以及您 ACR 登入伺服器的值。

    ![Jenkins 環境變數](media/jenkins-continuous-deployment/env-variables.png)

- 完成時，按一下 Jenkins 設定頁面底部的 [Save] \(儲存\)****。

## <a name="create-a-jenkins-credential-for-acr"></a>為 ACR 建立 Jenkins 認證

若要允許 Jenkins 在建置更新的容器映像後將其推送至 ACR，您必須為 ACR 指定認證。 此驗證可以使用 Azure Active Directory 服務主體。 在先決條件中，您已為 AKS 叢集設定一個具備 ACR 登錄之「讀者」** 權限的服務主體。 這些權限可讓 AKS 叢集從 ACR 登錄「提取」** 映像。 在進行 CI/CD 程序的期間，Jenkins 會根據應用程式更新來建置新的容器映像，且接著需要將這些映像「推送」** 至 ACR 登錄。 為了區隔角色和權限，現在請為 Jenkins 設定一個具備 ACR 登錄之「參與者」** 權限的服務主體。

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>為 Jenkins 建立服務主體以使用 ACR

首先，使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 命令來建立服務主體：

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

記下輸出中顯示的 *appId* 和 *password*。 在接下來的步驟中將會使用這些值來設定 Jenkins 中的認證資源。

使用 [az acr show](/cli/azure/acr#az-acr-show) 命令來取得 ACR 登錄的資源識別碼，然後將它儲存成變數。 提供您的資源群組名稱和 ACR 名稱：

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

現在，建立一個角色指派，以將 ACR 登錄的「參與者」** 權限指派給服務主體。 在下列範例中，提供先前命令輸出中所顯示您自己的 *appId* 以建立服務主體：

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>在 Jenkins 中為 ACR 服務主體建立認證資源

在 Azure 中建立角色指派之後，現在請將 ACR 認證儲存在 Jenkins 認證物件中。 進行 Jenkins 組建作業時，會參考這些認證。

返回 Jenkins 門戶的左側，按一下**憑據** > **Jenkins** > **全域憑據（無限制）** > **添加憑據**

確定認證種類是 [Username with password] \(使用者名稱搭配密碼\)****，然後輸入下列項目：

- **Username** \(使用者名稱\) - 為了向 ACR 登錄進行驗證而建立之服務主體的 *appId*。
- **Password** \(密碼\) - 為了向 ACR 登錄進行驗證而建立之服務主體的「密碼」**。
- **ID** \(識別碼\) - 認證識別碼，例如 *acr-credentials*

完成時，認證表單看起來會像以下範例：

![使用服務主體資訊來建立 Jenkins 認證物件](media/jenkins-continuous-deployment/acr-credentials.png)

按一下 [OK] \(確定\)****，然後返回 Jenkins 入口網站。

## <a name="create-a-jenkins-project"></a>建立 Jenkins 專案

從 Jenkins 入口網站首頁的左側，選取 [New item] \(新增項目\)****：

1. 輸入 *azure-vote* 作為作業名稱。 選擇 [Freestyle project] \(自由樣式專案\)****，然後選取 [OK] \(確定\)****
1. 在 **"常規**"部分下，選擇**GitHub 專案**並輸入分叉存儲庫 URL，例如*\/HTTPs：/github.com/\<您的 github 帳戶\>/azure 投票-應用-redis*
1. 在**原始程式碼管理**部分下，選擇**Git**，輸入分叉存儲庫 *.git* URL，如*HTTPs：\/\</github.com/您的 github\>帳戶 /azure 投票-app-redis.git*

1. 在 [Build Triggers] \(建置觸發程序\)**** 底下，選取 [GitHub hook trigger for GITscm polling] \(GITScm 輪詢的 GitHub 勾點觸發程序\)****
1. 在 [Build Environment] \(建置環境\)**** 底下，選取 [Use secret texts or files] \(使用祕密文字或檔案\)****
1. 在 [Bindings] \(繫結\)**** 底下，選取 [Add] \(新增\)**** > [Username and password (separated)] \(使用者名稱和密碼 (分隔)\)****
   - 針對 [Username Variable] \(使用者名稱變數\)**** 輸入 `ACR_ID`，並針對 [Password Variable] \(密碼變數\)**** 輸入 `ACR_PASSWORD`

     ![Jenkins 繫結](media/jenkins-continuous-deployment/bindings.png)

1. 選擇新增一個 [Execute shell] \(執行殼層\)**** 類型的 [Build Step] \(建置步驟\)****，然後使用下列文字。 此指令碼會建置新的容器映像，然後將其推送至 ACR 登錄。

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. 新增另一個 [Execute shell] \(執行殼層\)**** 類型的 [Build Step] \(建置步驟\)****，然後使用下列文字。 此指令碼會以來自 ACR 的新容器映像更新 AKS 中的應用程式部署。

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. 完成後，按一下 [Save] \(儲存\)****。

## <a name="test-the-jenkins-build"></a>測試 Jenkins 組建

在您根據 GitHub 認可將作業自動化之前，請先手動測試 Jenkins 組建。 這個手動組建會驗證是否已正確設定作業、已備妥適當的 Kubernetes 驗證檔案，以及是否可向 ACR 進行驗證。

在專案的左側功能表上，選取 [Build Now] \(立即建置\)****。

![Jenkins 測試組建](media/jenkins-continuous-deployment/test-build.png)

第一個組建會在將 Docker 映像層向下提取至 Jenkins 伺服器時花費一兩分鐘的時間。 後續組建可以使用快取映像層來縮短建置時間。

在進行建置程序的期間，會將 GitHub 存放庫複製到 Jenkins 建置伺服器。 系統會建立一個新的容器映像並推送至 ACR 登錄。 最後，在 AKS 叢集上執行的 Azure 投票應用程式會更新成使用新的映像。 由於並沒有對應用程式程式碼進行任何變更，因此當您在網頁瀏覽器中檢視範例應用程式時，應用程式並未變更。

組建作業完成之後，按一下建置歷程記錄底下的 [build #1]****。 選取 [Console Output] \(主控台輸出\)****，然後檢視建置程序的輸出。 最後一行應該會指出組建成功。

## <a name="create-a-github-webhook"></a>建立 GitHub Webhook

順利完成手動組建之後，現在請將 GitHub 整合至 Jenkins 組建。 Webhook 可用來在每次於 GitHub 中進行程式碼認可時，執行 Jenkins 組建作業。 若要建立 GitHub Webhook，請完成下列步驟：

1. 在網頁瀏覽器中瀏覽至分岔的 GitHub 存放庫。
1. 選取 [設定]****，然後選取左側的 [Webhook]****。
1. 選擇 [新增 Webhook]****。 針對 [Payload URL] \(承載 URL\)**，輸入 `http://<publicIp:8080>/github-webhook/`，其中 `<publicIp>` 是 Jenkins 伺服器的 IP 位址。 請務必包含尾端的斜線 (/)。 保留內容類型的其他預設值，並針對「推送」** 事件觸發。
1. 選擇 **"添加網路鉤子**"。

    ![為 Jenkins 建立 GitHub Webhook](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>測試完整的 CI/CD 管線

現在，您可以測試整個 CI/CD 管線。 當您向 GitHub 推送程式碼認可時，會發生下列步驟：

1. GitHub Webhook 向外連至 Jenkins。
1. Jenkins 啟動組建作業，並從 GitHub 提取最新的程式碼認可。
1. 使用已更新的程式碼來啟動 Docker 組建，並為新的容器映像標記最新的組建編號。
1. 將這個新的容器映像推送至 Azure Container Registry。
1. 部署至 Azure Kubernetes Service 的應用程式會以來自 Azure Container Registry 登錄的最新容器映像進行更新。

在您的開發電腦上，使用程式碼編輯器來開啟所複製的應用程式。 在 */azure-vote/azure-vote* 目錄底下，開啟名為 **config_file.cfg** 的檔案。 請將此檔案中的 vote 值更新成 cats 和 dogs 以外的值，如以下範例所示：

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

更新後，請儲存檔案、認可變更，然後將這些變更推送至您的 GitHub 存放庫分岔。 GitHub Webhook 會在 Jenkins 中觸發新的組建作業。 請在 Jenkins Web 儀表板中監視建置程序。 提取最新的程式碼、建立和推送已更新的映像，然後在 AKS 中部署已更新的應用程式，需要幾秒的時間。

組建完成之後，請重新整理範例 Azure 投票應用程式的網頁瀏覽器。 隨即會顯示您的變更，如以下範例所示：

![Jenkins 組建作業所更新的 AKS 中範例 Azure 投票](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure 上的 Jenkins](/azure/jenkins)