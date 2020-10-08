---
title: 教學課程：使用 Blazor 在 Azure Static Web Apps 中建置靜態 Web 應用程式
description: 了解如何使用 Blazor 建置 Azure Static Web Apps 網站。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 60e62228e33d2d86bb407e45802f5c0621a94049
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761085"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>教學課程：使用 Blazor 在 Azure Static Web Apps 中建置靜態 Web 應用程式

Azure Static Web Apps 藉由從 GitHub 存放庫建置應用程式，將網站發佈至實際執行環境。 在本教學課程中，您會使用 Azure 入口網站將 Web 應用程式部署到 Azure 靜態 Web 應用程式。

如果您沒有 Azure 訂用帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>Prerequisites

- [GitHub](https://github.com) 帳戶
- [Azure](https://portal.azure.com) 帳戶

## <a name="application-overview"></a>應用程式概觀

Azure Static Web Apps 可讓您建立無伺服器後端支援的靜態 Web 應用程式。 下列教學課程示範如何部署會傳回氣象資料的 Blazor Web 應用程式。

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="完整的 Blazor 應用程式":::

本教學課程中精選的應用程式是由三個不同的 Visual Studio 專案所組成：

- **API**：C# Azure Functions 應用程式會實作 API 端點，提供天氣資訊給靜態應用程式。 `WeatherForecast` 物件的 [`WeatherForecastFunction`](https://github.com/ssdeepak/blazor/blob/main/Api/WeatherForecastFunction.cs) 會傳回陣列。

- **用戶端**：前端 Blazor Web 組件專案。 系統會實作[後援路由](#fallback-route)，確保所有路由都會提供 index.html檔案。

- **共用**：保存 API 和用戶端專案所參考的通用類別，可讓資料從 API 端點流向前端 Web 應用程式。 [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) 類別會在兩個應用程式之間共用。

這些專案是建立在瀏覽器中執行之 Blazor Web 組件應用程式不可或缺的要件，並由 API 後端支援。

## <a name="fallback-route"></a>後援路由

應用程式會公開像是 /counter 的 URL，以及對應至應用程式特定路由的 /fetchdata。 由於此應用程式會實作為單一頁面應用程式，因此系統會為每個路由提供 index.html 檔案。 如此可確保任何路徑將[後援路由](./routes.md#fallback-routes)傳回 index.html 的要求會在用戶端專案的 wwwroot 資料夾中找到的 routes.json 檔案中實作。

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

上述組態可確保應用程式中任何路由的要求都會傳回 index.html 頁面。

## <a name="create-a-repository"></a>建立存放庫

本文使用 GitHub 範本存放庫，讓您可以輕鬆地開始使用。 範本具有部署至 Azure Static Web Apps 的入門應用程式。

1. 確定您已登入 GitHub，並瀏覽至下列位置以建立新的存放庫：
    - https://github.com/staticwebdev/blazor-starter/generate
1. 將您的存放庫命名為 **my-first-static-blazor-app**

## <a name="create-a-static-web-app"></a>建立靜態 Web 應用程式

現在已建立存放庫，可以從 Azure 入口網站建立靜態 Web 應用程式。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 選取 [建立資源]
1. 搜尋**靜態 Web 應用程式**
1. 選取 [Static Web Apps (預覽)]
1. 選取 [建立] 

在基本資料區段中，先設定您的新應用程式，並將其連結至 GitHub 存放庫。

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="完整的 Blazor 應用程式":::

1. 選取您的 _Azure 訂用帳戶_
1. 選取或建立新的 [資源群組]
1. 將應用程式命名為 **my-first-static-blazor-app**
    - 有效字元為 `a-z` (區分大小寫)、`0-9` 以及 `-`。
1. 選取最靠近您的 [區域]
1. 選取 [免費] _SKU_
1. 選取 [使用 GitHub 登入] 按鈕，並向 GitHub 進行驗證

當您使用 GitHub 登入後，請輸入存放庫資訊。

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="完整的 Blazor 應用程式":::

1. 選取您慣用的 [組織]
1. 從存放庫下拉式清單中選取 **my-first-static-blazor-app**
1. 從分支 下拉式選單中，選取 [主要]

    如果您沒有看到任何存放庫，可能需要在 GitHub 中授權 Azure Static Web Apps。 瀏覽至您的 GitHub 存放庫，並移至 [設定] > [應用程式] > [授權的 OAuth 應用程式]、選取 [Azure Static Web Apps]，然後選取 [授與]。 針對組織存放庫，您必須是組織的擁有者，才能授與權限。

1. 在組建詳細資料區段中，新增 Blazor 特有的組態詳細資料。

    - 從組建預設下拉式清單中選取 [Blazor]，並保留所有預設值。

1. 選取 [檢閱 + 建立]。

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="完整的 Blazor 應用程式":::

1. 選取 [建立]  。

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="完整的 Blazor 應用程式":::

1. 選取 [前往資源]  。

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="完整的 Blazor 應用程式":::

## <a name="view-the-website"></a>檢視網站

部署靜態應用程式有兩個層面。 第一個層面會佈建構成您應用程式的基礎 Azure 資源。 第二個層面是 GitHub Actions 工作流程，會建置及發佈您的應用程式。

在您可以瀏覽至新的靜態網站之前，部署建置必須先完成執行。

Static Web Apps 概觀視窗會顯示一系列的連結，協助您與 Web 應用程式互動。

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="完整的 Blazor 應用程式":::

1. 按一下顯示「按一下這裡檢查您的 GitHub Actions 執行狀態」的橫幅，會帶您前往針對您存放庫執行的 GitHub Actions。 一旦您確認部署作業已完成，您就可以透過產生的 URL 瀏覽至您的網站。

2. GitHub Actions 工作流程完成之後，您可以選取 URL 連結，在新索引標籤中開啟網站。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，您可以透過下列步驟刪除 Azure Static Web Apps 執行個體：

1. 開啟 [Azure 入口網站](https://portal.azure.com)
1. 從頂端的搜尋列搜尋 **my-first-static-blazor-app**
1. 選取應用程式名稱
1. 選取 [刪除] 按鈕
1. 選取 [是] 以確認刪除動作

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [驗證與授權](./authentication-authorization.md)
