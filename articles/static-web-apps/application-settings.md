---
title: 設定 Azure Static Web Apps 的應用程式設定
description: 瞭解 Azure Static Web Apps 的應用程式設定
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 66ad9c27ca69df230d9ce1d2282e734420fa14f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373655"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>設定 Azure Static Web Apps 預覽版本的應用程式設定

應用程式設定會保留可能會變更的設定值，例如資料庫連接字串。 新增應用程式設定可讓您修改應用程式的設定輸入，而不需要變更應用程式程式碼。

應用程式設定會：

- 待用加密
- 複製到[預備](review-publish-pull-requests.md)和實際執行環境

應用程式設定有時候也稱為「環境變數」。

> [!IMPORTANT]
> 本文所述的應用程式設定僅適用於 Azure Static Web App 的後端 API。
>
> 如需在前端 Web 應用程式使用環境變數的詳細資訊，請參閱您的 [JavaScript 架構](#javascript-frameworks-and-libraries)或[靜態網站產生器](#static-site-generators)的文件。

## <a name="prerequisites"></a>Prerequisites

- Azure Static Web Apps 應用程式
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="types-of-application-settings"></a>應用程式設定的類型

Azure Static Web Apps 應用程式通常有兩個層面。 第一個是 Web 應用程式 (或稱為「靜態內容」)，以 HTML、CSS、JavaScript、影像表示。 第二個是後端 API，由 Azure Functions 應用程式提供。

本文示範如何管理 Azure Functions 中後端 API 的應用程式設定。

本文所述的應用程式設定不能在靜態 Web 應用程式中使用或參考。 不過，許多前端架構和靜態網站產生器可讓您在開發期間使用環境變數。 在組建期間，要使用在產生的 HTML 或 JavaScript 中的值取代這些變數。 由於網站訪客可以輕鬆地探索 HTML 和 JavaScript 中的資料，因此您要避免在前端應用程式中放置機密資訊。 保存機密資料的設定最好位於應用程式的 API 部分。

如需如何搭配使用環境變數與 JavaScript 架構或程式庫的相關資訊，請參閱下列文章以取得詳細資料。

### <a name="javascript-frameworks-and-libraries"></a>JavaScript 架構和程式庫

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>靜態網站產生器

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>關於 API 應用程式設定

Azure Static Web Apps 中的 API 是由 Azure Functions 提供，後者可讓您在 local.settings.json 檔案中定義應用程式設定。 此檔案會在設定的 `Values` 屬性中定義應用程式設定。

下列的範例 local.settings.json 顯示如何為 `DATABASE_CONNECTION_STRING` 新增一個值。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

在程式碼中可以用環境變數參考 `Values` 屬性中定義的設定 (可從 `process.env` 物件取得)。

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

GitHub 存放庫不會追蹤 `local.settings.json` 檔案，因為機密資訊 (例如資料庫連接字串) 通常會放在此檔案中。 由於本機設定會保留在您的電腦上，您需要手動將您的設定上傳至 Azure。

一般來說，上傳您的設定不常進行，而且不需要在每個組建中執行。

## <a name="uploading-application-settings"></a>上傳應用程式設定

您可以透過 Azure 入口網站或 Azure CLI 來設定應用程式設定。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

Azure 入口網站提供的介面可用來建立、更新、刪除應用程式設定。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 在搜尋列中，搜尋並選取 [靜態 Web 應用程式]。

1. 按一下側邊欄中的 [設定] 選項。

1. 選取您想要套用應用程式設定的環境。 產生提取要求時，會自動建立預備環境，並會在合併提取要求之後，升級為實際執行環境。 您可以設定每個環境的應用程式設定。

1. 按一下 [新增] 按鈕新增應用程式設定。

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Azure Static Web Apps 的設定檢視":::

1. 輸入 [**名稱**] 和 [**值**]。

1. 按一下 [確定]。

1. 按一下 [檔案] 。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

您可以使用 `az rest` 命令，將您的設定大量上傳至 Azure。 此命令會接受應用程式設定，做為 `properties` 父屬性中的 JSON 物件。

若要建立具有適當值的 JSON 檔案，最簡單的方式是建立 local.settings.json 檔案的修改版本。

1. 為了確保具有機密資料的新檔案不會被公開，請將下列內容加入您的 .gitignore 檔案。

   ```bash
   local.settings*.json
   ```

2. 接下來，為您的 local.settings.json 檔案製作複本，並將其命名為 local.settings.properties.json。

3. 在新檔案中，移除應用程式設定以外的所有其他資料，並將 `Values` 重新命名為 `properties`。

   現在，您的檔案看起來應該會類似下列範例：

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Azure CLI 命令需要幾個您帳戶的專屬值才能執行上傳。 從您的靜態 Web 應用程式的資源 [概觀] 視窗中，您可以存取下列資訊：

1. 靜態網站名稱
2. 資源群組名稱
3. 訂用帳戶識別碼

:::image type="content" source="media/application-settings/overview.png" alt-text="Azure Static Web Apps 概觀":::

4. 從終端機或命令列，執行下列命令。 請務必將預留位置 `<YOUR_STATIC_SITE_NAME>`、`<YOUR_RESOURCE_GROUP_NAME>`、`<YOUR_SUBSCRIPTION_ID>` 取代為您在 [概觀] 視窗中的值。

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> "local.settings.properties.json" 檔案必須位於執行此命令的相同目錄中。 這個檔案可以命名為任何您喜歡的名稱。 名稱不重要。

### <a name="view-application-settings-with-the-azure-cli"></a>使用 Azure CLI 檢視應用程式設定

您可以透過 Azure CLI 來檢視應用程式設定。

- 從終端機或命令列，執行下列命令。 請務必以您的值取代預留位置 `<YOUR_SUBSCRIPTION_ID>`、`<YOUR_RESOURCE_GROUP_NAME>`、`<YOUR_STATIC_SITE_NAME>`。

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定本機開發](local-development.md)
