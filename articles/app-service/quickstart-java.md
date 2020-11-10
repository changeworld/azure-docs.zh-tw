---
title: 快速入門：在 Azure App Service 上建立 Java 應用程式
description: 在短短幾分鐘內將您的第一個 Java Hello World 部署至 Azure App Service。 適用於 Maven 的 Azure Web 應用程式外掛程式可讓您輕鬆部署 Java 應用程式。
keywords: Azure, App Service, Web 應用程式, Windows, Linux, Java, Maven, 快速入門
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 3c5a04bd14bdcbf250908db78c622b963f191d91
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333064"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>快速入門：在 Azure App Service 上建立 Java 應用程式

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。  本快速入門說明如何使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 搭配[適用於 Maven 的 Azure Web 應用程式外掛程式](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)，部署 .jar 檔案或 .war 檔案。 使用索引標籤，在 Java SE 與 Tomcat 指令之間切換。


> [!NOTE]
> 使用 IntelliJ 和 Eclipse 等熱門 IDE 也可以執行相同的動作。 請參閱 [Azure Toolkit for IntelliJ 快速入門](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入門](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)中的類似文件。


![在 Azure App Service 中執行的範例應用程式](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>建立 Java 應用程式

# <a name="java-se"></a>[Java SE](#tab/javase)

複製 [Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot) 範例專案。

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

將目錄變更至已完成的專案。

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

在 Cloud Shell 提示字元中執行下列 Maven 命令，以建立名為 `helloworld` 的新應用程式：

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

然後將您的工作目錄變更為專案資料夾：

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>設定 Maven 外掛程式

部署到 Azure App Service 的程序會自動從 Azure CLI 使用您的 Azure 認證。 如果 Azure CLI 未安裝在本機，則 Maven 外掛程式會使用 Oauth 或裝置登入進行驗證。 如需詳細資訊，請參閱[使用 Maven 外掛程式進行驗證](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)。

執行下列 Maven 命令來設定部署。 此命令可協助您設定 App Service 作業系統、Java 版本和 Tomcat 版本。

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. 出現 [訂用帳戶] 選項的提示時，請輸入行首列出的號碼，以選取適當的 `Subscription`。
1. 出現 [Web 應用程式] 選項的提示時，請按 Enter 鍵以接受預設選項 `<create>`，或選取現有應用程式。
1. 出現 [作業系統] 選項的提示時，請輸入 `3` 以選取 **Windows** 。
1. 當系統提示 **定價層** 選項時，請輸入 `2` 以選取 **B2** 。
1. 按 Enter 鍵，使用預設 Java 版本 **Java 8** 。
1. 最後，在最後一個提示時按 Enter 鍵以確認您的選擇。

    您的摘要輸出看起來類似如下所示的程式碼片段。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 出現 [訂用帳戶] 選項的提示時，請輸入行首列出的號碼，以選取適當的 `Subscription`。
1. 出現 [Web 應用程式] 選項的提示時，請按 Enter 鍵以接受預設選項 `<create>`，或選取現有應用程式。
1. 出現 [作業系統] 選項的提示時，請輸入 `3` 以選取 **Windows** 。
1. 當系統提示 **定價層** 選項時，請輸入 `2` 以選取 **B2** 。
1. 按 Enter 鍵，使用預設 Java 版本 **Java 8** 。
1. 按 Enter 鍵，使用預設 Web 容器 **Tomcat 8.5** 。
1. 最後，在最後一個提示時按 Enter 鍵以確認您的選擇。

    您的摘要輸出看起來類似如下所示的程式碼片段。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. 出現 [訂用帳戶] 選項的提示時，請輸入行首列出的號碼，以選取適當的 `Subscription`。
1. 出現 [Web 應用程式] 選項的提示時，請按 Enter 鍵以接受預設選項 `<create>`，或選取現有應用程式。
1. 出現 [作業系統] 選項的提示時，請按 Enter 鍵以選取 **Linux** 。
1. 當系統提示 **定價層** 選項時，請輸入 `2` 以選取 **B2** 。
1. 按 Enter 鍵，使用預設 Java 版本 **Java 8** 。
1. 最後，在最後一個提示時按 Enter 鍵以確認您的選擇。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 出現 [訂用帳戶] 選項的提示時，請輸入行首列出的號碼，以選取適當的 `Subscription`。
1. 出現 [Web 應用程式] 選項的提示時，請按 Enter 鍵以接受預設選項 `<create>`，或選取現有應用程式。
1. 出現 [作業系統] 選項的提示時，請按 Enter 鍵以選取 **Linux** 。
1. 當系統提示 **定價層** 選項時，請輸入 `2` 以選取 **B2** 。
1. 按 Enter 鍵，使用預設 Java 版本 **Java 8** 。
1. 按 Enter 鍵，使用預設 Web 容器 **Tomcat 8.5** 。
1. 最後，在最後一個提示時按 Enter 鍵以確認您的選擇。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

您可視需要，直接在 `pom.xml` 中修改 App Service 的其他設定。 以下列出一些常見的設定：

屬性 | 必要 | 描述 | 版本
---|---|---|---
`<schemaVersion>` | false | 指定組態結構描述的版本。 支援的值包括：`v1`、`v2`。 | 1.5.2
`<subscriptionId>` | false | 指定訂用帳戶識別碼。 | 0.1.0+
`<resourceGroup>` | true | Web 應用程式的 Azure 資源群組。 | 0.1.0+
`<appName>` | true | 您的 Web 應用程式名稱。 | 0.1.0+
`<region>` | true | 指定將裝載 Web 應用程式的區域；預設值為 **westeurope** 。 所有有效的區域皆列於[支援的區域](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)一節。 | 0.1.0+
`<pricingTier>` | false | 您 Web 應用程式的定價層。 生產工作負載預設值為 **P1V2** ，而 Java 開發/測試建議最少使用 **B2** 。 [深入了解](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | 執行階段環境組態，您可以在[此處](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)查看詳細資料。 | 0.1.0+
`<deployment>` | true | 部署組態，您可以在[此處](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)查看詳細資料。 | 0.1.0+

請小心 `<appName>` 和 `<resourceGroup>`的值 (在示範中為 `helloworld-1590394316693` 和 `helloworld-1590394316693-rg`)，稍後會用到這兩個值。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>部署應用程式

Maven 外掛程式會從 Azure CLI 使用帳戶認證來部署至 App Services。 繼續執行前，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli)。

```azurecli
az login
```

接著，可以使用下列命令將您的 Java 應用程式部署至 Azure。

```bash
mvn package azure-webapp:deploy
```

部署完成後，您的應用程式就會在 `http://<appName>.azurewebsites.net/` (示範中為 `http://helloworld-1590394316693.azurewebsites.net`) 準備就緒。 使用您的本機網頁瀏覽器開啟 URL，您應該會看到

![在 Azure App Service 中執行的範例應用程式](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**恭喜！** 您已將第一個 Java 應用程式部署至 App Service。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在資源群組中建立了 Azure 資源。 如果您在未來不需要這些資源，請從入口網站刪除資源群組，或在 Cloud Shell 中執行下列命令：

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

此命令可能會花一分鐘執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Java 連線至適用於 PostgreSQL 的 Azure DB](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [設定 CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [定價資訊](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [彙總記錄和計量](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [相應增加](manage-scale-up.md)

> [!div class="nextstepaction"]
> [適用於 Java 開發人員的 Azure 資源](/java/azure/)

> [!div class="nextstepaction"]
> [設定您的 Java 應用程式](configure-language-java.md)
