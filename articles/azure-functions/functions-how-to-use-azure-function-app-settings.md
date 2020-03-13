---
title: 在 Azure 中設定函數應用程式設定
description: 了解如何設定 Azure Functions 應用程式設定。
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276942"
---
# <a name="manage-your-function-app"></a>管理您的函數應用程式 

在 Azure Functions 中，函數應用程式會提供個別函數的執行內容。 函數應用程式行為會套用至指定之函數應用程式所裝載的所有函數。 函數應用程式中的所有函式都必須是相同的[語言](supported-languages.md)。 

函式應用程式中的個別函式會一起部署並一起調整。 相同函式應用程式中的所有函式都會隨著函式應用程式的調整，與每個實例共用資源。 

針對每個函式應用程式，會分別定義連接字串、環境變數和其他應用程式設定。 必須在函式應用程式之間共用的任何資料，都應該儲存在外部保存的存放區中。

本文說明如何設定和管理您的函數應用程式。 

> [!TIP]  
> 您也可以使用[Azure CLI]來管理許多設定選項。 

## <a name="get-started-in-the-azure-portal"></a>開始使用 Azure 入口網站

若要開始，請移至 [Azure 入口網站]，然後登入您的 Azure 帳戶。 在入口網站頂端的搜尋列中，輸入函數應用程式的名稱，然後從清單中選取它。 選取函數應用程式之後，您會看到下列頁面：

![Azure 入口網站中的函數應用程式概觀](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

您可以從 [總覽] 頁面流覽至管理函式應用程式所需的所有專案，特別是 [ **[應用程式設定](#settings)** ] 和 [ **[平臺] 功能](#platform-features)** 。

## <a name="settings"></a>應用程式設定

[**應用程式設定**] 索引標籤會維護您的函數應用程式所使用的設定。 這些設定會以加密方式儲存，而且您必須選取 [**顯示值**]，才能在入口網站中查看值。 您也可以使用 Azure CLI 來存取應用程式設定。

### <a name="portal"></a>入口網站

若要在入口網站中新增設定，請選取 [**新增應用程式設定**]，然後新增新的機碼值組。

![Azure 入口網站中的函數應用程式設定。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

[`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list)命令會傳回現有的應用程式設定，如下列範例所示：

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)命令會新增或更新應用程式設定。 下列範例會使用名為 `CUSTOM_FUNCTION_APP_SETTING` 的索引鍵和 `12345`的值來建立設定：


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>使用應用程式設定

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

當您在本機開發函式應用程式時，您必須在本機的設定 json 專案檔中維護這些值的本機複本。 若要深入瞭解，請參閱[本機設定檔](functions-run-local.md#local-settings-file)。

## <a name="platform-features"></a>平臺功能

![函數應用程式平台功能索引標籤。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

函數應用程式是在 Azure App Service 平台中執行並由此平台維護。 因此，您的函數應用程式可以存取 Azure 核心虛擬主機平台的大多數功能。 [平台功能] 索引標籤可供您存取許多可在函數應用程式中使用的 App Service 平台功能。 

> [!NOTE]
> 當函數應用程式在「取用」主控方案上執行時，並非所有 App Service 功能都可供使用。

本文的其餘部分著重于 Azure 入口網站中適用于函數的下列 App Service 功能：

+ [App Service 編輯器](#editor)
+ [Console](#console)
+ [進階工具 (Kudu)](#kudu)
+ [部署選項](#deployment)
+ [CORS](#cors)
+ [驗證](#auth)

如需有關如何使用 App Service 設定的詳細資訊，請參閱[設定 Azure App Service 設定](../app-service/configure-common.md)。

### <a name="editor"></a>App Service 編輯器

![App Service 編輯器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service 編輯器是一個進階的入口網站內編輯器，可供您用來修改 JSON 組態檔和類似的程式碼檔案。 選擇此選項會啟動一個含有基本編輯器的個別瀏覽器索引標籤。 這可讓您與 Git 存放庫整合、執行程式碼和進行偵錯，以及修改函數應用程式設定。 相較于內建函數編輯器，此編輯器為您的函式提供了增強的開發環境。  

我們建議您考慮在本機電腦上開發您的功能。 當您在本機開發併發布至 Azure 時，您的專案檔案在入口網站中是唯讀的。 若要深入瞭解，請參閱在本機撰寫程式[代碼和測試 Azure Functions](functions-develop-local.md)。

### <a name="console"></a>主控台

![函數應用程式主控台](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

當您偏好從命令列與函數應用程式進行互動時，入口網站內主控台是一個理想的開發人員工具。 常用命令包含目錄和檔案建立與瀏覽，以及執行批次檔和指令碼。 

在本機開發時，我們建議使用[Azure Functions Core Tools](functions-run-local.md)和[Azure CLI]。

### <a name="kudu"></a>進階工具 (Kudu)

![設定 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service 的進階工具 (也稱為 Kudu) 可讓您存取函數應用程式的進階系統管理功能。 從 Kudu，您可以管理系統資訊、應用程式設定、環境變數、網站擴充功能、HTTP 標頭，以及伺服器變數。 您也可以透過瀏覽至函數應用程式的 SCM 端點 (例如 **) 來啟動** Kudu`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment"></a>部署中心

當您使用原始檔控制方案來開發和維護您的函式程式碼時，部署中心可讓您從原始檔控制建立和部署。 當您進行更新時，會建立您的專案，並將其部署至 Azure。 如需詳細資訊，請參閱[Azure Functions 中的部署技術](functions-deployment-technologies.md)。

### <a name="cors"></a>跨原始來源資源分享

為了避免在用戶端上執行惡意程式碼，現代化的瀏覽器會封鎖從 web 應用程式對在不同網域中執行之資源的要求。 [跨原始來源資源分享（CORS）](https://developer.mozilla.org/docs/Web/HTTP/CORS)可讓 `Access-Control-Allow-Origin` 標頭宣告哪些來源可以呼叫您函式應用程式上的端點。

#### <a name="portal"></a>入口網站

當您設定函數應用程式的 [允許的原始**來源**] 清單時，會自動將 `Access-Control-Allow-Origin` 標頭新增至函式應用程式中來自 HTTP 端點的所有回應。 

![設定函數應用程式的 CORS 清單](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

使用萬用字元（`*`）時，會忽略所有其他的網域。 

使用[`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add)命令，將網域新增至 [允許的原始來源] 清單。 下列範例會新增 contoso.com 網域：

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

使用[`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show)命令來列出目前允許的原始來源。

### <a name="auth"></a>驗證

![設定函數應用程式的驗證](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

當函數使用 HTTP 觸發程序時，您可以要求呼叫必須先經過驗證。 App Service 支援使用社交提供者（例如 Facebook、Microsoft 和 Twitter）的 Azure Active Directory 驗證和登入。 如需設定特定驗證提供者的詳細資訊，請參閱 [Azure App Service 驗證概觀](../app-service/overview-authentication-authorization.md)。 


## <a name="next-steps"></a>後續步驟

+ [設定 Azure App Service 設定](../app-service/configure-common.md)
+ [Azure Functions 的持續部署](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure 入口網站]: https://portal.azure.com
