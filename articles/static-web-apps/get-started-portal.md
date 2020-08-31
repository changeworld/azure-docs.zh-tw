---
title: 快速入門：透過 Azure 入口網站使用 Azure Static Web Apps 建置您的第一個靜態 Web 應用程式
description: 了解如何使用 Azure 入口網站來建立 Azure Static Web Apps 執行個體。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: e0b78c5e053c5668fbebd8ebaac91a90aa2b364f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752792"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立您的第一個靜態 Web 應用程式

Azure Static Web Apps 藉由從 GitHub 存放庫建置應用程式，將網站發佈至實際執行環境。 在本快速入門中，您會使用入口網站將 Web 應用程式部署到 Azure 靜態 Web 應用程式。

如果您沒有 Azure 訂用帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>Prerequisites

- [GitHub](https://github.com) 帳戶
- [Azure](https://portal.azure.com) 帳戶

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>建立靜態 Web 應用程式

現在已建立存放庫，您可以從 Azure 入口網站建立靜態 Web 應用程式。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 選取 [建立資源]
1. 搜尋**靜態 Web 應用程式**
1. 選取 [Static Web Apps (預覽)]
1. 選取 [建立] 

在 [基本資料] 索引標籤中，先設定您的新應用程式，並將其連結至 GitHub 存放庫。

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="[基本] 索引標籤":::

1. 選取您的 _Azure 訂用帳戶_
1. 選取或建立新的 [資源群組]
1. 將應用程式命名為 **my-first-static-web-app**。
      1. 有效字元為 `a-z` (區分大小寫)、`0-9` 以及 `-`。
1. 選取最靠近您的 [區域]
1. 選取 [免費] _SKU_
1. 選取 [使用 GitHub 登入] 按鈕，並向 GitHub 進行驗證

當您使用 GitHub 登入後，請輸入存放庫資訊。

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="存放庫詳細資料":::

1. 選取您慣用的 [組織]
1. 從 [存放庫] 下拉式清單中選取 **my-first-web-static-app**
1. 從 [分支] 下拉式選單中，選取 [主要]
1. 選取頁面底部的 [下一步:組建 >] 按鈕，以編輯組建組態

:::image type="content" source="media/getting-started-portal/next-build-button.png" alt-text="下一個組建按鈕":::

> [!NOTE]
> 如果您沒有看到任何存放庫，可能需要在 GitHub 中授權 Azure Static Web Apps。 瀏覽至您的 GitHub 存放庫，並移至 [設定] > [應用程式] > [授權的 OAuth 應用程式]、選取 [Azure Static Web Apps]，然後選取 [授與]。 針對組織存放庫，您必須是組織的擁有者，才能授與權限。

1. 在 [建置] 索引標籤中，新增您慣用前端架構的特定設定詳細資料。

    # <a name="no-framework"></a>[無架構](#tab/vanilla-javascript)

    - 清除 [應用程式位置] 方塊中的預設值
    - 清除 [API 位置] 方塊中的預設值
    - 清除 [應用程式成品位置] 方塊中的預設值

    # <a name="angular"></a>[Angular](#tab/angular)

    - 清除 [應用程式位置] 方塊中的預設值
    - 清除 [API 位置] 方塊中的預設值
    - 在 [應用程式成品位置] 方塊中輸入 **dist/angular-basic**

    # <a name="react"></a>[React](#tab/react)

    - 清除 [應用程式位置] 方塊的預設值
    - 清除 [API 位置] 方塊中的預設值
    - 在 [應用程式成品位置] 方塊中輸入 **build**

    # <a name="vue"></a>[Vue](#tab/vue)

    - 清除 [應用程式位置] 方塊的預設值
    - 清除 [API 位置] 方塊中的預設值
    - 在 [應用程式成品位置] 方塊中輸入 **dist**

    ---

1. 選取 [檢閱 + 建立]。

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="檢閱建立按鈕":::

    > [!NOTE]
    > 若要在建立應用程式之後變更這些值，您可以編輯[工作流程檔案](github-actions-workflow.md)。

1. 選取 [建立]  。

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="建立按鈕":::

1. 選取 [前往資源]  。

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="移至資源按鈕":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，您可以透過下列步驟刪除 Azure Static Web Apps 執行個體：

1. 開啟 [Azure 入口網站](https://portal.azure.com)
1. 從頂端的搜尋列搜尋 **my-first-web-static-app**
1. 選取應用程式名稱
1. 選取 [刪除] 按鈕
1. 選取 [是] 以確認刪除動作

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增 API](add-api.md)
