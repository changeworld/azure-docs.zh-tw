---
title: 搭配使用適用於 Jenkins 的 Azure Dev Spaces 外掛程式和 Azure Kubenetes Service
description: 了解如何在持續整合管線中使用 Azure Dev Spaces 外掛程式。
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 281565cec5ee947781ab8ee9f62a00e01f9ababb
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037030"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>教學課程：搭配使用適用於 Jenkins 的 Azure Dev Spaces 外掛程式和 Azure Kubenetes Service 

Azure Dev Spaces 可讓您測試並反覆開發在 Azure Kubernetes Service (AKS) 中執行的微服務應用程式，無需複製或模擬相依性。 適用於 Jenkins 的 Azure Dev Spaces 外掛程式可協助您在持續整合和持續傳遞 (CI/CD) 管線中使用 Dev Spaces。

此教學課程也使用 Azure Container Registry (ACR)。 ACR 會儲存影像、ACR 工作組建 Docker 和 Helm 成品。 將 ACR 和 ACR 工作用於產生成品，便不再需要在您的 Jenkins 伺服器上安裝其他軟體 (例如，Docker)。 

在本教學課程中，您將完成下列工作：

> [!div class="checklist"]
> * 建立啟用 AKS 叢集的 Azure Dev Spaces
> * 將多服務應用程式部署至 AKS
> * 準備 Jenkins 伺服器
> * 在 Jenkins 管線中使用 Azure Dev Spaces 外掛程式，以在將程式碼合併至專案中之前檢視它們

此教學課程假設您有核心 Azure 服務、AKS、ACR、Azure Dev Spaces、Jenkins [管線](https://jenkins.io/doc/book/pipeline/)與外掛程式和 GitHub 的中級程度知識。 對 kubectl 和 Helm 等支援工具的基本程度熟悉很有幫助。

## <a name="prerequisites"></a>Prerequisites

* 一個 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

* GitHub 帳戶。 如果您沒有 GitHub 帳戶，請在開始之前[建立免費帳戶](https://github.com/) \(英文\)。

* 已安裝 [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) \(英文\) 擴充功能的 [Visual Studio Code](https://code.visualstudio.com/download) \(英文\)。

* 已安裝 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) (2.0.43 版或更新版本)。

* Jenkins 主要伺服器。 如果您還沒有 Jenkins Master，請遵循此 [快速入門](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)步驟在 Azure 上部署 [Jenkins](https://aka.ms/jenkins-on-azure) 。 

* Jenkins 伺服器必須已安裝 Helm 和 kubectl 安裝且可供 Jenkins 帳戶取得，如稍後在此教學課程中所述。

* VS Code、VS Code Terminal 或 WSL 和 Bash。 


## <a name="create-azure-resources"></a>建立 Azure 資源

在此節中，您會建立 Azure 資源：

* 建立資源群組，以包含此教學課程的所有 Azure 資源。
* [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) \(部分機器翻譯\) (AKS) 叢集。
* 用於建置 (使用 ACR 工作) 和儲存 Docker 映像的 [Azure 容器登錄庫](https://docs.microsoft.com/azure/container-registry/) \(部分機器翻譯\) (ACR)。

1. 建立資源群組。

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. 建立 AKS 叢集。 在[支援 Dev Spaces 的區域](../dev-spaces/about.md#supported-regions-and-configurations) \(部分機器翻譯\) 建立 AKS 叢集。

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. 設定 AKS 使用 Dev Spaces。

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    此步驟會安裝 `azds` CLI 擴充功能。

4. 建立容器登錄庫。

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>將範例應用程式部署至 AKS 叢集

在此節中，您會設定開發空間，並將範例應用程式部署到您在上一節中建立的 AKS 叢集。 應用程式由兩部分所組成 *webfrontend* 和 *mywebapi*。 兩個元件都部署在開發空間中。 稍後在此教學課程中，您將針對 mywebapi 提交提取要求，以觸發 Jenkins 中的 CI 管線。

如需搭配使用 Azure Dev Spaces 和多服務開發與 Azure Dev Spaces 的詳細資訊，請參閱[在使用 Java 的 Azure Dev Spaces 上開始使用](https://docs.microsoft.com/azure/dev-spaces/get-started-java)和[使用 Azure Dev Spaces 進行多服務開發](https://docs.microsoft.com/azure/dev-spaces/multi-service-java)。 這些教學課程提供此文章並未提及的額外背景資訊。

1. 從 GitHub 下載 https://github.com/Azure/dev-spaces 存放庫。

2. 在 VS Code 中開啟 `samples/java/getting-started/webfrontend` 資料夾。 (您可以略過要新增偵錯資產或還原專案的任何預設提示。)

3. 將 `/src/main/java/com/ms/sample/webfrontend/Application.java` 更新為看起來如下所示︰

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. 依序按一下 [檢視]  、[終端機]  ，以在 VS Code 中開啟整合式終端機。

5. 執行 `azds prep` 命令，備妥您的應用程以在開發空間中執行。 此命令必須從 `dev-spaces/samples/java/getting-started/webfrontend` 執行，以正確地準備您的應用程式：

    ```bash
    azds prep --public
    ```

    Dev Spaces CLI 的 `azds prep` 命令會產生具有預設設定的 Docker 和 Kubernetes 資產。 在專案存留期期間，系統會保存這些檔案，且您可自訂它們：

    * `./Dockerfile` 和 `./Dockerfile.develop` 描述應用程式的容器映像，以及如何建置原始程式碼和在容器內執行。
    * `./charts/webfrontend` 底下的 [Helm 圖表](https://helm.sh/docs/topics/charts/)會說明如何將容器部署至 Kubernetes。
    * `./azds.yaml` 是 Azure Dev Spaces 組態檔。

    如需詳細資訊，請參閱 [Azure Dev Spaces 的運作和設定方式](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works) \(部分機器翻譯\)。

6. 使用 `azds up` 命令在 AKS 中建置和執行應用程式：

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>掃描主控台輸出，以取得 `up` 命令所建立之 URL 的相關資訊。 它會在表單中：

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    在瀏覽器視窗中開啟此 URL，您應該會看到 Web 應用程式。 當容器執行時，`stdout` 和 `stderr` 輸出會串流至終端機視窗。

8. 接下來，設定和部署 *mywebapi*：

    1. 變更目錄為 `dev-spaces/samples/java/getting-started/mywebapi`

    2. 執行

        ```bash
        azds prep
        ```

    3. 執行

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>準備 Jenkins 伺服器

在此節中，您會準備 Jenkins 伺服器，以執行範例 CI 管線。

* 安裝外掛程式
* 安裝 Helm 和 Kubernetes CLI
* 新增認證

### <a name="install-plug-ins"></a>安裝外掛程式

1. 登入您的 Jenkins 伺服器。 選擇 [管理 Jenkins] > [管理外掛程式]  。
2. 在 [可用的]  索引標籤上，選取下列外掛程式：
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces) \(英文\)
    * [Azure Container Registry 工作](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [環境載入程式](https://plugins.jenkins.io/envinject) \(英文\)
    * [GitHub 整合](https://plugins.jenkins.io/github-pullrequest) \(英文\)

    如果這些外掛程式未出現在清單中，請查看 [已安裝]  索引標籤，確認它們是否已安裝。

3. 若要安裝您選取的外掛程式，請選擇 [立即下載並於重新啟動後安裝]  。

4. 重新啟動您的 Jenkins 伺服器以完成安裝。

### <a name="install-helm-and-kubectl"></a>安裝 Helm 和 kubectl

範例管線使用 Helm 和 kubectl 部署至開發空間。 安裝 Jenkins 時，它會建立名為 *jenkins* 的系統管理員帳戶。 Helm 和 kubectl 必須可供 jenkins 使用者存取。

1. 建立對 Jenkins Master 的 SSH 連線。 

2. 切換至 `jenkins` 使用者：
    ```bash
    sudo su jenkins
    ```

3. 安裝 Helm CLI。 如需詳細資訊，請參閱[安裝 Helm](https://helm.sh/docs/using_helm/#installing-helm) \(英文\)。

4. 安裝 kubectl。 如需詳細資訊，請參閱 [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm) \(英文\)。

### <a name="add-credentials-to-jenkins"></a>將認證新增至 Jenkins

1. Jenkins 需要 Azure 服務主體來驗證和存取 Azure 資源。 若要建立服務主體，請參閱部署到 Azure App Service 教學課程中的 [建立服務主體](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) 一節。 請務必儲存一份 `create-for-rbac` 的輸出，因為您需要該資訊來完成下一個步驟。 輸出會看起來類似這樣：

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. 使用來自上一步的 Azure 服務主體，在 Jenkins 中新增「Microsoft Azure 服務主體」  認證類型。 以下螢幕擷取畫面中的名稱與 `create-for-rbac` 的輸出對應。

    Jenkins 中的 [ID]  欄位是您服務主體的認證名稱。 此範例會使用 `displayName` 值 (在此案例中為 `xxxxxxxjenkinssp`)，但您可以使用任何您想要的文字。 此認證名稱是下一節中 AZURE_CRED_ID 環境變數的值。

    ![新增 Jenkins 服務主體認證](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    [描述]  是選擇性的。 如需詳細指示，請參閱部署至 Azure App Service 教學課程中的[將服務主體新增至 Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins)  一節。 



3. 若要顯示您的 ACR 認證，請執行此命令：

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    建立 JSON 輸出的副本，應該看起來像這樣：

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. 在 Jenkins 中新增「使用者名稱與密碼」  認證類型。 **username** 是從最後一個步驟中的使用者名稱，在此範例中為 `acr01`。 **password** 是第一個密碼的值，在此範例中是 `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`。 此認證的 **ID** 是 ACR_CRED_ID 的值。

5. 設定 AKS 認證。 新增 Jenkins 中的「Kubernetes 設定 (kubeconfig)」  認證類型 (使用 [直接輸入] 選項)。 若要取得您 AKS 叢集的存取認證，請執行下列命令：

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   此認證的 **ID** 是下一節中 KUBE_CONFIG_ID 的值。

## <a name="create-a-pipeline"></a>建立管線

為範例管線所選的案例是以實際模式為基礎：提取要求觸發 CI 管線將建議變更建置並部署到 Azure 開發空間，以進行測試和檢閱。 根據檢閱的結果，所做的變更會合併並部署到 AKS 或捨棄。 最後，系統會移除開發空間。

Jenkins 管線設定和 Jenkinsfile 定義 CI 管線中的階段。 此流程圖顯示管線階段和 Jenkinsfile 定義的決策點：

![Jenkins 管線流程](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. 從 [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi) 下載 *mywebapi* 專案的修改版本。 此專案包含建立管線所需的數個檔案，包括 *Jenkinsfile*、*Dockerfile* 和 Helm 圖表。

2. 登入 Jenkins。 從左側功能表選取 [新增項目]  。

3. 選取 [管線]  ，然後在 [輸入項目名稱]  方塊中輸入名稱。 選取 [確定]  ，然後管線設定畫面會自動開啟。

4. 在 [一般]  索引標籤上，選取 [準備執行環境]  。 

5. 選取 [保留 Jenkins 環境變數]  和 [保留 Jenkins 組建變數]  。

6. 在 [內容屬性]  方塊中，輸入下列環境變數：

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    使用上一節中給定的範例值，環境變數清單應該看起來像這樣：

    ![Jenkins 管線環境變數](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. 選擇 [管線] > [定義]  中的 [來自 SCM 的管線指令碼]  。
8. 在 [SCM]  中，選擇 [Git]  ，然後輸入您的存放庫 URL。
9. 在 [分支指定名稱]  中，輸入 `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`。
10. 填入 SCM 存放庫 URL 和指令碼路徑 "Jenkinsfile"。
11. 應選取 [輕量簽出]  。

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>建立提取要求來觸發管線

若要完成此節中的步驟 3，您必須將 Jenkinsfile 的一部分註解化，否則當您嘗試並排檢視新版本和舊版本時會收到 404 錯誤。 根據預設，當您選擇要合併的 PR 時，共用的舊版 mywebapi 會被移除並取代為新版。 完成步驟 1 之前，先對 Jenkinsfile 進行下列變更：

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. 變更 `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java` 然後建立提取要求。 例如：

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. 登入 Jenkins 並選取管線名稱，然後選擇 [立即建置]  。 

    您也可以設定 *webhook*，以自動觸發 Jenkins 管線。 輸入提取要求後，GitHub 會發出 POST 到 Jenkins 進而觸發管線。 如需有關設定 Webhook 的詳細資訊，請參閱[將 Jenkins 連線到 GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github)。

3. 比較目前共用版本的變更：

    1. 開啟瀏覽器並瀏覽至共用版本 `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`。 TEST_ENDPOINT 包含 URL。

    2. 開啟另一個索引標籤，然後輸入 PR 開發空間 URL。 該屬性將類似於 `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`。 您會在 Jenkins 作業的 [建置歷程記錄] > [<組建#>] > [主控台輸出]  中找到連結。 在頁面上搜尋 `aksapp`，或只查看前置詞，搜尋 `azdsprefix`。

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>建構子開發空間的 URL

當您發出提取要求時，Jenkins 會根據小組的公用開發空間建立子開發空間，並在該子開發空間中執行來自提取要求的程式碼。 子開發空間的 URL 格式為 `http://$env.azdsprefix.<test_endpoint>`。 

Azure Dev Spaces 外掛程式會在管線執行期間藉由 **devSpacesCreate** 來設定 **$env.azdsprefix**：

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint` 是您先前在[將範例應用程式部署至 AKS 叢集 (步驟 7)](#test_endpoint) 中使用 `azds up` 設定的 webfrontend 應用程式 URL。 `$env.TEST_ENDPOINT` 的值設定在管線設定中。 

下列程式碼片段顯示子開發空間 URL 如何用於 `smoketest` 階段中。 程式碼會檢查子開發空間 TEST_ENDPOINT 是否可供使用，如果可用，便會將問候語下載到 stdout：

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>清除資源

當您使用完範例應用程式之後，請刪除資源群組以清除 Azure 資源：

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure 上使用 Jenkins 的 CI/CD](jenkins-continuous-deployment.md)
