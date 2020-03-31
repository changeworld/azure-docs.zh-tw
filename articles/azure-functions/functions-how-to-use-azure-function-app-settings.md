---
title: 在 Azure 中配置功能應用設置
description: 了解如何設定 Azure Functions 應用程式設定。
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276942"
---
# <a name="manage-your-function-app"></a>管理功能應用 

在 Azure Functions 中，函數應用程式會提供個別函數的執行內容。 函數應用程式行為會套用至指定之函數應用程式所裝載的所有函數。 函數應用中的所有函數都必須具有相同的[語言](supported-languages.md)。 

函數應用中的各個函數一起部署並一起縮放。 同一函數應用中的所有函數共用資源，每個實例，如函數應用縮放。 

每個函數應用分別定義連接字串、環境變數和其他應用程式設定。 必須在函數應用之間共用的任何資料都應存儲在外部的持久存儲中。

本文介紹如何配置和管理函數應用。 

> [!TIP]  
> 許多配置選項也可以使用[Azure CLI]進行管理。 

## <a name="get-started-in-the-azure-portal"></a>開始使用 Azure 入口網站

若要開始，請移至 [Azure 入口網站]，然後登入您的 Azure 帳戶。 在入口網站頂端的搜尋列中，輸入函數應用程式的名稱，然後從清單中選取它。 選取函數應用程式之後，您會看到下列頁面：

![Azure 入口網站中的函數應用程式概觀](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

您可以導航到從概述頁面管理功能應用所需的一切，特別是**[應用程式設定](#settings)** 和**[平臺功能](#platform-features)**。

## <a name="application-settings"></a><a name="settings"></a>應用程式設定

"**應用程式設定"** 選項卡維護功能應用使用的設置。 這些設置是加密存儲的，您必須選擇 **"顯示值"** 才能查看門戶中的值。 您還可以使用 Azure CLI 訪問應用程式設定。

### <a name="portal"></a>入口網站

要在門戶中添加設置，請選擇 **"新建應用程式"設置**並添加新鍵值對。

![Azure 門戶中的函數應用設置。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

該[`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list)命令返回現有應用程式設定，如以下示例所示：

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

該[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)命令添加或更新應用程式設定。 下面的示例創建一個名稱`CUSTOM_FUNCTION_APP_SETTING`為鍵且值 為 的`12345`設置：


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>使用應用程式設定

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

在本地開發函數應用時，必須在本地.settings.json 專案檔案中維護這些值的本機複本。 要瞭解更多資訊，請參閱[本地設置檔](functions-run-local.md#local-settings-file)。

## <a name="platform-features"></a>平台功能

![函數應用程式平台功能索引標籤。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

函數應用程式是在 Azure App Service 平台中執行並由此平台維護。 因此，您的函數應用程式可以存取 Azure 核心虛擬主機平台的大多數功能。 [平台功能]**** 索引標籤可供您存取許多可在函數應用程式中使用的 App Service 平台功能。 

> [!NOTE]
> 當函數應用程式在「取用」主控方案上執行時，並非所有 App Service 功能都可供使用。

本文的其餘部分重點介紹 Azure 門戶中的以下應用服務功能，這些功能對函數很有用：

+ [App Service 編輯器](#editor)
+ [安慰](#console)
+ [高級工具（庫杜）](#kudu)
+ [部署選項](#deployment)
+ [CORS](#cors)
+ [驗證][](#auth)

如需有關如何使用 App Service 設定的詳細資訊，請參閱[設定 Azure App Service 設定](../app-service/configure-common.md)。

### <a name="app-service-editor"></a><a name="editor"></a>應用服務編輯器

![App Service 編輯器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service 編輯器是一個進階的入口網站內編輯器，可供您用來修改 JSON 組態檔和類似的程式碼檔案。 選擇此選項會啟動一個含有基本編輯器的個別瀏覽器索引標籤。 這可讓您與 Git 存放庫整合、執行程式碼和進行偵錯，以及修改函數應用程式設定。 與內建函數編輯器相比，此編輯器為您的函數提供了增強的開發環境。  

我們建議您考慮在本地電腦上開發函數。 在本地開發併發布到 Azure 時，專案檔案在門戶中是唯讀的。 要瞭解更多資訊，請參閱[本地代碼和測試 Azure 函數](functions-develop-local.md)。

### <a name="console"></a><a name="console"></a>安慰

![函數應用程式主控台](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

當您偏好從命令列與函數應用程式進行互動時，入口網站內主控台是一個理想的開發人員工具。 常用命令包含目錄和檔案建立與瀏覽，以及執行批次檔和指令碼。 

在本地開發時，我們建議使用[Azure 函數核心工具和](functions-run-local.md) [Azure CLI]。

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>進階工具 (Kudu)

![設定 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service 的進階工具 (也稱為 Kudu) 可讓您存取函數應用程式的進階系統管理功能。 從 Kudu，您可以管理系統資訊、應用程式設定、環境變數、網站擴充功能、HTTP 標頭，以及伺服器變數。 您也可以透過瀏覽至函數應用程式的 SCM 端點 (例如 `https://<myfunctionapp>.scm.azurewebsites.net/`) 來啟動 **Kudu** 


### <a name="deployment-center"></a><a name="deployment"></a>部署中心

當您使用原始程式碼管理解決方案來開發和維護函數代碼時，部署中心允許您從原始程式碼進行構建和部署。 進行更新時，將生成專案並將其部署到 Azure。 有關詳細資訊，請參閱[Azure 函數 中的部署技術](functions-deployment-technologies.md)。

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>跨原始資源共用

為了防止用戶端上惡意程式碼執行，現代瀏覽器會阻止從 Web 應用程式到在單獨域中運行的資源的請求。 [跨源資源分享 （CORS）](https://developer.mozilla.org/docs/Web/HTTP/CORS)允許`Access-Control-Allow-Origin`標頭聲明允許在函數應用上調用終結點的源。

#### <a name="portal"></a>入口網站

為函數應用配置 **"允許源**"清單時，`Access-Control-Allow-Origin`標頭將自動添加到函數應用中的 HTTP 終結點的所有回應中。 

![配置功能應用的 CORS 清單](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

使用萬用字元時`*`， 將忽略所有其他域。 

使用[`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add)命令將域添加到允許的原點清單中。 下面的示例添加contoso.com域：

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

使用[`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show)命令列出當前允許的原點。

### <a name="authentication"></a><a name="auth"></a>驗證

![設定函數應用程式的驗證](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

當函數使用 HTTP 觸發程序時，您可以要求呼叫必須先經過驗證。 應用服務支援 Azure 活動目錄身份驗證，並與社交供應商（如 Facebook、微軟和 Twitter）登錄。 如需設定特定驗證提供者的詳細資訊，請參閱 [Azure App Service 驗證概觀](../app-service/overview-authentication-authorization.md)。 


## <a name="next-steps"></a>後續步驟

+ [設定 Azure App Service 設定](../app-service/configure-common.md)
+ [Azure Functions 的持續部署](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure 門戶]: https://portal.azure.com
