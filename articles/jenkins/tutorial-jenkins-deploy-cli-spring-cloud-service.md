---
title: 使用 Jenkins 和 Azure CLI 將應用程式部署至 Azure Spring Cloud
description: 了解如何在持續整合及部署管線中使用 Azure CLI 將微服務部署至 Azure Spring Cloud 服務
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75732851"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>教學課程：使用 Jenkins 和 Azure CLI 將應用程式部署至 Azure Spring Cloud

[Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) 是完全受控的微服務開發，並內建服務探索和組態管理。 此服務可讓您輕鬆地將以 Spring Boot 為基礎的微服務應用程式部署到 Azure。 本教學課程會示範如何在 Jenkins 中使用 Azure CLI，將 Azure Spring Cloud 的持續整合與傳遞 (CI/CD) 自動化。

在本教學課程中，您將完成下列工作：

> [!div class="checklist"]
> * 佈建服務執行個體並啟動 Java Spring 應用程式
> * 準備 Jenkins 伺服器
> * 在 Jenkins 管線中使用 Azure CLI 來建置和部署微服務應用程式 

此教學課程假設您有核心 Azure 服務、Azure Spring Cloud、Jenkins [管線](https://jenkins.io/doc/book/pipeline/)與外掛程式和 GitHub 的中級程度知識。

## <a name="prerequisites"></a>Prerequisites

>[!Note]
> Azure Spring Cloud 目前提供公開預覽版。 公開預覽版供應項目可讓客戶在其正式發行前，先試驗新功能。  公開預覽功能和服務不適用於生產環境。  如需有關預覽期間支援的詳細資訊，請參閱我們的[常見問題集](https://azure.microsoft.com/support/faq/)或提出[支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)以深入了解。

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* GitHub 帳戶。 如果您沒有 GitHub 帳戶，請在開始之前[建立免費帳戶](https://github.com/) \(英文\)。

* Jenkins 主要伺服器。 如果您還沒有 Jenkins Master，請遵循此 [快速入門](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)步驟在 Azure 上部署 [Jenkins](https://aka.ms/jenkins-on-azure) 。 下列是 Jenkins 節點/代理程式 (例如組建伺服器) 上的必要項目 ：

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 或更新版本](https://maven.apache.org/download.cgi)
    * [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) (2.0.67 版或更新版本)

    >[!TIP]
    > 依預設，Azure Marketplace [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) 解決方案範本中會包含 Git、JDK、Az CLI 和 Azure 外掛程式等工具。
    
* [註冊 Azure 訂用帳戶](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>佈建服務執行個體並啟動 Java Spring 應用程式

我們會使用 [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) 作為 Microsoft 服務應用程式範例，並遵循以下文章中的步驟：[快速入門：使用 Azure CLI 來啟動 Java Spring 應用程式](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)，以佈建服務執行個體和設定應用程式。 如果您已經進行過相同的程序，您可以跳到下一節。 沒有的話，以下是 Azure CLI 命令。 請參閱[快速入門：使用 Azure CLI 來啟動 Java Spring 應用程式](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)，以取得其他背景資訊。

您的本機電腦必須符合與 Jenkins 組建伺服器相同的必要條件。 務必安裝下列各項，才能建置及部署微服務應用程式：
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 或更新版本](https://maven.apache.org/download.cgi)
    * [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) (2.0.67 版或更新版本)

1. 安裝 Azure Spring Cloud 擴充功能：

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. 建立資源群組來包含您的 Azure Spring Cloud 服務：

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. 佈建 Azure Spring Cloud 的執行個體：

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. 在您自己的 GitHub 帳戶中派生 [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) 存放庫。 在您的本機電腦中，將您的存放庫複製到名為 `source-code` 的目錄中：

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. 設定您的設定伺服器。 請務必以正確的值取代 &lt;您的 GitHub 識別碼&gt;。

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. 建置專案：

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. 建立三個微服務：**gateway**、**auth-service** 及 **account-service**：

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. 部署應用程式： 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. 將公用端點指派到閘道：

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. 請查詢閘道應用程式來取得 URL，以便確認該應用程式正在執行。

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    瀏覽至由上一個命令所提供的 URL，以執行 PiggyMetrics 應用程式。 

## <a name="prepare-jenkins-server"></a>準備 Jenkins 伺服器

在本節中，您會準備 Jenkins 伺服器來執行適合測試的組建。 不過，因為安全性考量，您應該使用 [Azure VM 代理程式](https://plugins.jenkins.io/azure-vm-agents)或 [Azure 容器代理程式](https://plugins.jenkins.io/azure-container-agents)，加速 Azure 中的代理程式執行您的組建。 如需詳細資訊，請參閱有關[在主要節點上建置的安全性含意](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master)的 Jenkins 文章。

### <a name="install-plug-ins"></a>安裝外掛程式

1. 登入您的 Jenkins 伺服器。 選擇 [管理 Jenkins] > [管理外掛程式]  。
2. 在 [可用的]  索引標籤上，選取下列外掛程式：
    * [GitHub 整合](https://plugins.jenkins.io/github-pullrequest) \(英文\)
    * [Azure 認證](https://plugins.jenkins.io/azure-credentials)

    如果這些外掛程式未出現在清單中，請查看 [已安裝]  索引標籤，確認它們是否已安裝。

3. 若要安裝您選取的外掛程式，請選擇 [立即下載並於重新啟動後安裝]  。

4. 重新啟動您的 Jenkins 伺服器以完成安裝。

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>在 Jenkins 認證存放區中新增您的 Azure 服務主體認證

1. 您必須要有 Azure 服務主體，才能部署到 Azure。 如需詳細資訊，請參閱「部署到 Azure App Service」教學課程中的 [建立服務主體](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) 一節。 `az ad sp create-for-rbac` 的輸出會看起來類似這樣：

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. 在 Jenkins 儀表板上，選取 [Credentials] \(認證\)   > [System] \(系統\)  。 然後，選取 [Global credentials(unrestricted)] \(全域認證 (不受限)\)  。

3. 選取 [新增認證]  。 

4. 選取 [Microsoft Azure 服務主體]  作為類型。

5. 提供以下項目的值：* 訂用帳戶識別碼：使用您的 Azure 訂用帳戶識別碼 * 用戶端識別碼：使用 `appId` * 用戶端密碼：使用 `password` * 租用戶識別碼：使用 `tenant` * Azure 環境：選取預先設定的值。 例如，針對 Azure Global 使用 **Azure** * ID：設定為 **azure_service_principal**。 我們會在本文稍後的步驟中使用此識別碼 * 描述：這是選擇性欄位。 我們建議您在這裡提供有意義的值。

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>安裝 Maven 和 Az CLI spring-cloud 擴充功能

管線範例會使用 Maven 來建置，並使用 Az CLI 來部署至服務執行個體。 安裝 Jenkins 時，它會建立名為 *jenkins* 的系統管理員帳戶。 請確定使用者 jenkins  有執行 spring-cloud 擴充功能的權限。

1. 透過 SSH 連線到 Jenkins 主機。 

2. 安裝 Maven

    ```bash
        sudo apt-get install maven 
    ```

3. 安裝 Azure CLI。 如需詳細資訊，請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 根據預設，如果您使用 [Azure 上的 Jenkins Master](https://aka.ms/jenkins-on-azure)，系統會安裝 Azure CLI。

4. 切換至 `jenkins` 使用者：

    ```bash
        sudo su jenkins
    ```

5. 新增 **spring-cloud** 擴充功能：

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>建立 Jenkinsfile
1. 在您存放庫 (https://github.com/&lt ;您的 GitHub 識別碼&gt; /piggymetrics) 的根目錄中建立 **Jenkinsfile**。

2. 請更新檔案為下列內容。 請務必取代 **\<資源群組名稱>** 和 **\<服務名稱>** 的值。 如果您在 Jenkins 中新增認證時使用不同的值，請將 **azure_service_principal** 取代為正確的識別碼。 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. 儲存並認可變更。

## <a name="create-the-job"></a>建立工作

1. 在 Jenkins 儀表板中，按一下 [新增項目]  。

2. 為作業提供一個名稱 (Deploy-PiggyMetrics  )，然後選取 [管線]  。 按一下 [確定]。

3. 接著，按一下 [Pipeline] \(管線\)  索引標籤。

4. 針對 [Definition] \(定義\)  ，選取 [Pipeline script from SCM] \(來自 SCM 的管線指令碼\)  。

5. 針對 [SCM]  ，選取 [Git]  。

6. 輸入您分支存放庫的 GitHub URL： **https://github.com/&lt ;您的 GitHub 識別碼&gt; /piggymetrics.git**

7. 請確定 [分支指定名稱 (空白則表示「任何一個」)]  是 * **/Azure**

8. 將 [指令碼路徑]  保留為 **Jenkinsfile**

7. 按一下 [儲存] 

## <a name="validate-and-run-the-job"></a>驗證並執行作業

在執行作業之前，讓我們先將 [登入] 輸入方塊中的文字更新為**輸入登入識別碼**。

1. 在您自己的存放庫中，從 **/gateway/src/main/resources/static/** 之中開啟 `index.html`

2. 搜尋「輸入您的登入」並更新為「輸入登入識別碼」

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. 認可變更

4. 以手動方式在 Jenkins 中執行作業。 在 Jenkins 儀表板上，按一下 Deploy-PiggyMetrics  作業，然後按一下 [立即建立]  。

作業完成後，瀏覽至**閘道**應用程式的公用 IP，並確認您的應用程式已更新。 

![已更新的 Piggy Metrics](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除：

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在 Jenkins 中使用 Azure CLI，將 Azure Spring Cloud 的持續整合與傳遞 (CI/CD) 自動化。

若要深入了解 Azure Jenkins 提供者，請參閱 Azure 網站上的 Jenkins。

> [!div class="nextstepaction"]
> [Azure 上的 Jenkins](/azure/jenkins/)
