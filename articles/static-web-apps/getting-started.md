---
title: 快速入門：使用 Azure Static Web Apps 建置您的第一個靜態 Web 應用程式
description: 了解如何建置 Azure Static Web Apps 網站。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: a78f74b4aadd26af141ed84ca99a092693f56af5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369557"
---
# <a name="quickstart-building-your-first-static-web-app"></a>快速入門：建置您的第一個靜態 Web 應用程式

Azure Static Web Apps 藉由從 GitHub 存放庫建置應用程式，將網站發佈至實際執行環境。 在本快速入門中，您會使用 Visual Studio Code 擴充功能將 Web 應用程式部署到 Azure Static Web Apps。

如果您沒有 Azure 訂用帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>必要條件

- [GitHub](https://github.com) 帳戶
- [Azure](https://portal.azure.com) 帳戶
- [Visual Studio Code](https://code.visualstudio.com)
- [適用於 Visual Studio Code 的 Azure Static Web Apps 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

接下來，開啟 Visual Studio Code 並移至 [檔案] > [開啟資料夾]，以在編輯器中開啟您剛才複製到電腦的存放庫。

## <a name="create-a-static-web-app"></a>建立靜態 Web 應用程式

1. 在 Visual Studio Code 內，選取活動列中的 [Azure] 標誌，以開啟 [Azure 擴充功能] 視窗。

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure 標誌":::

    > [!NOTE]
    > 需要 Azure 和 GitHub 登入。 如果您尚未從 Visual Studio Code 登入 Azure 和 GitHub，此擴充功能會在建立過程中提示您登入。

1. 將滑鼠游標放在 [Static Web Apps] 標籤上，然後選取 **加號**。

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="應用程式名稱":::

1. 命令選擇區會在編輯器的頂端開啟，並提示您為應用程式命名。

    輸入 **my-first-static-web-app**，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="建立靜態 Web 應用程式":::

1. 選取 [主要] 分支，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="分支名稱":::

1. 選取 **/** 作為應用程式程式碼的位置，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="應用程式程式碼位置":::

1. 擴充功能正在尋找應用程式中的 API 位置。 本文不會實作 API。

    選取 [暫時跳過]，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API 位置":::

1. 選取應用程式中針對生產環境建置檔案的位置。

    # <a name="no-framework"></a>[無架構](#tab/vanilla-javascript)

    清除此方塊，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="應用程式檔案路徑":::

    # <a name="angular"></a>[Angular](#tab/angular)

    輸入 **dist/angular-basic**，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Angular 應用程式檔案路徑":::

    # <a name="react"></a>[React](#tab/react)

    輸入 **build**，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="React 應用程式檔案路徑":::

    # <a name="vue"></a>[Vue](#tab/vue)

    輸入 **dist**，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Vue 應用程式檔案路徑":::

    ---

1. 選取最接近您的位置，然後按 **Enter**。

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="資源位置":::

1. 建立應用程式之後，Visual Studio Code 中就會顯示確認通知。

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="已建立確認":::

1. 在 [Visual Studio Code 總管] 視窗中，瀏覽至具有您的訂用帳戶名稱的節點，並將其展開。 請注意，部署可能需要數分鐘才能完成。 然後，返回 [Static Web Apps] 區段，並選取您的應用程式名稱，然後以滑鼠右鍵按一下 my-first-static-web-app，並選取 [在入口網站中開啟]，以在 Azure 入口網站中檢視應用程式。

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="開啟入口網站":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，您可以透過擴充功能刪除 Azure Static Web Apps 執行個體。

在 [Visual Studio Code 總管] 視窗中，返回 [Static Web Apps] 區段，然後以滑鼠右鍵按一下 [my-first-static-web-app]，然後選取 [刪除]。

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="刪除應用程式":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增 API](add-api.md)
