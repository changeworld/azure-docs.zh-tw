---
title: 教學課程：將 Hugo 網站發佈至 Azure 靜態 Web Apps
description: 了解如何將 Hugo 應用程式部署至 Azure 靜態 Web Apps。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: a0d1ba696b39b9331c4a85c9cf37d13d545ffad5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593694"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>教學課程：將 Hugo 網站發佈至 Azure 靜態 Web Apps 預覽版

本文示範如何建立 [Hugo](https://gohugo.io/) Web 應用程式，並將其部署至 [Azure 靜態 Web Apps](overview.md)。 最終會產生新的 Azure 靜態 Web Apps (具有相關聯的 GitHub 動作)，讓您能夠控制建置和發佈應用程式的方式。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 建立 Hugo 應用程式
> - 設定 Azure 靜態 Web Apps
> - 將 Hugo 應用程式部署至 Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有帳戶，可以[免費建立帳戶](https://azure.microsoft.com/free/)。
- GitHub 帳戶。 如果您沒有帳戶，可以[免費建立帳戶](https://github.com/join)。

## <a name="create-a-hugo-app"></a>建立 Hugo 應用程式

使用 Hugo 命令列介面 (CLI) 建立 Hugo 應用程式：

1. 在您的作業系統上依照 Hugo 的[安裝指南](https://gohugo.io/getting-started/installing/)操作。

1. 開啟終端機

1. 執行 Hugo CLI 以建立新的應用程式。

   ```bash
   hugo new site static-app
   ```

1. 瀏覽至新建立的應用程式。

   ```bash
   cd static-app
   ```

1. 將 Git 存放庫初始化。

   ```bash
    git init
   ```

1. 接著，將佈景主題安裝為 Git 子模組，然後在 Hugo 組態檔中加以指定，以將佈景主題新增至網站。

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. 認可變更。

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>將您的應用程式推送至 GitHub

您需要 GitHub 上的存放庫，才能連線至 Azure 靜態 Web Apps。 下列步驟說明如何為您的網站建立存放庫。

1. 從名為 **hugo-static-app** 的 [https://github.com/new](https://github.com/new) 建立空白的 GitHub 存放庫 (不要建立讀我檔案)。

1. 將 GitHub 存放庫新增至本機存放庫的遠端。 請務必新增您的 GitHub 使用者名稱，以取代下列命令中的 `<YOUR_USER_NAME>` 預留位置。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. 將您的本機存放庫推送至 GitHub。

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>部署 Web 應用程式

下列步驟說明如何建立新的靜態網站應用程式，並將其部署至生產環境。

### <a name="create-the-application"></a>建立應用程式

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [建立資源]
1. 搜尋 [靜態 Web Apps]
1. 按一下 [靜態 Web Apps (預覽)]
1. 按一下 [建立] 

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="在入口網站中建立 Azure 靜態 Web Apps 資源":::

1. 針對 [訂用帳戶]，請接受列出的訂用帳戶，或從下拉式清單中選取一個新的訂用帳戶。

1. 在 [資源群組] 中選取 [新增]。 在 [新增資源群組名稱] 中輸入 hugo-static-app，然後選取 [確定]。

1. 接著，在 [名稱] 方塊中，為您的應用程式提供全域唯一的名稱。 有效的字元包括 `a-z`、`A-Z`、`0-9` 和 `-`。 此值會作為您靜態應用程式的 URL 前置詞，格式為 `https://<APP_NAME>....`。

1. 在 [區域] 中，選取您附近的可用區域。

1. 針對 [SKU]，選取 [免費]。

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="已填寫的詳細資料":::

1. 按一下 [以 GitHub 登入] 按鈕。

1. 選取您的存放庫建立所在的**組織**。

1. 選取 **hugo-static-app** 作為 [存放庫]。

1. 針對 [分支]，選取 [主要]。

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="已完成的 GitHub 資訊":::

### <a name="build"></a>Build

接著，您將新增建置程序用來建置應用程式的組態設定。 下列設定會設定 GitHub 動作工作流程檔案。

1. 按 [下一步：組建 >] 按鈕，以編輯組建組態

1. 將 [應用程式位置] 設定為 **/** 。

1. 將 [應用程式成品位置] 設定為 [公用]。

   [API 位置] 的值並非必要值，因為您此時不會部署 API。

   :::image type="content" source="./media/publish-hugo/build-details.png" alt-text="組建設定":::

### <a name="review-and-create"></a>檢閱並建立

1. 按一下 [檢閱 + 建立] 按鈕，以確認詳細資料全部正確。

1. 按一下 [建立] 以開始建立 Azure 靜態 Web Apps，並佈建 GitHub 動作以進行部署。

1. 在部署完成後，瀏覽至您的終端機，並使用 GitHub 動作將認可提取至您的機器。

   ```bash
   git pull
   ```

1. 在文字編輯器中開啟 Hugo 應用程式，然後開啟 _.github/workflows/azure-pages-<WORKFLOW_NAME>.yml_ 檔案。

1. 將 `- uses: actions/checkout@v1` 一行 (第 18 行) 取代為下列程式碼，以建置 Hugo 應用程式。

   ```yml
   - uses: actions/checkout@v2
        with:
          submodules: true

   - name: Setup Hugo
     uses: peaceiris/actions-hugo@v2.4.8
     with:
       hugo-version: "latest"

   - name: Build
     run: hugo
   ```

1. 認可已更新的工作流程，並推送至 GitHub。

   ```bash
   git add -A
   git commit -m "Updating GitHub Actions workflow"
   git push
   ```

1. 等候 GitHub 動作完成。

1. 在 Azure 入口網站中，移至新建立的 Azure 靜態 Web Apps 資源的 [概觀] 視窗，然後按一下 [URL] 連結以開啟已部署的應用程式。

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="已部署的應用程式":::

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增自訂網域](custom-domain.md)
