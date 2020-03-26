---
title: 教學課程：使用 Jenkins 從 GitHub 部署至 Azure App Service
description: 設定 Jenkins 以進行 Java Web 應用程式從 GitHub 的持續整合 (CI) 以及對 Azure App Service 的持續部署 (CD)
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9fcf178b71ac1f07bfb58cd2502701ae5392b472
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74158397"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>教學課程：使用 Jenkins 持續整合和部署從 GitHub 部署至 Azure App Service

本教學課程將以 Jenkins 設定持續整合 (CI) 和持續部署 (CD)，將範例 Java Web 應用程式從 GitHub 部署至 [Linux 上的 Azure App Service](/azure/app-service/containers/app-service-linux-intro)。 當您將認可推送至 GitHub 以更新應用程式時，Jenkins 會自動建置應用程式並將其重新發佈至 Azure App Service。 本教學課程中的範例應用程式是使用 [Spring Boot](https://projects.spring.io/spring-boot/) 架構開發的。 

![從 GitHub 到 Azure App Service 的部署概觀](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

在本教學課程中，您將完成下列工作：

> [!div class="checklist"]
> * 安裝 Jenkins 外掛程式，以便從 GitHub 進行建置、部署至 Azure App Service，和執行其他相關工作。
> * 派生範例 GitHub 存放庫，以產生您的工作複本。
> * 將 Jenkins 連線至 GitHub。
> * 建立 Azure 服務主體，使 Jenkins 可直接存取 Azure 而無須使用您的認證。
> * 將您的服務主體新增至 Jenkins。
> * 建立 Jenkins 管線，以在您每次更新 GitHub 中的應用程式時建置和部署範例應用程式。
> * 為您的 Jenkins 管線建立組建和部署檔案。
> * 在組建和部署指令碼上指定您的 Jenkins 管線。
> * 藉由執行手動組建將範例應用程式部署至 Azure。
> * 在 GitHub 中推送應用程式更新，這會觸發 Jenkins 對 Azure 的建置和重新部署。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列項目：

* 已在 Azure Linux VM 上安裝 Java Development Kit (JDK) 和 Maven 工具的 [Jenkins](https://jenkins.io/) 伺服器

  如果您沒有 Jenkins 伺服器，請立即在 Azure 入口網站中完成下列步驟：[在 Azure Linux VM 上建立 Jenkins 伺服器](/azure/jenkins/install-jenkins-solution-template)

* 一個 [GitHub](https://github.com) 帳戶，以便您取得範例 Java Web 應用程式的工作複本 (分支)。 

* [Azure CLI](/cli/azure/install-azure-cli) (可從您的本機命令列或 [Azure Cloud Shell](/azure/cloud-shell/overview) 執行)

## <a name="install-jenkins-plug-ins"></a>安裝 Jenkins 外掛程式

1. 在下列位置登入您的 Jenkins Web 主控台：

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. 在 Jenkins 的主頁面上，選取 [管理 Jenkins]   > [管理外掛程式]  。

   ![管理 Jenkins 外掛程式](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. 在 [可用的]  索引標籤上，選取下列外掛程式：

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub 分支來源](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [環境載入程式外掛程式](https://plugins.jenkins.io/envinject)
   - [Azure 認證](https://plugins.jenkins.io/azure-credentials)

   若未出現這些外掛程式，請檢查 [已安裝]  索引標籤以確定它們尚未安裝。

1. 若要安裝您選取的外掛程式，請選取 [立即下載並於重新啟動後安裝]  。

1. 作業完成後，請在 Jenkins 功能表上選取 [管理 Jenkins]  返回 Jenkins 管理頁面，以進行後續步驟。

## <a name="fork-sample-github-repo"></a>派生範例 Github 存放庫

1. [登入 Spring Boot 範例應用程式的 GitHub 存放庫](https://github.com/spring-guides/gs-spring-boot)。 

1. 在 GitHub 的右上角選取 [派生]  。

   ![從 GitHub 派生範例存放庫](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. 依照提示選取您的 GitHub 帳戶，並完成派生。

然後，使用您的 GitHub 認證設定 Jenkins。

## <a name="connect-jenkins-to-github"></a>將 Jenkins 連線至 GitHub

若要讓 Jenkins 監視 GitHub，並在新認可推送至您 GitHub 分支中的 Web 應用程式時予以回應，請在 Jenkins 中啟用 [GitHub Webhook](https://developer.github.com/webhooks/)。

> [!NOTE]
> 
> 這些步驟會使用您的 GitHub 使用者名稱和密碼，建立讓 Jenkins 與 GitHub 搭配運作的個人存取權杖認證。 
> 不過，如果您的 GitHub 帳戶使用雙因素驗證，請在 GitHub 中建立權仗，並將 Jenkins 設定為使用該權仗。 
> 如需詳細資料，請參閱 [Jenkins GitHub 外掛程式](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin)文件。

1. 在 [管理 Jenkins]  頁面中，選取 [設定系統]  。 

   ![在 Jenkins 中設定系統](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. 在 [GitHub]  區段中，提供您 GitHub 伺服器的詳細資料。 在 [新增 GitHub 伺服器]  清單中，選取 [GitHub 伺服器]  。 

   ![在 Jenkins 中新增 GitHub 伺服器](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. 若未選取 [管理勾點]  屬性，請選取此屬性。 選取 [進階]  以便指定其他設定。 

   ![指定 GitHub 伺服器的進階 Jenkins 設定](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. 從 [管理其他 GitHub 動作]  清單中，選取 [將登入和密碼轉換成權杖]  。

   ![將登入和密碼轉換為 GitHub 的權杖](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. 選取 [從登入和密碼]  ，以便您輸入 GitHub 使用者名稱和密碼。 作業完成後，請選取 [建立權杖認證]  ，以建立 [GitHub 個人存取權杖 (PAT)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。   

   ![從登入和密碼建立 GitHub PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. 從 [GitHub 伺服器]  區段的 [認證]  清單中，選取您的新權杖。 藉由選擇 [測試連線]  來檢查驗證是否正常運作。

   ![使用新的 PAT 檢查 GitHub 伺服器的連線](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

接著，建立 Jenkins 用來驗證和存取 Azure 資源的 Azure 服務主體。

## <a name="create-service-principal"></a>建立服務主體

在後續章節中，您將建立 Jenkins 管線作業，用以從 GitHub 建置您的應用程式，並將其部署至 Azure App Service。 若要讓 Jenkins 無須輸入您的認證即可存取 Azure，請在 Azure Active Directory 中建立 Jenkins 的[服務主體](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。 服務主體是 Jenkins 可用來驗證 Azure 資源存取權的個別身分識別。 若要建立服務主體，請從您的本機命令列或 Azure Cloud Shell 執行 Azure CLI 命令 [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)，例如： 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

請確實以引號括住服務主體名稱。 此外，請根據 [Azure Active Directory 密碼規則和限制](/azure/active-directory/active-directory-passwords-policy)建立強式密碼。 若未提供密碼，Azure CLI 將為您建立密碼。 

以下是 **`create-for-rbac`** 命令所產生的輸出： 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> 如果您已有服務主體，您可以改為重複使用該身分識別。
> 提供驗證的服務主體值時，請使用 `appId`、`password` 和 `tenant` 屬性值。 
> 搜尋現有的服務主體時，請使用 `displayName` 屬性值。

## <a name="add-service-principal-to-jenkins"></a>將服務主體新增至 Jenkins

1. 在 Jenkins 的主頁面上，選取 [認證]   > [系統]  。 

1. 在 [系統]  頁面的 [網域]  下方，選取 [全域認證 (不受限)]  。

1. 從左側功能表中，選取 [新增認證]  。

1. 從 [種類]  清單中，選取 [Azure 服務主體]  。

1. 在此步驟中，於下表說明的屬性中提供您服務主體與 Azure 訂用帳戶的資訊：

   ![新增 Azure 服務主體認證](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | 屬性 | 值 | 描述 | 
   |----------|-------|-------------| 
   | **訂用帳戶識別碼** | <*yourAzureSubscription-ID*> | 您 Azure 訂用帳戶的 GUID 值 <p>**提示**：如果您不知道您的 Azure 訂用帳戶 ID，請從命令列或在 Cloud Shell 中執行下列 Azure CLI 命令，然後使用 `id` GUID 值： <p>`az account list` | 
   | **用戶端識別碼** | <*yourAzureServicePrincipal-ID*> | 先前為您的 Azure 服務主體產生的 `appId` GUID 值 | 
   | **用戶端祕密** | <*yourSecurePassword*> | 您為 Azure 服務主體提供的 `password` 值或「祕密」 | 
   | **租用戶識別碼** | <*yourAzureActiveDirectoryTenant-ID*> | 您 Azure Active Directory 租用戶的 `tenant` GUID 值 | 
   | **識別碼** | <*yourAzureServicePrincipalName*> | 您 Azure 服務主體的 `displayName` 值 | 

1. 若要確認您的服務主體可運作，請選取 [驗證服務主體]  。 完成後，選取 [確定]  。

接著，建立用來建置和部署應用程式的 Jenkins 管線。

## <a name="create-jenkins-pipeline"></a>建立 Jenkins 管線

在 Jenkins 中，建立用來建置和部署應用程式的管線作業。

1. 返回 Jenkins 首頁，並選取 [新增項目]  。 

   ![建立 Jenkins 管線](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. 提供管線作業的名稱 (例如 "My-Java-Web-App")，然後選取 [管線]  。 選取底部的 [確定]  。  

   ![命名 Jenkins 管線作業](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. 以您的服務主體設定 Jenkins，使 Jenkins 可使用您自己的認證部署至 Azure。

   1. 在 [一般]  索引標籤上，選取 [準備執行環境]  。 

   1. 在顯示的 [屬性內容]  方塊中，新增這些環境變數及其值。 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![準備執行環境，並設定環境變數](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. 完成時，選取 [儲存]  。

接著，建立 Jenkins 的組建和部署指令碼。

## <a name="create-build-and-deployment-files"></a>建立組建和部署檔案

現在，請建立 Jenkins 用來建置和部署應用程式的檔案。

1. 在 GitHub 分支的 `src/main/resources/` 資料夾中，建立這個名為 `web.config` 的應用程式組態檔，其中包含下列 XML (但請將 `$(JAR_FILE_NAME)` 取代為 `gs-spring-boot-0.1.0.jar`)：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. 在 GitHub 分支的根資料夾中，建立這個名為 `Jenkinsfile` 的組建和部署指令碼，其中包含下列文字 ([在此為 GitHub 中的來源](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se))：

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. 將 `web.config` 和 `Jenkinsfile` 檔案認可到您的 GitHub 分支，並推送您的變更。

## <a name="point-pipeline-at-script"></a>在指令碼上指定管線

現在，請指定您要讓 Jenkins 使用的組建和部署指令碼。

1. 在 Jenkins 中，選取您先前建立的管線作業。 

   ![選取 Web 應用程式的 Jenkins 管線作業](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. 在左側功能表上，選取 [設定]  。

1. 在 [管線]  索引標籤上，從 [定義]  清單中選取 [來自 SCM 的管線指令碼]  。

   1. 在顯示的 [SCM]  方塊中，選取 [Git]  作為您的原始檔控制。 

   1. 在 [存放庫]  區段中，針對 [存放庫 URL]  輸入您 GitHub 分支的 URL，例如： 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. 針對 [認證]  ，選取您先前建立的 GitHub 個人存取權杖。

   1. 在 [指令碼路徑]  方塊中，新增 "Jenkinsfile" 指令碼的路徑。

   完成作業後，您管線定義會如下列範例所示︰ 

   ![在指令碼上指向 Jenkins 管線](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. 完成時，選取 [儲存]  。

接著，建置您的應用程式並將其部署至 Azure App Service。 

## <a name="build-and-deploy-to-azure"></a>建置並部署至 Azure

1. 從命令列或 Azure Cloud Shell 使用 Azure CLI，建立 [Linux 上的 Azure App Service Web 應用程式](/azure/app-service/containers/app-service-linux-intro) (Jenkins 會在完成建置後部署您的 Web 應用程式)。 請確定您的 Web 應用程式具有唯一名稱。

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   如需這些 Azure CLI 命令的詳細資訊，請參閱下列頁面：

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. 在 Jenkins 中選取您的管線作業，然後選取 [立即建置]  。

   建置完成後，Jenkins 會部署您的應用程式 (此時存在於 Azure 上的發行集 URL 中)，例如： 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![在 Azure 上檢視已部署的應用程式](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>推送變更並重新部署

1. 在網頁瀏覽器中，移至您 Web 應用程式的 GitHub 分支中的下列位置：

   `complete/src/main/java/Hello/Application.java`
   
1. 從 GitHub 的右上角選取 [編輯此檔案]  。

1. 對 `commandLineRunner()` 方法進行下列變更，並將變更認可至存放庫的 `master` 分支。 `master` 分支中的這個認可會在 Jenkins 中起始建置。 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. 建置完成，且 Jenkins 重新部署至 Azure 後，請重新整理您的應用程式，此時您會看到您的更新。

   ![在 Azure 上檢視已部署的應用程式](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>對 Jenkins 外掛程式進行疑難排解

如果您遇到任何有關 Jenkins 外掛程式的錯誤，請在 [Jenkins JIRA](https://issues.jenkins-ci.org/) 的特定元件中提交問題。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure VM 做為組建代理程式](/azure/jenkins/jenkins-azure-vm-agents)
