---
title: 快速入門：透過 CLI 使用 Azure Static Web Apps 建置您的第一個靜態網站
description: 了解如何使用 Azure CLI 將靜態網站部署至 Azure Static Web Apps。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: ca05194c0d8e63290a9cab8d9f7903daa192069b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030343"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>快速入門：使用 Azure CLI 建立您的第一個靜態網站

Azure Static Web Apps 藉由從 GitHub 存放庫建置應用程式，將網站發佈至實際執行環境。 在本快速入門中，您會使用 Azure CLI 將 Web 應用程式部署到 Azure 靜態 Web 應用程式。

如果您沒有 Azure 訂用帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>Prerequisites

- [GitHub](https://github.com) 帳戶
- [GitHub 個人存取權杖](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) 帳戶
- 已安裝 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) (2.8.0 版或更新版本)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

接下來，使用下列命令切換至新的資料夾。

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>建立靜態 Web 應用程式

現在已建立存放庫，您可以從 Azure CLI 建立靜態 Web 應用程式。

> [!IMPORTANT]
> 請確定您位於終端機的 _my-first-static-web-app_ 資料夾中。

1. 使用下列命令來登入 Azure CLI。

    ```bash
    az login
    ```

1. 從您的存放庫建立新的靜態 Web 應用程式。

    # <a name="no-framework"></a>[無架構](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`:以現有的 Azure 資源組名稱取代此值。

    - `<YOUR_GITHUB_ACCOUNT_NAME>`:將此值取代為您的 GitHub 使用者名稱。

    - `<LOCATION>`:將此值取代為最接近您的位置。 選項包括：_CentralUS_、_EastAsia_、_EastUS2_、_WestEurope_ 和 _WestUS2_。

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`:將此值取代為您先前產生的 [GitHub 個人存取權杖](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)。

    您現在可以在 Azure 中檢視已建立的應用程式。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。

1. 從頂端的搜尋列搜尋 **my-first-web-static-app**。

1. 選取 [my-first-web-static-app]。

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，您可以執行下列命令來刪除 Azure Static Web Apps 執行個體：

```bash
az staticwebapp delete \
    --name my-first-static-web-app
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增 API](add-api.md)
