---
title: 快速入門：使用 Azure Static Web Apps 建置您的第一個靜態 Web 應用程式
description: 了解如何使用您慣用的前端架構來建立 Azure Static Web Apps 執行個體。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 8de6e9cff8149423f4b00b07e3113be6606181b5
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714231"
---
# <a name="quickstart-building-your-first-static-web-app"></a>快速入門：建置您的第一個靜態 Web 應用程式

Azure Static Web Apps 藉由從 GitHub 存放庫建置應用程式，將網站發佈至實際執行環境。 在本快速入門中，您會使用慣用的前端架構，從 GitHub 存放庫建置 Web 應用程式。

如果您沒有 Azure 訂用帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>Prerequisites

- [GitHub](https://github.com) 帳戶
- [Azure](https://portal.azure.com) 帳戶

## <a name="create-a-repository"></a>建立存放庫

本文使用 GitHub 範本存放庫，讓您可以輕鬆地建立新的存放庫。 範本功能入門應用程式是以不同的前端架構建置。

# <a name="angular"></a>[Angular](#tab/angular)

- 瀏覽至下列位置以建立新的存放庫
  - https://github.com/staticwebdev/angular-basic/generate
- 將您的存放庫命名為 **my-first-static-web-app**

# <a name="react"></a>[React](#tab/react)

- 瀏覽至下列位置以建立新的存放庫
  - https://github.com/staticwebdev/react-basic/generate
- 將您的存放庫命名為 **my-first-static-web-app**

# <a name="vue"></a>[Vue](#tab/vue)

- 瀏覽至下列位置以建立新的存放庫
  - https://github.com/staticwebdev/vue-basic/generate
- 將您的存放庫命名為 **my-first-static-web-app**

# <a name="no-framework"></a>[無架構](#tab/vanilla-javascript)

- 瀏覽至下列位置以建立新的存放庫
  - https://github.com/staticwebdev/vanilla-basic/generate
- 將您的存放庫命名為 **my-first-static-web-app**

> [!NOTE]
> Azure Static Web Apps 需要至少一個 HTML 檔案，才能建立 Web 應用程式。 您在這個步驟中建立的存放庫包含單一 _index.html_ 檔案。

---

按一下 [從範本建立存放庫] 按鈕。

:::image type="content" source="media/getting-started/create-template.png" alt-text="從範本建立存放庫":::

## <a name="create-a-static-web-app"></a>建立靜態 Web 應用程式

現在已建立存放庫，您可以從 Azure 入口網站建立靜態 Web 應用程式。

- 瀏覽至 [Azure 入口網站](https://portal.azure.com)
- 按一下 [建立資源]
- 搜尋 [Static Web Apps]
- 按一下 [Static Web Apps (預覽)]
- 按一下 [建立] 

### <a name="basics"></a>基本概念

首先，設定您的新應用程式，並將其連結至 GitHub 存放庫。

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="[基本] 索引標籤":::

- 選取您的 _Azure 訂用帳戶_
- 選取或建立新的 [資源群組]
- 將應用程式命名為 **my-first-static-web-app**。
  - 有效字元為 `a-z` (區分大小寫)、`0-9` 以及 `-`。
- 選取最靠近您的 [區域]
- 選取 [免費] _SKU_
- 按一下 [使用 GitHub 登入] 按鈕，並向 GitHub 進行驗證

當您使用 GitHub 登入後，請輸入存放庫資訊。

:::image type="content" source="media/getting-started/repository-details.png" alt-text="存放庫詳細資料":::

- 選取您慣用的 [組織]
- 從 [存放庫] 下拉式清單中選取 **my-first-web-static-app**
- 從 [分支] 下拉式選單中，選取 [主要]
- 按 [下一步：組建 >] 按鈕，以編輯組建組態

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="下一個組建按鈕":::

### <a name="build"></a>Build

接下來，新增您慣用前端架構的特定設定詳細資料。

# <a name="angular"></a>[Angular](#tab/angular)

- 在 [應用程式位置] 方塊中輸入 **/**
- 清除 [API 位置] 方塊中的預設值
- 在 [應用程式成品位置] 方塊中輸入 **dist/angular-basic**

# <a name="react"></a>[React](#tab/react)

- 在 [應用程式位置] 方塊中輸入 **/**
- 清除 [API 位置] 方塊中的預設值
- 在 [應用程式成品位置] 方塊中輸入 **build**

# <a name="vue"></a>[Vue](#tab/vue)

- 在 [應用程式位置] 方塊中輸入 **/**
- 清除 [API 位置] 方塊中的預設值
- 在 [應用程式成品位置] 方塊中輸入 **dist**

# <a name="no-framework"></a>[無架構](#tab/vanilla-javascript)

- 在 [應用程式位置] 方塊中輸入 **/**
- 清除 [API 位置] 方塊中的預設值
- 清除 [應用程式成品位置] 方塊中的預設值

---

按一下 [檢閱 + 建立] 按鈕。

:::image type="content" source="media/getting-started/review-create.png" alt-text="檢閱建立按鈕":::

若要在建立應用程式之後變更這些值，您可以編輯[工作流程檔案](github-actions-workflow.md)。

### <a name="review--create"></a>檢閱 + 建立

要求經過驗證之後，您可以繼續建立應用程式。

按一下 [建立] 按鈕

:::image type="content" source="media/getting-started/create-button.png" alt-text="建立按鈕":::

建立資源之後，按一下 [移至資源] 按鈕

:::image type="content" source="media/getting-started/resource-button.png" alt-text="移至資源按鈕":::

## <a name="view-the-website"></a>檢視網站

部署靜態應用程式有兩個層面。 第一個層面會佈建構成您應用程式的基礎 Azure 資源。 第二個層面是 GitHub Actions 工作流程，會建置及發佈您的應用程式。

在您可以瀏覽至新的靜態網站之前，部署建置必須先完成執行。

Static Web Apps 概觀視窗會顯示一系列的連結，協助您與 Web 應用程式互動。

:::image type="content" source="media/getting-started/overview-window.png" alt-text="概觀視窗":::

1. 按一下顯示「按一下這裡檢查您的 GitHub Actions 執行狀態」的橫幅，會帶您前往針對您的存放庫執行的 GitHub Actions。 一旦您確認部署作業已完成，您就可以透過產生的 URL 瀏覽至您的網站。

2. GitHub Actions 工作流程完成之後，您可以按一下 _URL_ 連結，在新索引標籤中開啟網站。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，您可以透過下列步驟刪除 Azure Static Web Apps 執行個體：

1. 開啟 [Azure 入口網站](https://portal.azure.com)
1. 從頂端的搜尋列搜尋 **my-first-web-static-app**
1. 按一下應用程式名稱
1. 按一下 [刪除] 按鈕
1. 按一下 [是] 以確認刪除動作

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增 API](add-api.md)
