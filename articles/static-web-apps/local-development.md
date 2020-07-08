---
title: 設定 Azure 靜態 Web Apps 的本機開發
description: 了解如何為 Azure 靜態 Web Apps 設定本機開發環境
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: a7215790a7f052227b08f51dcd7ad5dd337bb4e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84259264"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>設定 Azure 靜態 Web Apps 預覽版的本機開發

Azure 靜態 Web Apps 執行個體由兩種不同類型的應用程式所組成。 第一種是適用於靜態內容的 Web 應用程式。 Web 應用程式通常會使用前端架構和程式庫建立，或使用靜態網站產生器來建立。 第二個層面是 API，屬於 Azure Functions 應用程式，可提供豐富的後端開發環境。

在雲端中執行時，Azure 靜態 Web Apps 可順暢地將要求對應至從 Web 應用程式到 Azure Functions 應用程式的 `api` 路由，而無需進行 CORS 設定。 在本機上，您必須設定應用程式以模擬此行為。

本文示範針對本機開發而建議的最佳做法，包括下列概念：

- 設定 Web 應用程式的靜態內容
- 為應用程式的 API 設定 Azure Functions 應用程式
- 偵錯和執行應用程式
- 應用程式的檔案和資料夾結構的最佳做法

## <a name="prerequisites"></a>Prerequisites

- [Visual Studio Code](https://code.visualstudio.com/)
- 適用於 Visual Studio Code 的 [Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- 適用於 Visual Studio Code 的[即時伺服器擴充功能](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
  - 只有您未使用前端 JavaScript 架構或靜態網站產生器的 CLI 時，才有此必要

## <a name="run-projects-locally"></a>在本機執行專案

在本機執行 Azure 靜態 Web 應用程式牽涉到三個程序，具體上取決於您的專案是否包含 API。

- 執行本機網頁伺服器
- 執行 API
- 將 Web 專案連線至 API

視網站的建置方式而定，本機網頁伺服器不一定需要在瀏覽器中執行應用程式。 使用前端 JavaScript 架構和靜態網站產生器時，此功能會內建於其各自的 CLI (命令列介面) 中。 下列連結指向選定架構、程式庫和產生器的 CLI 參考。

### <a name="javascript-frameworks-and-libraries"></a>JavaScript 架構和程式庫

- [Angular CLI](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [React CLI](https://create-react-app.dev/)

### <a name="static-site-generators"></a>靜態網站產生器

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

如果您使用 CLI 工具為網站提供服務，您可以跳至[執行 API](#run-api-locally) 一節。

### <a name="running-a-local-web-server-with-live-server"></a>透過即時伺服器執行本機網頁伺服器

適用於 Visual Studio Code 的即時伺服器擴充功能提供了本機開發網頁伺服器，可提供靜態內容。

#### <a name="create-a-repository"></a>建立存放庫

1. 請確定您已登入 GitHub，並 [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) 使用此範本，流覽至並建立名為**vanilla 的**新 GitHub 專案。

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="GitHub 新增存放庫視窗":::

1. 開啟 Visual Studio Code。

1. 按 **F1** 以開啟命令選擇區。

1. 在搜尋方塊中輸入**複製**，然後選取 [Git：複製]。

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="Visual Studio Code 中的 Git 複製選項":::

1. 針對 [存放庫 URL] 輸入下列值。

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. 選取新專案的資料夾位置。

1. 當系統提示您開啟複製的存放庫時，請選取 [開啟]。

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="在新視窗中開啟":::

Visual Studio Code 會在編輯器中開啟複製的專案。

### <a name="run-the-website-locally-with-live-server"></a>使用即時伺服器在本機執行網站

1. 按 **F1** 以開啟命令選擇區。

1. 在搜尋方塊中輸入**即時伺服器**，然後選取 [即時伺服器：開啟即時伺服器]

    [瀏覽器] 索引標籤隨即開啟，以顯示應用程式。

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="在瀏覽器中執行的簡易靜態網站":::

    此應用程式會對 `api/message` 端點提出 HTTP 要求。 目前該要求失敗，因為必須啟動此應用程式的 API 部分。

### <a name="run-api-locally"></a>在本機執行 API

Azure 靜態 Web Apps API 由 Azure Functions 提供技術支援。 如需關於將 API 新增至 Azure 靜態 Web Apps 專案的詳細資訊，請參閱[使用 Azure Functions 將 API 新增至 Azure 靜態 Web Apps](add-api.md)。

在 API 建立程序中，會為 Visual Studio Code 建立啟動組態。 此組態位於 _.vscode_ 資料夾中。 此資料夾包含在本機建置和執行 API 的所有必要設定。

1. 在 Visual Studio Code 中，按 **F5** 以啟動 API。

1. 新的終端機執行個體隨即開啟，並顯示 API 建置程序的輸出。

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="在 Visual Studio Code 終端機中執行的 API":::

   Visual Studio Code 中的狀態列現在為橙色。 這個顏色表示 API 正在執行中，且已附加偵錯工具。

1. 接著，按 **Ctrl/Cmd**，然後按一下終端機中的 URL，以開啟呼叫 API 的瀏覽器視窗。

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="瀏覽器顯示 API 呼叫的結果":::

### <a name="debugging-the-api"></a>對 API 進行偵錯

1. 在 Visual Studio Code 中開啟 _api/GetMessage/index.js_ 檔案。

1. 按一下第 2 行的左邊界以設定中斷點。 出現紅點時，表示中斷點已設定。

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Visual Studio Code 中的中斷點":::

1. 在瀏覽器中，重新整理在 <http://127.0.0.1:7071/api/message> 執行的頁面。

1. 在 Visual Studio Code 中達到中斷點時，程式會暫停執行。

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="在 Visual Studio Code 中達到中斷點":::

   [Visual Studio Code 中針對您的 API 提供了完整的偵錯體驗](https://code.visualstudio.com/Docs/editor/debugging)。

1. 按偵錯列中的 [繼續] 按鈕以繼續執行。

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Visual Studio Code 中的 [繼續] 按鈕":::

### <a name="calling-the-api-from-the-application"></a>從應用程式呼叫 API

部署後，Azure 靜態 Web Apps 會自動將這些要求對應至 _api_ 資料夾中的端點。 此對應可確保從應用程式到 API 的要求看起來如下列範例所示。

```javascript
let response = await fetch("/api/message");
```

在本機執行應用程式時，有兩種方式可以設定 `api` 路由的路徑，端視您的應用程式是否是以 JavaScript 架構 CLI 建置而定。

- 環境組態檔 (建議用於 JavaScript 架構和程式庫)
- 本機 Proxy

### <a name="environment-configuration-files"></a>環境組態檔

如果您要使用具有 CLI 的前端架構來建置應用程式，則應使用環境組態檔。 每個架構或程式庫分別會以不同的方式來處理這些環境組態檔。 在應用程式執行於本機時使用一個用於開發的組態檔，並且在應用程式執行於生產環境時使用一個用於生產環境的組態檔，是很常見的做法。 您所使用的 JavaScript 架構或靜態網站產生器的 CLI，會自動得知應在本機使用開發檔案，並且在 Azure 靜態 Web Apps 建置應用程式時使用生產檔案。

在開發組態檔中，您可以指定 API 的路徑，指向您網站的 API 在本機執行的本機位置 `http:127.0.0.1:7071`。

```
API=http:127.0.0.1:7071/api
```

在生產環境組態檔中，請將 API 的路徑指定為 `api`。 如此，在生產環境中執行時，您的應用程式將會透過 "yoursite.com/api" 來呼叫 API。

```
API=api
```

這些組態值可以當做 Web 應用程式 JavaScript 中的節點環境變數來參考。

```js
let response = await fetch(`${process.env.API}/message`);
```

當 CLI 用來在開發模式中執行您的網站，或用來建置生產環境的網站時，`process.env.API` 值會取代為適當組態檔中的值。

如需關於為前端 JavaScript 架構和程式庫設定環境檔案的詳細資訊，請參閱下列文章：

- [Angular 環境變數](https://angular.io/guide/build#configuring-application-environments)
- [回應 - 新增自訂環境變數](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue - 模式和環境變數](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>重新啟動即時伺服器

1. 在 Visual Studio Code 中按 **F1**，以開啟命令選擇區。

1. 輸入**即時伺服器**，然後選取 [即時伺服器：停止即時伺服器]。

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Visual Studio 命令選擇區中的停止即時伺服器命令":::

1. 按 **F1** 以開啟命令選擇區。

1. 輸入**即時伺服器**，然後選取 [即時伺服器：**開啟即時伺服器]** ，以啟動即時伺服器。

1. 重新整理在 `http://locahost:3000` 執行的應用程式。 瀏覽器現在會顯示從 API 傳回的訊息。

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="瀏覽器中顯示的 Hello from API":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定應用程式設定](application-settings.md)
