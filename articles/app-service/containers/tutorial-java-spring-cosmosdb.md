---
title: 教學課程：使用 MongoDB 的 Linux Java 應用程式
description: 了解如何藉由連線至 Azure 中的 MongoDB (Cosmos DB)，讓資料驅動的 Linux Java 應用程式在 Azure App Service 中運作。
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 5109c33acf4a92a3227fe79d6d2c997a54adec08
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425267"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>教學課程：使用 Linux 上的 Azure App Service 和 Azure Cosmos DB 建置 Java Spring Boot Web 應用程式

本教學課程會引導您完成在 Azure 上建置、設定、部署及調整 Java Web 應用程式的程序。 當您完成後，[Azure Cosmos DB](/azure/cosmos-db) (在 [Linux 上的 Azure App Service](/azure/app-service/containers) 中執行) 中會有一個儲存資料的 [Spring Boot](https://projects.spring.io/spring-boot/) 應用程式。

![將資料儲存在 Azure Cosmos DB 中的 Spring Boot 應用程式](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 Cosmos DB 資料庫。
> * 將應用程式範例連線至資料庫，並在本機進行測試
> * 將應用程式範例部署至 Azure
> * 來自 App Service 的串流診斷記錄
> * 新增額外的執行個體來擴充範例應用程式

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* 在您自己的電腦上安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/overview)。 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>複製 TODO 應用程式範例，並準備存放庫

本教學課程將使用具有 Web UI 的 TODO 清單應用程式範例，此 UI 可用來呼叫由 [Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb) 支援的 Spring REST API。 您可在 [GitHub](https://github.com/Microsoft/spring-todo-app) 上找到應用程式的程式碼。 若要深入了解如何使用 Spring 和 Cosmos DB 撰寫 Java 應用程式，請參閱[搭配 Azure Cosmos DB SQL API 使用 Spring Boot Starter 的教學課程](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db )和 [Spring Data Azure Cosmos DB 快速入門](https://github.com/Microsoft/spring-data-cosmosdb#quick-start)。


在您的終端機中執行下列命令，即可複製範例存放庫並設定範例應用程式環境。

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>建立 Azure Cosmos DB

請遵循下列步驟在訂用帳戶中建立 Azure Cosmos DB 資料庫。 TODO 清單應用程式會連線到此資料庫，並在執行時儲存其資料，讓您無論在哪執行應用程式，皆可保存應用程式狀態。

1. 登入您的 Azure CLI，並選擇性地設定您的訂用帳戶 (如果您有多個與登入認證連線的訂用帳戶)。

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. 建立 Azure 資源群組，並記下 Azure 資源群組名稱。

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. 建立種類為 `GlobalDocumentDB` 的 Azure Cosmos DB。 Cosmos DB 的名稱只能使用小寫字母。 請記下命令回應中的 `documentEndpoint` 欄位。

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. 取得您的 Azure Cosmos DB 金鑰以連線到應用程式。 請將 `primaryMasterKey`、`documentEndpoint` 放在方便取得的地方，因為下一個步驟將會用到。

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>設定 TODO 應用程式屬性

在您的電腦上開啟終端機。 在所複製的存放庫中複製指令檔範例，以針對您剛才建立的 Cosmos DB 資料庫進行自訂。

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
在您慣用的編輯器中編輯 `.scripts/set-env-variables.sh`，並提供 Azure Cosmos DB 連線資訊。 針對 App Service Linux 設定，請使用與之前一樣的區域 (`your-resource-group-region`)，並使用建立 Cosmos DB 資料庫時使用的資源群組 (`your-azure-group-name`)。 選擇唯一的 WEBAPP_NAME，因為任何 Azure 部署中的任何 Web 應用程式名稱都不能重複。

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

然後執行指令碼：

```bash
source .scripts/set-env-variables.sh
```
   
這些環境變數會用於 TODO 清單應用程式中的 `application.properties`。 屬性檔案中的欄位會設定適用於 Spring Data 的預設存放庫組態：

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

然後，應用程式範例會使用從 `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` 匯入的 `@Document` 註釋，將實體類型設定為由 Cosmos DB 儲存及管理：

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>執行範例應用程式

使用 Maven 執行範例。

```bash
mvn package spring-boot:run
```

輸出應該看起來如下所示。

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

Spring TODO 應用程式啟動之後，您可以使用以下連結從本機存取應用程式：[http://localhost:8080/](http://localhost:8080/)。

 ![在本機存取 Spring TODO 應用程式](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

如果您看到例外狀況，而不是「啟動 TodoApplication」訊息，請確認上一個步驟中的 `bash` 指令碼是否已正確匯出環境變數，以及這些值是否適用於您建立的 Azure Cosmos DB 資料庫。

## <a name="configure-azure-deployment"></a>設定 Azure 部署

開啟 `initial/spring-boot-todo` 目錄中的 `pom.xml` 檔案，並新增下列[適用於 Maven 的 Azure Web 應用程式外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)組態。

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.8.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <runtime>
                 <os>linux</os>
                 <javaVersion>jre8</javaVersion>
                 <webContainer>jre8</webContainer>
             </runtime>
             <deployment>
                 <resources>
                 <resource>
                     <directory>${project.basedir}/target</directory>
                     <includes>
                     <include>*.jar</include>
                     </includes>
                 </resource>
                 </resources>
             </deployment>

            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property> 
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>

        </configuration>
    </plugin>           
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>在 Linux 上部署 Azure App Service

使用 `azure-webapp:deploy` Maven 目標將 TODO 應用程式部署至 Linux 上的 Azure App Service。

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.8.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource to /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Renaming /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7/spring-todo-app-2.0-SNAPSHOT.jar to app.jar
[INFO] Deploying the zip package spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7718326714198381983.zip...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2019-11-06T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

輸出會包含您已部署的應用程式 URL (在此範例中為 `https://spring-todo-app.azurewebsites.net`)。 您可以將此 URL 複製到網頁瀏覽器，或在終端機視窗中執行下列命令來載入您的應用程式。

```bash
open https://spring-todo-app.azurewebsites.net
```

您應該會看到應用程式正以網址列中的遠端 URL 執行：

 ![使用遠端 URL 執行的 Spring Boot 應用程式](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>擴充 TODO 應用程式

藉由新增另一個背景工作角色來擴充應用程式：

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>清除資源

如果您不需要這些資源來進行其他教學課程 (請參閱[後續步驟](#next))，您可以在 Cloud Shell 中執行下列命令來將這些資源刪除︰ 
  
```bash
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>後續步驟

[適用於 Java 開發人員的 Azure](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot)、[適用於 Cosmos DB 的 Spring 資料](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable)、[Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) 和 [App Service Linux](app-service-linux-intro.md)。

在開發人員指南中深入了解如何在 Linux 中的 App Service 上執行 Java 應用程式。

> [!div class="nextstepaction"] 
> [App Service Linux 中的 Java 開發人員指南](configure-language-java.md)
