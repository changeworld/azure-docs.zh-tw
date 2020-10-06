---
title: 使用 Azure Resource Manager 範本建立 App Service 應用程式
description: 使用 Azure Resource Manager 範本在幾秒內於 Azure App Service 中建立您的第一個應用程式，這是部署至 App Service 的其中一種方法。
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653250"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立 App Service 應用程式

在 Cloud Shell 中使用 Azure Resource Manager 範本和 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 將應用程式部署至雲端，以開始使用 [Azure App Service](overview.md)。 由於您是使用免費的 App Service 層，因此不需支付任何費用，就能完成本快速入門。

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先決條件：

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>建立 Azure App Service 應用程式

### <a name="review-the-template"></a>檢閱範本

::: zone pivot="platform-windows"
本快速入門中使用的範本是來自 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/)。 其會在 Windows 上部署 App Service 方案和 App Service 應用程式。 其與 .NET Core、.NET Framework、PHP、Node.js 和靜態 HTML 應用程式相容。 若為 Java，請參閱[建立 Java 應用程式](app-service-web-get-started-java.md)。 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

範本中定義了兩個 Azure 資源：

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：建立 App Service 方案。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：建立 App Service 應用程式。

此範本包含數個為了讓您方便而預先定義的參數。 請參閱下表中的參數預設值和其描述：

| 參數 | 類型    | 預設值                | 說明： |
|------------|---------|------------------------------|-------------|
| webAppName | 字串  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | 應用程式名稱 |
| location   | 字串  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | 應用程式區域 |
| sku        | 字串  | "F1"                         | 執行個體大小 (F1 = 免費層) |
| 語言   | 字串  | ".net"                       | 程序設計語言堆疊 (.net、php、node、html) |
| helloWorld | boolean | False                        | True = 部署 "Hello World" 應用程式 |
| repoUrl    | 字串  |                             | 外部 Git 存放庫 (選擇性) |
::: zone-end
::: zone pivot="platform-linux"
本快速入門中使用的範本是來自 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/)。 其會在 Linux 上部署 App Service 方案和 App Service 應用程式。 其與 App Service 上所有支援的程序設計語言相容。

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

範本中定義了兩個 Azure 資源：

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：建立 App Service 方案。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：建立 App Service 應用程式。

此範本包含數個為了讓您方便而預先定義的參數。 請參閱下表中的參數預設值和其描述：

| 參數 | 類型    | 預設值                | 說明： |
|------------|---------|------------------------------|-------------|
| webAppName | 字串  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | 應用程式名稱 |
| location   | 字串  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | 應用程式區域 |
| sku        | 字串  | "F1"                         | 執行個體大小 (F1 = 免費層) |
| linuxFxVersion   | 字串  | "DOTNETCORE&#124;3.0        | "Programming language stack &#124; Version" |
| repoUrl    | 字串  |                             | 外部 Git 存放庫 (選擇性) |

---
::: zone-end


### <a name="deploy-the-template"></a>部署範本

這裡使用 Azure CLI 來部署範本。 您也可以使用 Azure 入口網站、Azure PowerShell 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-powershell.md)。 

下列程式碼會建立資源群組、App Service 方案和 Web 應用程式。 已為您設定好預設的資源群組、App Service 方案和位置。 請將 `<app-name>` 取代為全域唯一的應用程式名稱 (有效字元為 `a-z`、`0-9` 和 `-`)。

::: zone pivot="platform-windows"
執行下列程序碼，在 Windows 上部署 .NET Framework 應用程式。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

若要部署不同的語言堆疊，請使用適當值更新 `linuxFxVersion`。 範例如下所示。 若要顯示目前的版本，請在 Cloud Shell 中執行下列命令：`az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Language    | 範例：                                               |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> 您可以[在這裡找到更多 Azure App Service 範本範例](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)。


## <a name="validate-the-deployment"></a>驗證部署

瀏覽至 `http://<app_name>.azurewebsites.net/` 並確認已建立好。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，請[刪除資源群組](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>從本機 Git 進行部署

> [!div class="nextstepaction"]
>ASP.NET Core 搭配 SQL Database

> [!div class="nextstepaction"]
>Python with Postgres

> [!div class="nextstepaction"]
>PHP with MySQL

> [!div class="nextstepaction"]
> [使用 Java 連線至 Azure SQL 資料庫](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [對應自訂網域](app-service-web-tutorial-custom-domain.md)