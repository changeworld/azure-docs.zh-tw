---
title: 在 Azure Functions 中設定函數應用程式設定
description: 瞭解如何在 Azure Functions 中設定函數應用程式設定。
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: 4db6abeb3e6f4a07780268a6455177e0ca237205
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598477"
---
# <a name="manage-your-function-app"></a>管理您的函數應用程式 

在 Azure Functions 中，函數應用程式會提供個別函數的執行內容。 函數應用程式行為會套用至指定之函數應用程式所裝載的所有函數。 函數應用程式中的所有函式都必須是相同的 [語言](supported-languages.md)。 

函式應用程式中的個別函式會一起部署，並一起調整。 相同函式應用程式中的所有函式都會隨著函式應用程式的調整，共用每個實例的資源。 

系統會為每個函數應用程式個別定義連接字串、環境變數和其他應用程式設定。 必須在函式應用程式之間共用的任何資料都應儲存在持續性存放區的外部。

## <a name="get-started-in-the-azure-portal"></a>開始使用 Azure 入口網站

1. 若要開始，請移至 [Azure 入口網站]，然後登入您的 Azure 帳戶。 在入口網站頂端的搜尋列中，輸入函數應用程式的名稱，然後從清單中選取它。 

2. 在左窗格的 [設定] 底下 **，選取 [****設定**]。

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Azure 入口網站中的函數應用程式概觀":::

您可以從 [總覽] 頁面流覽至管理函數應用程式所需的所有專案，特別是 **[應用程式設定](#settings)** 和 **[平臺功能](#platform-features)**。

## <a name="work-with-application-settings"></a><a name="settings"></a>使用應用程式設定

您可以使用[Azure CLI](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings)和[Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings)，從[Azure 入口網站](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings)管理應用程式設定。 您也可以從 [Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) 和 [Visual Studio](functions-develop-vs.md#function-app-settings)管理應用程式設定。 

這些設定會以加密的儲存。 若要深入瞭解，請參閱 [應用程式設定安全性](security-concepts.md#application-settings)。

# <a name="portal"></a>[入口網站](#tab/portal)

[ **應用程式設定** ] 索引標籤會維護函數應用程式所使用的設定。 您必須選取 [ **顯示值** ]，才能在入口網站中查看值。 若要在入口網站中新增設定，請選取 [ **新增應用程式設定** ]，並新增新的機碼值組。

![Azure 入口網站中的函數應用程式設定。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

此 [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) 命令會傳回現有的應用程式設定，如下列範例所示：

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

此 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令會新增或更新應用程式設定。 下列範例會使用名為的索引鍵 `CUSTOM_FUNCTION_APP_SETTING` 和值來建立設定 `12345` ：


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

此 [`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting) Cmdlet 會傳回現有的應用程式設定，如下列範例所示： 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

此 [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) 命令會新增或更新應用程式設定。 下列範例會使用名為的索引鍵 `CUSTOM_FUNCTION_APP_SETTING` 和值來建立設定 `12345` ：

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>使用應用程式設定

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

當您在本機開發函數應用程式時，您必須在專案檔的 local.settings.js中維護這些值的本機複本。 若要深入瞭解，請參閱 [本機設定檔](functions-run-local.md#local-settings-file)案。

## <a name="hosting-plan-type"></a>主控方案類型

當您建立函數應用程式時，您也會建立應用程式執行所在的主控方案。 方案可以有一或多個函數應用程式。 函數的功能、規模調整和定價取決於方案的類型。 若要深入瞭解，請參閱 [Azure Functions 裝載選項](functions-scale.md)。

您可以從 Azure 入口網站、使用 Azure CLI 或 Azure PowerShell Api，判斷函數應用程式所使用的方案類型。 

下列值指出方案類型：

| 方案類型 | 入口網站 | Azure CLI/PowerShell |
| --- | --- | --- |
| [取用量](consumption-plan.md) | **取用量** | `Dynamic` |
| [高級](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [專用 (App Service) ](dedicated-plan.md) | 各種類型 | 各種類型 |

# <a name="portal"></a>[入口網站](#tab/portal)

若要判斷您的函數應用程式所使用的計畫類型，請參閱 [Azure 入口網站](https://portal.azure.com)中函數應用程式的 [**總覽**] 索引標籤中的 **App Service 方案**。 若要查看定價層，請選取 **App Service 方案** 的名稱，然後從左窗格選取 [ **屬性** ]。

![在入口網站中檢視調整方案](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

執行下列 Azure CLI 命令以取得您的主控方案類型：

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

在上述範例中 `<RESOURCE_GROUP>` `<FUNCTION_APP_NAME>` ，請將和取代為個別的資源群組和函式應用程式名稱。 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

執行下列 Azure PowerShell 命令以取得您的主控方案類型：

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
在上述範例中 `<RESOURCE_GROUP>` `<FUNCTION_APP_NAME>` ，請將和取代為個別的資源群組和函式應用程式名稱。 

---

## <a name="plan-migration"></a>規劃移轉

您可以使用 Azure CLI 命令，在 Windows 上的耗用量方案和高階方案之間遷移函數應用程式。 特定的命令取決於遷移的方向。 目前不支援直接遷移至專用 (App Service) 方案。

Linux 不支援這種遷移。

### <a name="consumption-to-premium"></a>Premium 的耗用量

使用下列程式，從取用方案遷移至 Windows 上的高階方案：

1. 執行下列命令，以在與現有函式應用程式相同的區域和資源群組中，建立新的 App Service 方案 (彈性 Premium) 。  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. 執行下列命令，將現有的函式應用程式遷移至新的 Premium 方案

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. 如果您不再需要先前的耗用量函式應用程式方案，請在確認您已成功遷移至新的函式應用程式計畫之後，刪除原始函式應用程式方案。 執行下列命令，以取得資源群組中所有使用量方案的清單。

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    您可以安全地刪除具有零個網站的方案，也就是您從中遷移的網站。

1. 執行下列命令，以刪除您從遷移的耗用量方案。

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Premium 至耗用量

使用下列程式，從高階方案遷移至 Windows 上的耗用量方案：

1. 執行下列命令，以在與現有函式應用程式相同的區域和資源群組中建立新的函數應用程式 (耗用量) 。 此命令也會建立函數應用程式執行所在的新取用方案。

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. 執行下列命令，將現有的函式應用程式遷移至新的耗用量方案。

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. 刪除您在步驟1中建立的函式應用程式，因為您只需要為了執行現有的函數應用程式所建立的計畫。

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. 如果您不再需要先前的高階函式應用程式方案，請在確認您已成功遷移至新的函式應用程式計畫之後，刪除原始函數應用程式方案。 請注意，如果未刪除方案，您仍需支付 Premium 方案的費用。 執行下列命令，以取得資源群組中所有 Premium 方案的清單。

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. 執行下列命令，以刪除您從遷移的高階方案。

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>平台功能

函數應用程式會在中執行，並由 Azure App Service 的平臺維護。 因此，您的函數應用程式可以存取 Azure 核心虛擬主機平台的大多數功能。 您可以在左窗格中存取 App Service 平臺的許多功能，以便在函式應用程式中使用。 

> [!NOTE]
> 當函數應用程式在「取用」主控方案上執行時，並非所有 App Service 功能都可供使用。

本文的其餘部分將著重于下列 Azure 入口網站中適用于函數的 App Service 功能：

+ [App Service 編輯器](#editor)
+ [主控台](#console)
+ [Advanced tools (Kudu) ](#kudu)
+ [部署選項](#deployment)
+ [CORS](#cors)
+ [驗證](#auth)

如需有關如何使用 App Service 設定的詳細資訊，請參閱[設定 Azure App Service 設定](../app-service/configure-common.md)。

### <a name="app-service-editor"></a><a name="editor"></a>App Service 編輯器

![App Service 編輯器](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service 編輯器是一個進階的入口網站內編輯器，可供您用來修改 JSON 組態檔和類似的程式碼檔案。 選擇此選項會啟動一個含有基本編輯器的個別瀏覽器索引標籤。 這可讓您與 Git 存放庫整合、執行程式碼和進行偵錯，以及修改函數應用程式設定。 此編輯器會針對您的函式提供增強的開發環境，相較于內建函數編輯器。  

建議您考慮在本機電腦上開發您的功能。 當您在本機開發併發布至 Azure 時，您的專案檔在入口網站中是唯讀的。 若要深入瞭解，請參閱在本機進行程式 [代碼和測試 Azure Functions](functions-develop-local.md)。

### <a name="console"></a><a name="console"></a>主控台

![函數應用程式主控台](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

當您偏好從命令列與函數應用程式進行互動時，入口網站內主控台是一個理想的開發人員工具。 常用命令包含目錄和檔案建立與瀏覽，以及執行批次檔和指令碼。 

在本機開發時，建議使用 [Azure Functions Core Tools](functions-run-local.md) 和 [Azure CLI]。

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>進階工具 (Kudu)

![設定 Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service 的進階工具 (也稱為 Kudu) 可讓您存取函數應用程式的進階系統管理功能。 從 Kudu，您可以管理系統資訊、應用程式設定、環境變數、網站擴充功能、HTTP 標頭，以及伺服器變數。 您也可以透過瀏覽至函數應用程式的 SCM 端點 (例如 `https://<myfunctionapp>.scm.azurewebsites.net/`) 來啟動 **Kudu** 


### <a name="deployment-center"></a><a name="deployment"></a>部署中心

當您使用原始檔控制方案來開發和維護您的函式程式碼時，部署中心可讓您從原始檔控制中建立和部署。 當您進行更新時，您的專案就會建立並部署到 Azure。 如需詳細資訊，請參閱 [Azure Functions 中的部署技術](functions-deployment-technologies.md)。

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>跨原始資源共用

為了防止惡意程式碼在用戶端上執行，新式瀏覽器會封鎖從 web 應用程式到在個別網域中執行之資源的要求。 [跨原始來源資源分享 (CORS) ](https://developer.mozilla.org/docs/Web/HTTP/CORS) 可讓 `Access-Control-Allow-Origin` 標頭宣告可在函數應用程式上呼叫端點的來源。

#### <a name="portal"></a>入口網站

當您為函式應用程式設定 **允許的來源** 清單時，會 `Access-Control-Allow-Origin` 自動將標頭新增至函式應用程式中來自 HTTP 端點的所有回應。 

![設定函數應用程式的 CORS 清單](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

使用 () 萬用字元時 `*` ，會忽略所有其他網域。 

使用 [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) 命令將網域新增至允許的來源清單。 下列範例會新增 contoso.com 網域：

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

使用 [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) 命令可列出目前允許的來源。

### <a name="authentication"></a><a name="auth"></a>驗證

![設定函數應用程式的驗證](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

當函數使用 HTTP 觸發程序時，您可以要求呼叫必須先經過驗證。 App Service 支援 Azure Active Directory 驗證，以及使用社交提供者（例如 Facebook、Microsoft 和 Twitter）登入。 如需設定特定驗證提供者的詳細資訊，請參閱 [Azure App Service 驗證概觀](../app-service/overview-authentication-authorization.md)。 


## <a name="next-steps"></a>後續步驟

+ [設定 Azure App Service 設定](../app-service/configure-common.md)
+ [Azure Functions 的持續部署](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure 入口網站]: https://portal.azure.com
