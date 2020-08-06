---
title: 教學課程：在 Azure 靜態 Web Apps 上部署伺服器轉譯的 Nuxt.js 網站
description: 使用 Azure 靜態 Web Apps 產生及部署 Nuxt.js 動態網站。
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-javascript
ms.openlocfilehash: 9c7e03f5e658b8e15dcae1c5314b73dfbfdf0206
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430292"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>在 Azure 靜態 Web Apps 預覽版上部署伺服器轉譯的 Nuxt.js 網站

在本教學課程中，您將了解如何將 [Nuxt.js](https://nuxtjs.org) 產生的靜態網站部署至 [Azure 靜態 Web Apps](overview.md)。 首先，您應了解如何安裝、設定及部署 Nuxt.js 應用程式。 在此過程中，您也將了解如何解決使用 Nuxt.js 產生靜態頁面時常會出現的難題

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。
- GitHub 帳戶。 [免費建立帳戶](https://github.com/join)。
- 已安裝 [Node.js](https://nodejs.org)。

## <a name="set-up-a-nuxtjs-app"></a>設定 Nuxt.js 應用程式

您可以使用 `create-nuxt-app` 設定新的 Nuxt.js 專案。 在本教學課程中，您會藉由複製現有的存放庫來展開作業，而不使用新專案。 此存放庫經過設定，會示範如何將動態 Nuxt.js 應用程式部署為靜態網站。

1. 透過範本存放庫，在您的 GitHub 帳戶下建立新的存放庫。
1. 巡覽到 <http://github.com/staticwebdev/nuxtjs-starter/generate>
1. 將存放庫命名為 **nuxtjs-starter**
1. 然後，將新的存放庫複製到您的機器。 請務必將 <YOUR_GITHUB_ACCOUNT_NAME> 取代為您的帳戶名稱。

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. 瀏覽至新複製的 Nuxt.js 應用程式：

   ```bash
   cd nuxtjs-starter
   ```

1. 安裝相依性：

    ```bash
    npm install
    ```

1. 在開發環境中啟動 Nuxt.js 應用程式：

    ```bash
    npm run dev
    ```

瀏覽至 <http://localhost:3000> 以開啟應用程式，在此您應該會看到下列網站在您慣用的瀏覽器中開啟：

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="啟動 Nuxt.js 應用程式":::

當您按一下架構/程式庫時，您應該會看到關於所選項目的詳細資料頁面：

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="詳細資料頁面":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>從 Nuxt.js 組建產生靜態網站

當您使用 `npm run build` 建置 Nuxt.js 網站時，應用程式會建置為傳統 Web 應用程式，而不是靜態網站。 若要產生靜態網站，請使用下列應用程式組態。

1. 使用 `nuxt generate` 命令，將 _package.json_ 的建置指令碼更新為僅產生靜態網站：

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    在此命令就緒後，每當您推送認可時，靜態 Web Apps 就會執行 `build` 指令碼。

1. 建立靜態網站：

    ```bash
    npm run build
    ```

    Nuxt.js 會產生靜態網站，並將其複製到工作目錄根目錄的 _dist_ 資料夾中。

    > [!NOTE]
    > 此資料夾會列在 _.gitignore_ 檔案中，因為當您進行部署時，應會由 CI/CD 加以產生。

## <a name="push-your-static-website-to-github"></a>將您的靜態網站推送至 GitHub

Azure 靜態 Web Apps 會從 GitHub 存放庫部署您的應用程式，並針對每個推送至指定分支的認可持續執行此操作。 使用下列命令，將您的變更同步至 GitHub。

1. 暫存所有已變更的檔案：

    ```bash
    git add .
    ```

1. 認可所有變更

    ```bash
    git commit -m "Update build config"
    ```

1. 將您的變更推送至 GitHub。

    ```bash
    git push origin master
    ```

## <a name="deploy-your-static-website"></a>部署您的靜態網站

下列步驟說明如何將您剛剛推送至 GitHub 的應用程式連結至 Azure 靜態 Web Apps。 在 Azure 中，您可以將應用程式部署至生產環境。

### <a name="create-an-azure-static-web-apps-preview-resource"></a>建立 Azure 靜態 Web Apps 預覽資源

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [建立資源]
1. 搜尋 [靜態 Web Apps]
1. 按一下 [靜態 Web Apps (預覽)]
1. 按一下 [建立] 

1. 從 [訂用帳戶] 下拉式清單中選取訂用帳戶，或使用預設值。
1. 按一下 [資源群組] 下拉式清單下方的 [新增] 連結。 在 [新增資源群組名稱] 中輸入 **mystaticsite**，然後按一下 [確定]
1. 在 [名稱] 文字方塊中，為您的應用程式提供全域唯一的名稱。 有效的字元包括 `a-z`、`A-Z`、`0-9` 和 `-`。 此值會作為您靜態應用程式的 URL 前置詞，格式為 `https://<APP_NAME>.azurestaticapps.net`。
1. 在 [區域] 下拉式清單中，選擇離您最近您的區域。
1. 從 [SKU] 下拉式清單中選取 [免費]。

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="建立靜態 Web 應用程式":::

### <a name="add-a-github-repository"></a>新增 GitHub 存放庫

新的靜態 Web Apps 帳戶必須能使用您的 Nuxt.js 應用程式存取存放庫，以便自動部署認可。

1. 按一下 [以 GitHub 登入] 按鈕
1. 選取您用來為 Nuxt.js 專案建立存放庫的**組織**，這可能是您的 GitHub 使用者名稱。
1. 尋找並選取您先前建立的存放庫名稱。
1. 從 [分支] 下拉式清單中選擇 [主要] 作為分支。

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="連接 GitHub":::

### <a name="configure-the-build-process"></a>設定建置程序

建置 Azure 靜態 Web Apps 是為了自動執行一般工作，例如安裝 npm 模組，以及在每個部署期間執行 `npm run build`。 不過，有某些設定必須要手動設定，例如應用程式來源資料夾和組建目的地資料夾。

1. 按一下 [建置] 索引標籤，以設定靜態輸出檔案夾。

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="建置索引標籤":::

1. 在 [應用程式成品位置] 文字方塊中輸入 **dist**。

### <a name="review-and-create"></a>檢閱並建立

1. 按一下 [檢閱 + 建立] 按鈕，以確認詳細資料全部正確。
1. 按一下 [建立] 以開始建立資源，並佈建 GitHub 動作以進行部署。
1. 在部署完成後，按一下 [移至資源]
1. 在 [概觀] 視窗上，按一下 [URL] 連結以開啟您已部署的應用程式。 

如果網站未立即載入，表示背景 GitHub 動作工作流程仍在執行中。 在工作流程完成之後，您即可按一下 [重新整理瀏覽器] 以檢視您的 Web 應用程式。

您可以瀏覽至存放庫的 [動作]，以查看動作工作流程的狀態：

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>同步變更

當您建立應用程式時，Azure 靜態 Web Apps 會在您的存放庫中建立 GitHub 動作工作流程檔案。 您必須將此檔案移至本機存放庫，以同步您的 Git 歷程記錄。

返回終端機，然後執行下列命令 `git pull origin master`。

## <a name="configure-dynamic-routes"></a>設定動態路由

瀏覽至新部署的網站，然後按一下其中一個架構或程式庫標誌。 您會收到 404 錯誤頁面，而不是取得詳細資料頁面。

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="動態路由上的 404":::

其原因在於，Nuxt.js 雖然產生了靜態網站，但僅限於首頁的部分。 Nuxt.js 可為每個 `.vue` 分頁檔產生對等的靜態 `.html` 檔案，但有例外狀況。 

如果頁面是動態頁面 (例如 `_id.vue`)，則沒有足夠的資訊可從這類動態頁面產生靜態 HTML。 您必須明確提供動態路由的可能路徑。

## <a name="generate-static-pages-from-dynamic-routes"></a>從動態路由產生靜態頁面

1. 更新 _nuxt.config.js_ 檔案，讓 Nuxt.js 使用所有可用資料的清單來產生每個架構/程式庫的靜態頁面：

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` 是非同步函式，因此您可以在此函式中對 API 提出要求，並使用傳回的清單產生路徑。

2. 將新的變更推送至您的 GitHub 存放庫，並等候幾分鐘的時間，讓 GitHub 動作重新建置您的網站。 建置完成後，404 錯誤就會消失。

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="動態路由上的 404 已修正":::

> [!div class="nextstepaction"]
> [設定自訂網域](custom-domain.md)
