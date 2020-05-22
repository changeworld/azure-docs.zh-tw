---
title: 教學課程：將 VuePress 網站發佈至 Azure 靜態 Web Apps
description: 本教學課程說明如何將 VuePress 應用程式部署至 Azure 靜態 Web Apps。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: bd9eaad0c141eda815da159e3c13d6c51f5e6200
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593595"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>教學課程：將 VuePress 網站發佈至 Azure 靜態 Web Apps 預覽版

本文示範如何建立 [VuePress](https://vuepress.vuejs.org/) Web 應用程式，並將其部署至 [Azure 靜態 Web Apps](overview.md)。 最終會產生新的 Azure 靜態 Web Apps 應用程式 (具有相關聯的 GitHub 動作)，讓您能夠控制建置和發佈應用程式的方式。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 建立 VuePress 應用程式
> - 設定 Azure 靜態 Web Apps
> - 將 VuePress 應用程式部署至 Azure

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有帳戶，可以[免費建立帳戶](https://azure.microsoft.com/free/)。
- GitHub 帳戶。 如果您沒有帳戶，可以[免費建立帳戶](https://github.com/join)。
- 已安裝 [Node.js](https://nodejs.org)。

## <a name="create-a-vuepress-app"></a>建立 VuePress 應用程式

從命令列介面 (CLI) 建立 VuePress 應用程式：

1. 為 VuePress 應用程式建立新的資料夾。

   ```bash
   mkdir static-site
   ```

1. 將 _README.md_ 檔案新增至此資料夾。

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. 初始化 _package.json_ 檔案。

   ```bash
   npm init -y
   ```

1. 新增 VuePress 作為 `devDependency`。

   ```bash
   npm install --save-dev vuepress
   ```

1. 在文字編輯器中開啟 _package.json_ 檔案，然後將建置命令新增至 [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) 區段。

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. 建立 _.gitignore_ 檔案，以排除 _node\_modules_ 資料夾。

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. 將 Git 存放庫初始化。

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>將您的應用程式推送至 GitHub

您需要 GitHub 上的存放庫，才能連線至 Azure 靜態 Web Apps。 下列步驟說明如何為您的網站建立存放庫。

1. 從名為 **vuepress-static-app** 的 [https://github.com/new](https://github.com/new) 建立空白的 GitHub 存放庫 (不要建立讀我檔案)。

1. 將 GitHub 存放庫新增至本機存放庫的遠端。 請務必新增您的 GitHub 使用者名稱，以取代下列命令中的 `<YOUR_USER_NAME>` 預留位置。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. 將您的本機存放庫推送至 GitHub。

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>部署 Web 應用程式

下列步驟說明如何建立新的靜態 Web Apps 應用程式，並將其部署至生產環境。

### <a name="create-the-application"></a>建立應用程式

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [建立資源]
1. 搜尋 [靜態 Web Apps]
1. 按一下 [靜態 Web Apps (預覽)]
1. 按一下 [建立] 

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="在入口網站中建立靜態 Web Apps (預覽)":::

1. 針對 [訂用帳戶]，請接受列出的訂用帳戶，或從下拉式清單中選取一個新的訂用帳戶。

1. 在 [資源群組] 中選取 [新增]。 在 [新增資源群組名稱] 中輸入 vuepress-static-app，然後選取 [確定]。

1. 接著，在 [名稱] 方塊中，為您的應用程式提供全域唯一的名稱。 有效的字元包括 `a-z`、`A-Z`、`0-9` 和 `-`。 此值會作為您靜態應用程式的 URL 前置詞，格式為 `https://<APP_NAME>.azurestaticapps.net`。

1. 在 [區域] 中，選取您附近的可用區域。

1. 針對 [SKU]，選取 [免費]。

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="已填寫的詳細資料":::

1. 按一下 [以 GitHub 登入] 按鈕。

1. 選取您的存放庫建立所在的**組織**。

1. 選取 **vuepress-static-app** 作為 [存放庫]。

1. 針對 [分支]，選取 [主要]。

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="已完成的 GitHub 資訊":::

### <a name="build"></a>Build

接著，您將新增建置程序用來建置應用程式的組態設定。 下列設定會設定 GitHub 動作工作流程檔案。

1. 按 [下一步：組建 >] 按鈕，以編輯組建組態

1. 將 [應用程式位置] 設定為 **/** 。

1. 將 [應用程式成品位置] 設定為 [.vuepress/dist]。

[API 位置] 的值並非必要值，因為您此時不會部署 API。

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="組建設定":::

### <a name="review-and-create"></a>檢閱並建立

1. 按一下 [檢閱 + 建立] 按鈕，以確認詳細資料全部正確。

1. 按一下 [建立] 以開始建立 Azure 靜態 Web Apps，並佈建 GitHub 動作以進行部署。

1. 在部署完成時，按一下 [前往資源]。

1. 在 [資源] 畫面上，按一下 [URL] 連結，以開啟已部署的應用程式。 您可能需要等候一或兩分鐘的時間，GitHub 動作才會完成。

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="已部署的應用程式":::

### <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增自訂網域](custom-domain.md)
