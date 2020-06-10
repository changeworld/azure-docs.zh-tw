---
title: 快速入門：在 Windows 上建立 Java 應用程式
description: 在短短幾分鐘內將您的第一個 Java Hello World 部署至 Windows 上的 Azure App Service。 適用於 Maven 的 Azure Web 應用程式外掛程式可讓您輕鬆部署 Java 應用程式。
keywords: Azure, App Service, Web 應用程式, Windows, Java, Maven, 快速入門
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: b07ffe92a5dd0c105188fab55bc679c04f660ed2
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300938"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>快速入門：在 Windows 上的 Azure App Service 中建立 Java 應用程式

> [!NOTE]
> 本文會將應用程式部署至 Windows 上的 App Service。 若要部署至 _Linux_ 上的 App Service，請參閱[在 Linux 上建立 Java Web 應用程式](./containers/quickstart-java.md)。
>

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。  本快速入門說明如何使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 搭配[適用於 Maven 的 Azure Web 應用程式外掛程式](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)，部署 Java Web 封存 (WAR) 檔案。

> [!NOTE]
> 使用 IntelliJ 和 Eclipse 等熱門 IDE 也可以執行相同的動作。 請參閱 [Azure Toolkit for IntelliJ 快速入門](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入門](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)中的類似文件。
>
![在 Azure App Service 中執行的範例應用程式](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>建立 Java 應用程式

在 Cloud Shell 提示字元中執行下列 Maven 命令，以建立名為 `helloworld` 的新應用程式：

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp -Dversion=1.0-SNAPSHOT
```

然後將您的工作目錄變更為專案資料夾：

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>設定 Maven 外掛程式

您可以在命令提示字元處，執行下列 maven 命令來設定部署，為第一個步驟中的 **Windows** OS 選擇 ['2']，然後按 **ENTER** 鍵接受預設設定，直到出現 [確認 (是/否)] 提示，然後按 ['y'] 完成設定。 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

範例程序如下所示：

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```

> [!NOTE]
> 在本文中，我們只會使用封裝在 WAR 檔案中的 JAVA 應用程式。 此外掛程式也支援 JAR Web 應用程式，請瀏覽[將 Java SE JAR 檔案部署至 Linux 上的 App Service](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，並加以試用。

開啟 `pom.xml`，查看更新過的設定。

```bash
code pom.xml
```

您可視需要，直接在 pom 檔案中修改 App Service 的其他設定，以下列出一些常見的設定：

 屬性 | 必要 | 描述 | 版本
---|---|---|---
`<schemaVersion>` | false | 指定組態結構描述的版本。 支援的值包括：`v1`、`v2`。 | 1.5.2
`<resourceGroup>` | true | Web 應用程式的 Azure 資源群組。 | 0.1.0+
`<appName>` | true | 您的 Web 應用程式名稱。 | 0.1.0+
`<region>` | true | 指定將裝載 Web 應用程式的區域；預設值為 **westeurope**。 所有有效的區域皆列於[支援的區域](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)一節。 | 0.1.0+
`<pricingTier>` | false | 您 Web 應用程式的定價層。 預設值為 **P1V2**。| 0.1.0+
`<runtime>` | true | 執行階段環境組態，您可以在[此處](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)查看詳細資料。 | 0.1.0+
`<deployment>` | true | 部署組態，您可以在[此處](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)查看詳細資料。 | 0.1.0+

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>部署應用程式

部署到 Azure App Service 的程序從 Azure CLI 使用帳戶認證。 繼續執行前，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```
接著，可以使用下列命令將您的 Java 應用程式部署至 Azure：

```bash
mvn package azure-webapp:deploy
```

完成部署後，在網頁瀏覽器中使用下列 URL，瀏覽至已部署的應用程式，例如 `http://<webapp>.azurewebsites.net/`。

![在 Azure App Service 中執行的範例應用程式](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**恭喜！** 您已將第一個 Java 應用程式部署至 Windows 上的 App Service。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用 Java 連線至 Azure SQL 資料庫](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [使用 Java 連線至適用於 MySQL 的 Azure DB](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [使用 Java 連線至適用於 PostgreSQL 的 Azure DB](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [適用於 Java 開發人員的 Azure 資源](/java/azure/)

> [!div class="nextstepaction"]
> [對應自訂網域](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [深入了解 Azure 的 Maven 外掛程式](https://github.com/microsoft/azure-maven-plugins)
