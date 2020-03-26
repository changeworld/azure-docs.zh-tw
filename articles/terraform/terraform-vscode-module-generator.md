---
title: 教學課程 - 使用 Yeoman 在 Azure 中建立 Terraform 基底範本
description: 了解如何使用 Yeoman 在 Azure 中建立 Terraform 基底範本。
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472140"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>教學課程：使用 Yeoman 在 Azure 中建立 Terraform 基底範本

在本教學課程中，您會學到如何使用 [Terraform](/azure/terraform/) 和 [Yeoman](https://yeoman.io/) 的組合。 Terraform 是在 Azure 上建立基礎結構的工具。 Yeoman 可輕鬆建立 Terraform 模組。

在本文中，您會瞭解如何執行下列工作：
> [!div class="checklist"]
> * 使用 Yeoman 模組產生器建立基礎 Terraform 範本。
> * 使用兩種不同的方法測試 Terraform 範本。
> * 使用 Docker 檔案執行 Terraform 模組。
> * 在 Azure Cloud Shell 中以原生方式執行 Terraform 模組。

## <a name="prerequisites"></a>Prerequisites

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- **Visual Studio Code**：配合您的平台，[下載 Visual Studio Code](https://code.visualstudio.com/download)。
- **Terraform**：[安裝 Terraform](terraform-install-configure.md)，執行由 Yeoman 建立的模組。
- **Docker**：[安裝 Docker](https://www.docker.com/get-started)，執行由 Yeoman 產生器建立的模組。
- **Go 程式設計語言**：[Yeoman 產生的測試案例是使用 Go 語言的程式碼時，請安裝 Go](https://golang.org/)。

>[!NOTE]
>此教學課程中大部分的程序都需要使用命令列介面。 所述的步驟皆適用於所有作業系統與命令列工具。 至於範例，針對本機環境選擇使用 PowerShell，Cloud Shell 環境則使用 Git Bash。

## <a name="prepare-your-environment"></a>準備您的環境

### <a name="install-nodejs"></a>安裝 Node.js

若要在 Cloud Shell 中使用 Terraform，您必須[安裝 Node.js](https://nodejs.org/en/download/) 6.0+。

>[!NOTE]
>若要確認是否已安裝 Node.js，請開啟終端機視窗並輸入 `node --version`。

### <a name="install-yeoman"></a>安裝 Yeoman

執行以下命令：

```bash
npm install -g yo
```

![安裝 Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>安裝適用於 Terraform 模組的 Yeoman 範本

執行以下命令：

```bash
npm install -g generator-az-terra-module
```

![安裝 generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

若要確認是否已安裝 Yeoman，請執行下列命令：

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>建立 Yeoman 產生模組的目錄

Yeoman 範本會在目前的目錄中產生檔案。 因此，您必須建立目錄。

您必須將這個空白目錄放在 $GOPATH/src 底下。 如需此路徑的詳細資訊，請參閱[設定 GOPATH](https://github.com/golang/go/wiki/SettingGOPATH)文章。

1. 瀏覽至要建立新目錄的父代目錄。

1. 執行下列命令取代預留位置。 在此範例中，會使用 `GeneratorDocSample` 的目錄名稱。

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. 瀏覽到新目錄：

    ```bash
    cd <new-directory-name>
    ```

    ![瀏覽到您的新目錄](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>建立基底模組範本

1. 執行以下命令：

    ```bash
    yo az-terra-module
    ```

1. 依照螢幕上的指示提供下列資訊：

    - Terraform 模組專案名稱  - 範例中使用 `doc-sample-module` 的值。

        ![專案名稱](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Would you like to include the Docker image file?** \(是否要包含 Docker 映像檔案？\) - 輸入 `y`。 如果您選擇 `n`，產生的模組程式碼僅會支援以原生模式執行。

        ![包含 Docker 映像檔案？](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. 列出目錄內容以檢視建立的檔案：

    ```bash
    ls
    ```

    ![列出所建立檔案](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>檢閱所產生的模組程式碼

1. 啟動 Visual Studio Code

1. 從功能表列，選取 [檔案] > [開啟資料夾]  ，然後選取您所建立的資料夾。

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

下列檔案是由 Yeoman 模組產生器所建立。 如需這些檔案及其使用方式的詳細資訊，請參閱 [Terraform 模組中的 Terratest](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster) \(英文\)。

- `main.tf` - 定義名為 `random-shuffle`的模組。 輸入是 `string_list`。 輸出是排列的計數。
- `variables.tf` - 定義模組所使用的輸入與輸出變數。
- `outputs.tf` - 定義模組的輸出內容。 在這裡，該內容為由內建的 Terraform 模組 `random_shuffle` 所傳回的值。
- `Rakefile` - 定義建置步驟。 這些步驟包括：
    - `build` - 驗證 main.tf 檔案的格式。
    - `unit` - 產生的模組基本架構不會包含單元測試的程式碼。 如果您想要指定單元測試案例，便必須在此新增該程式碼。
    - `e2e` - 執行模組的端對端測試。
- `test`
    - 測試案例是以 Go 撰寫的。
    - test 中的所有程式碼都是端對端測試。
    - 端對端測試會嘗試佈建 `fixture` 下定義的所有項目。 會將 `template_output.go` 檔案中的結果與預先定義的預期值進行比較。
    - `Gopkg.lock` 和 `Gopkg.toml`：定義相依性。 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>使用 Docker 檔案測試新的 Terraform 模組

此節會介紹如何使用 Docker 檔案測試 Terraform 模組。

>[!NOTE]
>此範例會在本機上執行模組，而不是在 Azure 上執行。

### <a name="confirm-docker-is-installed-and-running"></a>確認 Docker 已安裝且正在執行

從命令提示字元，輸入 `docker version`。

![Docker 版本](media/terraform-vscode-module-generator/ymg-docker-version.png)

輸出結果確認 Docker 已安裝。

若要確認 Docker 是否正在執行，請輸入 `docker info`。

![Docker 資訊](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>設定 Docker 容器

1. 從命令提示字元，輸入

    第 1 課：建立 Windows Azure 儲存體物件`docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`。

    系統將會顯示訊息 **Successfully built** (成功建置)。

    ![顯示成功建置的訊息](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. 在命令提示字元中，輸入 `docker image ls` 即可看到由您建立的模組 `terra-mod-example` 列出。

    ![包含新模組的清單](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. 輸入 `docker run -it terra-mod-example /bin/sh`。 執行 `docker run` 命令之後，您就會在 Docker 環境中。 此時，您可以使用 `ls` 命令來探索檔案。

    ![Docker 中的檔案清單](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>建置模組

1. 執行以下命令：

    ```bash
    bundle install
    ```

1. 執行以下命令：

    ```bash
    rake build
    ```

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>執行端對端測試

1. 執行以下命令：

    ```bash
    rake e2e
    ```

1. 過幾分鐘之後，將會出現 **PASS** (通過) 訊息。

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. 輸入 `exit` 完成測試，並結束 Docker 環境。

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>使用 Yeoman 產生器來建立和測試 Cloud Shell 中的模組

在本節中，會使用 Yeoman 產生器來建立和測試 Cloud Shell 中的模組。 比起使用 Docker 檔案，使用 Cloud Shell 可大幅簡化程序。 使用 Cloud Shell，會預先安裝下列產品：

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>啟動 Cloud Shell 工作階段

1. 透過 [Azure 入口網站](https://portal.azure.com/)、[shell.azure.com](https://shell.azure.com)或 [Azure 行動應用程式](https://azure.microsoft.com/features/azure-portal/mobile-app/)來啟動 Azure Cloud Shell 工作階段。

1. [歡迎使用 Azure Cloud Shell]  頁面隨即開啟。 選取 **Bash (Linux)** 。

    ![歡迎使用 Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. 若您尚未設定 Azure 儲存體帳戶，會出現下列畫面。 選取 [建立儲存體]  。

    ![您未掛接任何儲存體](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell 會在您先前選取的殼層中啟動，並顯示剛為您建立之雲端磁碟機的資訊。

    ![已建立您的雲端磁碟機](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>準備目錄以保存您的 Terraform 模組

1. 此時，Cloud Shell 已為您在環境變數中設定 GOPATH。 若要查看此路徑，請輸入 `go env`。

1. 如果尚無 $GOPATH 目錄，請建立一個：輸入 `mkdir ~/go`。

1. 在 $GOPATH 目錄中建立目錄。 此目錄會用來保存此範例中建立的不同專案目錄。 

    ```bash
    mkdir ~/go/src
    ```

1. 建立目錄來保存您的 Terraform 模組以取代預留位置。 在此範例中，會使用 `my-module-name` 的目錄名稱。

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. 瀏覽至您的模組目錄： 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>建立和測試您的 Terraform 模組

1. 執行下列命令，並按照指示進行。 如果系統詢問您是否要建立 Docker 檔案，請輸入 `N`。

    ```bash
    yo az-terra-module
    ```

1. 執行下列命令以安裝相依性：

    ```bash
    bundle install
    ```

1. 執行下列命令以建置模組：

    ```bash
    rake build
    ```

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. 執行下列命令以執行文字：

    ```bash
    rake e2e
    ```

    ![測試 - 傳遞結果](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [安裝和使用 Azure Terraform Visual Studio Code 擴充功能](/azure/terraform/terraform-vscode-extension)。