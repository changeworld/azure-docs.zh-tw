---
title: 適用於 Azure 靜態 Web Apps 的 GitHub Actions 工作流程
description: 了解如何使用 GitHub 存放庫來設定持續部署至 Azure 靜態 Web Apps。
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: chnwamba
ms.openlocfilehash: c7180be2afa541f743d7bd81627b93f34d9bb4eb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595267"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>適用於 Azure 靜態 Web Apps 預覽版的 GitHub Actions 工作流程

當您建立新的 Azure 靜態 Web 應用程式資源時，Azure 會產生 GitHub Actions 工作流程，以控制應用程式的持續部署。 工作流程是由 YAML 檔案驅動。 本文詳細說明工作流程檔案的結構和選項。

部署是由[觸發程序](#triggers)所起始，其會執行個別[步驟](#steps)所定義的[作業](#jobs)。

## <a name="file-location"></a>檔案位置

當您將 GitHub 存放庫連結至 Azure 靜態 Web Apps 時，會將工作流程檔案新增至存放庫。

請遵循下列步驟來檢視產生的工作流程檔案。

1. 在 GitHub 上開啟應用程式的存放庫。
1. 從 [程式碼] 索引標籤中，按一下 `.github/workflows` 資料夾。
1. 按一下名稱類似 `azure-static-web-apps-<RANDOM_NAME>.yml` 的檔案。

存放庫中的 YAML 檔案將會類似下列範例：

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v1
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        app_artifact_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>觸發程序

GitHub Actions [觸發程序](https://help.github.com/actions/reference/events-that-trigger-workflows)會通知 GitHub Actions 工作流程，以事件觸發程序為基礎執行作業。 會使用工作流程檔案中的 `on` 屬性來列出觸發程序。

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

您可以透過與 `on` 屬性相關聯的設定，定義哪些分支會觸發作業，並將觸發程序設定為針對不同的提取要求狀態進行引發。

在此範例中，工作流程會在_主要_分支變更時啟動。 啟動工作流程的變更包括推送認可，以及針對所選分支開啟提取要求。

## <a name="jobs"></a>工作

每個事件觸發程序都需要一個事件處理常式。 [作業](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)會定義觸發事件時所發生的情況。

在靜態 Web Apps 工作流程檔案中，有兩個可用的作業。

| 名稱  | 描述 |
|---------|---------|
|`build_and_deploy_job` | 當您推送認可或針對 `on` 屬性中所列的分支開啟提取要求時，即會執行。 |
|`close_pull_request_job` | 只有在您關閉提取要求時才會執行。 |

## <a name="steps"></a>步驟

步驟是作業的循序工作。 步驟會執行一些動作，例如安裝相依性、執行測試，以及將應用程式部署至生產環境。

工作流程檔案會定義下列步驟。

| 工作 (Job)  | 步驟  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>簽出 Action 環境中的存放庫。<li>建置存放庫，並將其部署至 Azure 靜態 Web Apps。</ol>|
| `close_pull_request_job` | <ol><li>通知 Azure 靜態 Web Apps 已關閉提取要求。</ol>|

## <a name="build-and-deploy"></a>建置及部署

名為 `Build and Deploy` 的步驟會建置並部署至您的 Azure 靜態 Web Apps 執行個體。 在 `with` 區段下，您可以針對部署自訂下列的值。

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    app_artifact_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| 屬性 | 描述 | 必要 |
|---|---|---|
| `app_location` | 應用程式的程式碼位置。<br><br>例如，如果您的應用程式原始程式碼位於存放庫的根目錄，則輸入 `/`，或如果您的應用程式程式碼位於名為 `app` 的目錄中，則輸入 `/app`。 | 是 |
| `api_location` | Azure Functions 程式碼的位置。<br><br>例如，如果您的應用程式程式碼位於名為 `api` 的資料夾中，請輸入 `/api`。 如果在資料夾中偵測不到任何 Azure Functions 的應用程式，則組建不會失敗，工作流程會假設您不想要 API。 | 否 |
| `app_artifact_location` | 組建輸出目錄相對於 `app_location` 的位置。<br><br>例如，如果您的應用程式原始程式碼位於 `/app`，而組建指令碼將檔案輸出到 `/app/build` 資料夾，則將 `build` 設定為 `app_artifact_location` 值。 | 否 |

Azure 靜態 Web Apps 為您設定的 `repo_token`、`action` 和 `azure_static_web_apps_api_token` 值不應手動變更。

## <a name="custom-build-commands"></a>自訂建置命令

您可以精細地控制在部署期間執行的命令。 下列命令可以在作業的 `with` 區段下進行定義。

部署一律會在任何自訂命令之前呼叫 `npm install`。

| Command            | 描述 |
|---------------------|-------------|
| `app_build_command` | 定義要在部署靜態內容應用程式期間執行的自訂命令。<br><br>例如，若要設定 Angular 應用程式的生產建置，請輸入 `ng build -prod`。 如果保留空白，則工作流程會嘗試執行 `npm run build` 或 `npm run build:Azure` 命令。  |
| `api_build_command` | 定義要在 Azure Functions API 應用程式部署期間執行的自訂命令。 |

## <a name="route-file-location"></a>路由檔案位置

您可以自訂工作流程，以尋找存放庫任何資料夾中的 [routes.json](routes.md)。 下列屬性可以在作業的 `with` 區段下進行定義。

| 屬性            | 描述 |
|---------------------|-------------|
| `routes_location` | 定義 _routes.json_ 檔案所在的目錄位置。 此位置相對於存放庫的根目錄。 |

 如果您的前端架構建置步驟依預設不會將此檔案移至 `app_artifact_location`，則明確了解 _routes.json_ 檔案的位置特別重要。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在生產前環境中檢閱提取要求](review-publish-pull-requests.md)
