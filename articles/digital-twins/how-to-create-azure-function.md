---
title: 在 Azure 中設定函數來處理資料
titleSuffix: Azure Digital Twins
description: 瞭解如何在 Azure 中建立可存取並由數位 twins 觸發的函式。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 04ca8d515dbc5a28a7d3a30369d97877928c9dc1
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683840"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>連接 Azure 中的函數應用程式以處理資料

更新以資料為基礎的數位 twins 會透過計算資源（例如使用 [Azure Functions](../azure-functions/functions-overview.md)所建立的函式），使用 [**事件路由**](concepts-route-events.md)進行處理。 函數可以用來更新數位對應項，以回應：
* 來自 IoT 中樞的裝置遙測資料
* 屬性變更或來自對應項圖形中其他數位對應項的其他資料

本文將逐步引導您在 Azure 中建立函式，以搭配 Azure 數位 Twins 使用。 

以下是其包含的步驟概述：

1. 在 Visual Studio 中建立 Azure Functions 專案
2. 使用 [事件方格](../event-grid/overview.md) 觸發程式撰寫函數
3. 將驗證程式代碼新增至函式 (，以便能夠存取 Azure 數位 Twins) 
4. 將函數應用程式發佈至 Azure
5. 設定函數應用程式的 [安全性](concepts-security.md) 存取

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>先決條件：設定 Azure 數位 Twins 實例

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>在 Visual Studio 中建立函數應用程式

在 Visual Studio 2019 中，選取 [檔案] _> 新的 > 專案_ ]，然後搜尋 _Azure Functions_ 範本。 選取 [下一步]  。

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio：新增專案對話方塊":::

指定函數應用程式的名稱，然後選取 [ _建立_]。

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio：設定新的專案":::

選取 [ *事件方格觸發* 程式] 的 [函數應用程式類型]，然後選取 [ _建立_]。

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Visual Studio： Azure Functions 專案觸發程式對話方塊":::

建立函數應用程式之後，Visual Studio 會在專案資料夾的 **Function1.cs** 檔案中產生程式碼範例。 這個 short 函數用來記錄事件。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio：含有範例程式碼的專案視窗":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>使用事件方格觸發程式撰寫函數

您可以藉由將 SDK 新增至函式應用程式來撰寫函式。 函數應用程式會使用 [適用于 .net 的 Azure 數位 TWINS SDK (c # ) ](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)來與 Azure 數位 Twins 互動。 

若要使用 SDK，您必須將下列套件包含在您的專案中。 您可以使用 Visual Studio 的 NuGet 套件管理員來安裝套件，或 `dotnet` 在命令列工具中使用新增套件。 針對您慣用的方法，請遵循下列步驟。

**選項1。使用 Visual Studio 套件管理員新增套件：**
    
以滑鼠右鍵選取您的專案，然後從清單中選取 [ _管理 NuGet 套件_ ]。 然後，在開啟的視窗中，選取 [ _流覽_ ] 索引標籤，並搜尋下列套件。 選取 [ _安裝_ 並 _接受_ 授權合約] 以安裝套件。

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**選項2。使用 `dotnet` 命令列工具新增套件：**

或者，您可以 `dotnet add` 在命令列工具中使用下列命令：

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

接下來，在 Visual Studio 方案總管中，開啟您擁有範例程式碼的 _Function1.cs_ 檔案，然後將下列語句新增至您的函式 `using` 。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>將驗證程式代碼新增至函式

您現在會宣告類別層級變數，並新增可讓函式存取 Azure 數位 Twins 的驗證程式代碼。 您會將下列程式新增至 _Function1.cs_ 檔案中的函式。

* 用來讀取 Azure 數位 Twins 服務 URL 作為環境變數的程式碼。 從環境變數讀取服務 URL 是很好的作法，而不是在函式中進行硬式編碼。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* 用來保存 HttpClient 實例的靜態變數。 HttpClient 的建立成本相當昂貴，而且我們想要避免在每次函式呼叫時都必須這麼做。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* 您可以在 Azure Functions 中使用受控識別認證。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* 在您的函式內新增本機變數 _DigitalTwinsClient_ ，以保存您的 Azure 數位 Twins 用戶端實例。 請勿在您的類別內將此 *變數設為* 靜態。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* 針對 _adtInstanceUrl_ 新增 null 檢查，並將您的函式邏輯包裝在 try/catch 區塊中，以攔截任何例外狀況。

這些變更之後，您的函式程式碼將會如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

現在您的應用程式已寫入，您可以使用下一節中的步驟將它發佈至 Azure。

## <a name="publish-the-function-app-to-azure"></a>將函數應用程式發佈至 Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

## <a name="set-up-security-access-for-the-function-app"></a>設定函數應用程式的安全性存取

您可以使用 Azure CLI 或 Azure 入口網站來設定函數應用程式的安全性存取。 請遵循下列步驟，以瞭解您的慣用選項。

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>選項1：使用 CLI 設定函數應用程式的安全性存取

先前範例的函式基本架構需要將持有人權杖傳遞給它，才能使用 Azure 數位 Twins 進行驗證。 若要確定已通過此持有人權杖，您必須為函數應用程式設定 [ (MSI) 的受控服務識別 ](../active-directory/managed-identities-azure-resources/overview.md) 。 每個函數應用程式只需要執行一次。

您可以建立系統管理的身分識別，並將函數應用程式的身分識別指派給 Azure 數位 Twins 實例的 _**Azure 數位 Twins 資料擁有**_ 者角色。 這會提供實例中的函數應用程式許可權，以執行資料平面活動。 然後，藉由設定環境變數，讓您的函式可以存取 Azure 數位 Twins 實例的 URL。

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

使用 [Azure Cloud Shell](https://shell.azure.com) 執行命令。

使用下列命令建立系統管理的身分識別。 記下輸出中的 _principalId_ 欄位。

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
使用下列命令中的 _principalId_ 值，將函數應用程式的身分識別指派給您 Azure Digital Twins 執行個體的「Azure Digital Twins 資料擁有者」角色。

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
最後，您可以藉由設定環境變數，讓您的 Azure 數位 Twins 實例的 URL 可供您的函式存取。 如需有關設定環境變數的詳細資訊，請參閱 [*環境變數*](/sandbox/functions-recipes/environment-variables)。 

> [!TIP]
> Azure 數位 Twins 實例的 URL 是藉由將 *HTTPs://* 新增至 Azure 數位 Twins 實例 *主機名稱* 的開頭來建立。 若要查看主機名稱，以及實例的所有屬性，您可以執行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>選項2：使用 Azure 入口網站設定函數應用程式的安全性存取

系統指派的受控識別可讓 Azure 資源向雲端服務進行驗證 (例如 Azure Key Vault) ，而不需要將認證儲存在程式碼中。 啟用之後，就可以透過 Azure 角色型存取控制來授與所有必要的許可權。 這種受控識別的生命週期會系結到此資源的生命週期。 此外，每個資源 (例如，虛擬機器) 只能有一個系統指派的受控識別。

在 [Azure 入口網站](https://portal.azure.com/)中，使用您稍早建立的函式應用程式名稱，在搜尋列中搜尋 _函數應用程式_ 。 從清單中選取 *函數應用程式* 。 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 入口網站：搜尋函數應用程式":::

在 [函式應用程式] 視窗中，選取左側導覽列中的 [ _識別_ ]，以啟用受控識別。
在 [ _系統指派_ ] 索引標籤下，將 _狀態_ 切換至開啟並 _儲存_ 。 您將會看到快顯視窗，以 _啟用系統指派的受控識別_。
選取 _[是]_ 按鈕。 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure 入口網站：啟用系統管理的身分識別":::

您可以在通知中確認已成功向 Azure Active Directory 註冊您的函式。

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure 入口網站：通知":::

另外也請注意 [身分 _識別_] 頁面上顯示的 **物件識別碼**，因為它將會在下一節中使用。

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="複製要在未來使用的物件識別碼":::

### <a name="assign-access-roles-using-azure-portal"></a>使用 Azure 入口網站指派存取角色

選取 [ _azure 角色指派_ ] 按鈕，這會開啟 [ *azure 角色指派* ] 頁面。 然後，選取 [ _+ 新增角色指派] (預覽)_。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure 入口網站：新增角色指派":::

在開啟的 [ _新增角色指派 (預覽])_ 頁面上，選取：

* _範圍_：資源群組
* _訂_ 用帳戶：選取您的 Azure 訂用帳戶
* _資源群組_：從下拉式清單中選取您的資源群組
* _角色_：從下拉式清單中選取 _Azure 數位 Twins 資料擁有_ 者

然後，按下 [ _儲存_ ] 按鈕來儲存您的詳細資料。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure 入口網站：新增角色指派 (預覽) ":::

### <a name="configure-application-settings-using-azure-portal"></a>使用 Azure 入口網站來設定應用程式設定

您可以藉由設定環境變數，讓您的函式可以存取您的 Azure 數位 Twins 實例的 URL。 如需有關這個的詳細資訊，請參閱 [*環境變數*](/sandbox/functions-recipes/environment-variables)。 應用程式設定會公開為環境變數，以存取數位 twins 實例。 

若要使用實例的 URL 來設定環境變數，請先尋找您的 Azure 數位 Twins 實例的主機名稱來取得 URL。 在 [Azure 入口網站](https://portal.azure.com) 搜尋列中搜尋您的實例。 然後，選取左側導覽列上的 _[總覽_ ] 以查看 _主機名稱_。 複製這個值。

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure 入口網站：總覽-> 複製要在 _Value_ 欄位中使用的主機名稱。":::

您現在可以依照下列步驟來建立應用程式設定：

1. 使用搜尋列中的函式應用程式名稱來搜尋應用程式，並從清單中選取函數應用程式
1. 選取 _左側導覽列上的_ [設定]，以建立新的應用程式設定
1. 在 [_應用程式設定_] 索引標籤中，選取 [ _+ 新增應用程式設定_]

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure 入口網站：搜尋現有的函數應用程式" lightbox="media/how-to-create-azure-function/search-for-azure-function.png":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure 入口網站：設定應用程式設定":::

在開啟的視窗中，使用上面複製的主機名稱值來建立應用程式設定。
* _名稱_ ： ADT_SERVICE_URL
* _值_： HTTPs：//{您的-twins-主機名稱}

選取 _[確定]_ 以建立應用程式設定。

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure 入口網站：加入應用程式設定。":::

您可以使用 [ _名稱_ ] 欄位下的應用程式名稱來查看您的應用程式設定。 然後，選取 [ _儲存_ ] 按鈕來儲存您的應用程式設定。

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure 入口網站：查看已建立的應用程式，並重新啟動應用程式":::

應用程式設定的任何變更都需要重新開機應用程式。 選取 [ _繼續_ ] 以重新開機您的應用程式。

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure 入口網站：儲存應用程式設定":::

您可以藉由選取 [ _通知_ ] 圖示來查看應用程式設定是否已更新。 如果未建立您的應用程式設定，您可以遵循上述程式來重試新增應用程式設定。

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure 入口網站：更新應用程式設定的通知":::

## <a name="next-steps"></a>下一步

在本文中，您已遵循在 Azure 中設定函數應用程式以搭配 Azure 數位 Twins 使用的步驟。

接下來，請參閱如何建立基本函式以將 IoT 中樞資料內嵌至 Azure 數位 Twins：
* [*作法：從 IoT 中樞內嵌遙測*](how-to-ingest-iot-hub-data.md)
