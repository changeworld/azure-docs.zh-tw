---
title: 教學課程：將 Gatsby 網站發佈至 Azure 靜態 Web Apps
description: 本教學課程說明如何將 Gatsby 應用程式部署至 Azure 靜態 Web Apps。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: e24a2a19eb39e2c6c7612631ad98f95cb4c5b9ef
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250159"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>教學課程：將 Gatsby 網站發佈至 Azure 靜態 Web Apps 預覽版

本文示範如何建立 [Gatsby](https://gatsbyjs.org) Web 應用程式，並將其部署至 [Azure 靜態 Web Apps](overview.md)。 最終會產生新的靜態 Web Apps 網站 (具有相關聯的 GitHub 動作)，讓您能夠控制建置和發佈應用程式的方式。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 建立 Gatsby 應用程式
> - 設定 Azure 靜態 Web Apps 網站
> - 將 Gatsby 應用程式部署至 Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有帳戶，可以[免費建立帳戶](https://azure.microsoft.com/free/)。
- GitHub 帳戶。 如果您沒有帳戶，可以[免費建立帳戶](https://github.com/join)。
- 已安裝 [Node.js](https://nodejs.org)。

## <a name="create-a-gatsby-app"></a>建立 Gatsby 應用程式

使用 Gatsby 命令列介面 (CLI) 建立 Gatsby 應用程式：

1. 開啟終端機
1. 使用 [npx](https://www.npmjs.com/package/npx) 工具，透過 Gatsby CLI 建立新的應用程式。 這可能需要幾分鐘的時間。

   ```bash
   npx gatsby new static-web-app
   ```

1. 瀏覽至新建立的應用程式

   ```bash
   cd static-web-app
   ```

1. 初始化 Git 存放庫

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>將您的應用程式推送至 GitHub

您需要有 GitHub 上的存放庫，才能建立新的 Azure 靜態 Web Apps 資源。

1. 從名為 **gatsby-static-web-app** 的 [https://github.com/new](https://github.com/new) 建立空白的 GitHub 存放庫 (不要建立讀我檔案)。

1. 接著，將您剛剛建立的 GitHub 存放庫新增至本機存放庫的遠端。 請務必新增您的 GitHub 使用者名稱，以取代下列命令中的 `<YOUR_USER_NAME>` 預留位置。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
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
1. 搜尋 [Static Web Apps]
1. 按一下 [Static Web Apps (預覽)]
1. 按一下 [建立] 

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="在入口網站中建立靜態 Web Apps (預覽)":::

1. 針對 [訂用帳戶]，請接受列出的訂用帳戶，或從下拉式清單中選取一個新的訂用帳戶。

1. 在 [資源群組] 中選取 [新增]。 在 [新增資源群組名稱] 中輸入 gatsby-static-web-app，然後選取 [確定]。

1. 接下來，在 [名稱] 方塊中提供應用程式的名稱。 有效的字元包括 `a-z`、`A-Z`、`0-9` 和 `-`。

1. 在 [區域] 中，選取您附近的可用區域。

1. 針對 [SKU]，選取 [免費]。

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="在入口網站中建立靜態 Web Apps (預覽)":::

1. 按一下 [以 GitHub 登入] 按鈕。

1. 選取您的存放庫建立所在的**組織**。

1. 選取 **gatsby-static-web-app** 作為 [存放庫]。

1. 針對 [分支]，選取 [主要]。

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="在入口網站中建立靜態 Web Apps (預覽)":::

### <a name="build"></a>Build

接著，新增建置程序用來建置應用程式的組態設定。

1. 按 [下一步：組建 >] 按鈕，以編輯組建組態

1. 若要在 GitHub 動作中設定步驟的設定，請將 [應用程式位置] 設定為 **/** 。

1. 將 [應用程式成品位置] 設定為 [公用]。

   [API 位置] 的值並非必要值，因為您此時不會部署 API。

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="在入口網站中建立靜態 Web Apps (預覽)":::

### <a name="review-and-create"></a>檢閱並建立

1. 按一下 [檢閱 + 建立] 按鈕，以確認詳細資料全部正確。

1. 按一下 [建立] 以開始建立 App Service Web Apps，並佈建 GitHub 動作以進行部署。

1. 在部署完成時，按一下 [前往資源]。

1. 在 [資源] 畫面上，按一下 [URL] 連結，以開啟已部署的應用程式。 您可能需要等候一或兩分鐘的時間，GitHub 動作才會完成。

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="在入口網站中建立靜態 Web Apps (預覽)":::

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增自訂網域](custom-domain.md)
