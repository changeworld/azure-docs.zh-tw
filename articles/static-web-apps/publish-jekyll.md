---
title: 教學課程：將 Jekyll 網站發佈至 Azure Static Web Apps
description: 了解如何將 Jekyll 應用程式部署至 Azure Static Web Apps。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: e3bad12362358620d0f2dc105bb2820dfb691d00
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000210"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>教學課程：將 Jekyll 網站發佈至 Azure Static Web Apps Preview

本文示範如何建立 [Jekyll](https://jekyllrb.com/) Web 應用程式，並將其部署至 [Azure Static Web Apps](overview.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 建立 Jekyll 網站
> - 設定 Azure 靜態 Web Apps
> - 將 Jekyll 應用程式部署至 Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

- 安裝 [Jekyll](https://jekyllrb.com/docs/installation/)
  - 如有必要，您可以使用 Windows 子系統 Linux 版，並遵循 Ubuntu 指示。
- 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有帳戶，可以[免費建立帳戶](https://azure.microsoft.com/free/)。
- GitHub 帳戶。 如果您沒有帳戶，可以[免費建立帳戶](https://github.com/join)。

## <a name="create-jekyll-app"></a>建立 Jekyll 應用程式

使用 Jekyll 命令列介面 (CLI) 建立 Jekyll 應用程式：

1. 從終端執行 Jekyll CLI，以建立新的應用程式。

   ```bash
   jekyll new static-app
   ```

1. 瀏覽至新建立的應用程式。

   ```bash
   cd static-app
   ```

1. 初始化新的 Git 存放庫。

   ```bash
    git init
   ```

1. 認可變更。

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>將您的應用程式推送至 GitHub

Azure Static Web Apps 會使用 GitHub 來發佈您的網站。 下列步驟說明如何建立 GitHub 存放庫。

1. 透過 [https://github.com/new](https://github.com/new) 建立空白的 GitHub 存放庫 (不要建立讀我檔案)，並命名為 **jekyll-azure-static**。

1. 將 GitHub 存放庫新增至本機存放庫的遠端。 請務必新增您的 GitHub 使用者名稱，以取代下列命令中的 `<YOUR_USER_NAME>` 預留位置。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. 將您的本機存放庫推送至 GitHub。

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>部署 Web 應用程式

下列步驟說明如何建立新的靜態網站應用程式，並將其部署至生產環境。

### <a name="create-the-application"></a>建立應用程式

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 按一下 [建立資源]。

1. 搜尋 **Static Web Apps**。

1. 按一下 [Static Web Apps (預覽)]。

1. 按一下 [建立]。

1. 針對 [訂用帳戶]，請接受列出的訂用帳戶，或從下拉式清單中選取一個新的訂用帳戶。

1. 在 [資源群組] 中選取 [新增]。 在「新增資源群組名稱」 中輸入 **jekyll-static-app**，然後選取 [確定]。

1. 接下來，在「名稱」方塊中提供應用程式名稱。 有效的字元包括 `a-z`、`A-Z`、`0-9` 和 `-`。

1. 在 [區域] 中，選取您附近的可用區域。

1. 針對 [SKU]，選取 [免費]。

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="已填寫的詳細資料":::

1. 按一下 [以 GitHub 登入] 按鈕。

1. 選取您的存放庫建立所在的**組織**。

1. 選取 [jekyll-static-app] 作為「存放庫」。

1. 針對 [分支]，選取 [主要]。

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="已填寫的詳細資料":::

### <a name="build"></a>Build

接著，您將新增建置程序用來建置應用程式的組態設定。 下列設定會設定 GitHub 動作工作流程檔案。

1. 按 [下一步：**組建 >]** 按鈕，以編輯組建設定。

1. 將「應用程式位置」 設定為 **/_site**。

1. 將 [應用程式成品位置] 保留為空白。

   [API 位置] 的值並非必要值，因為您此時不會部署 API。

### <a name="review-and-create"></a>檢閱並建立

1. 按一下 [檢閱 + 建立] 按鈕，以確認詳細資料全部正確。

1. 按一下 [建立] 以開始建立 Azure 靜態 Web Apps，並佈建 GitHub 動作以進行部署。

1. 部署起初會失敗，因為工作流程檔案需要一些 Jekyll 特有設定。 若要新增這些設定，請瀏覽至您的終端機，並使用 GitHub Action 將認可提取至您的機器。

   ```bash
   git pull
   ```

1. 在文字編輯器中開啟 Jekyll 應用程式，然後開啟 _.github/workflows/azure-pages-<WORKFLOW_NAME>.yml_ 檔案。

1. 在 `- uses: actions/checkout@v2` 這一行後面新增下列設定區塊。

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@ec106b438a1ff6ff109590de34ddc62c540232e0
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. 認可已更新的工作流程，並推送至 GitHub。

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. 等候 GitHub 動作完成。

1. 在 Azure 入口網站的「概觀」 視窗上，按一下 [URL] 連結以開啟您已部署的應用程式。

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="已填寫的詳細資料":::

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增自訂網域](custom-domain.md)
