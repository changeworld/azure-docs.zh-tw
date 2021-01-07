---
title: 教學課程：在 Azure App Service 中建置及執行自訂映像
description: 建置自訂 Linux 或 Windows 映像、將映像推送至 Azure Container Registry，然後將該映像部署至 Azure App Service 的逐步指南。 了解如何將部署自訂軟體遷移至自訂容器中的 App Service。
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: azure app service, web 應用程式, linux, windows, docker, 容器
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: b3507e22c691f3e3ca9f9e6562a313e95e42f080
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900190"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>使用自訂容器將自訂軟體遷移至 Azure App Service

::: zone pivot="container-windows"  

[Azure App Service](overview.md) 會在 Windows 上提供預先定義的應用程式堆疊 (例如 ASP.NET 或 Node.js)，執行於 IIS 上。 預先設定的 Windows 環境會鎖定作業系統的系統管理存取、軟體安裝、對全域組件快取的變更等作業 (請參閱 [Azure App Service 上的作業系統功能](operating-system-functionality.md))。 但是，在 App Service 中使用自訂 Windows 容器可讓您進行應用程式所需的作業系統變更，因此能夠很容易地移轉需要自訂作業系統與軟體設定的內部部署應用程式。 此教學課程會示範如何將使用 Windows 字型庫中已安裝自訂字型的 ASP.NET 應用程式移轉至 App Service。 您會將自訂的 Windows 映像從 Visual Studio 部署到 [Azure Container Registry](../container-registry/index.yml)，然後在 App Service 中執行。

![顯示在 Windows 容器中執行的 Web 應用程式。](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

- <a href="https://hub.docker.com/" target="_blank">註冊 Docker Hub 帳戶</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">安裝適用於 Windows 的 Docker</a>。
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">切換 Docker 以執行 Windows 容器</a>。
- <a href="https://www.visualstudio.com/downloads/" target="_blank">安裝 Visual Studio 2019</a>，記得包含 **ASP.NET 與網頁程式開發** 與 **Azure 開發** 工作負載。 若您已安裝 Visual Studio 2019：
    - 按一下 [說明] > [檢查更新] 以安裝最新的 Visual Studio 更新。
    - 按一下 [工具] > [取得工具與功能] 以在 Visual Studio 中新增工作負載。

## <a name="set-up-the-app-locally"></a>在本機設定應用程式

### <a name="download-the-sample"></a>下載範例

在此步驟中，您要設定本機 .NET 專案。

- [下載範例專案](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip)。
- 擷取 (解壓縮) *custom-font-win-container.zip* 檔案。

範例專案包含一個使用 Windows 字型庫中已安裝自訂字型的簡單 ASP.NET 應用程式。 您不需要安裝字型，但它是一個與底層 OS 整合的應用程式範例。 若要將此類應用程式移轉至 App Service，可以重新建構程式碼以移除整合，或在自訂 Windows 容器中原封不動進行移轉。

### <a name="install-the-font"></a>安裝字型

在 Windows 檔案總管中，瀏覽至 _custom-font-win-container-master/CustomFontSample_，以滑鼠右鍵按一下 _FrederickatheGreat-Regular.ttf_，然後選取 [安裝]。

可從 [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great) 下載此字型。

### <a name="run-the-app"></a>執行應用程式

在 Visual Studio 中開啟 *custom-font-win-container/CustomFontSample.sln* 檔案。 

輸入 `Ctrl+F5` 以執行應用程式而不偵錯。 應用程式會在預設瀏覽器中顯示。 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="顯示預設瀏覽器中顯示之應用程式的螢幕擷取畫面。":::

由於該應用程式是使用已安裝的字型，因此無法在 App Service 沙箱中執行。 但是，可以改為使用 Windows 容器部署它，因為可以在 Windows 容器中安裝字型。

### <a name="configure-windows-container"></a>設定 Windows 容器

在 [方案總管] 中，以滑鼠右鍵按一下 **CustomFontSample** 專案，然後選取 [新增] > [容器協調流程支援]。

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="[方案總管] 視窗的螢幕擷取畫面，其中顯示已選取 [CustomFontSample] 專案、[新增] 和 [容器協調器支援] 功能表項目。":::

選取 [Docker Compose] > [確定]。

您的專案現在已設定為在 Windows 容器中執行。 _Dockerfile_ 已新增至 **CustomFontSample** 專案，而且 **docker-compose** 專案已新增至解決方案。 

從 [方案總管] 開啟 **Dockerfile**。

您需要使用[支援的父映像](configure-custom-container.md#supported-parent-images)。 請使用下列程式碼取代 `FROM` 這一行，以變更父映像：

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

在檔案結尾處加入以下這一行並儲存檔案：

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

您可以在 **CustomFontSample** 專案中找到 _InstallFont.ps1_。 它是用於安裝字型的簡單指令碼。 您可以在 [Script Center](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133) 找到較複雜版本的指令碼。

> [!NOTE]
> 若要在本機測試 Windows 容器，請確定已在您的本機電腦上啟動 Docker。
>

## <a name="publish-to-azure-container-registry"></a>發佈至 Azure Container Registry

[Azure Container Registry](../container-registry/index.yml) 可儲存映像以用於容器部署。 您可以設定 App Service 使用 Azure Container Registry 中裝載的映像。

### <a name="open-publish-wizard"></a>開啟發佈精靈

在 [方案總管] 中，以滑鼠右鍵按一下 **CustomFontSample** 專案，然後選取 [發佈]。

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="[方案總管] 的螢幕擷取畫面，其中顯示 CustomFontSample 專案並選取 [發佈]。":::

### <a name="create-registry-and-publish"></a>建立登錄並發佈

在 [發佈精靈] 中，選取 [容器登錄] > [建立新的 Azure Container Registry] > [發佈]。

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="[發佈] 精靈的螢幕擷取畫面，其中顯示容器登錄、建立新的 Azure Container Registry，以及選取的 [發佈] 按鈕。":::

### <a name="sign-in-with-azure-account"></a>使用 Azure 帳戶登入

在 [建立新的 Azure Container Registry] 對話方塊中，選取 [新增帳戶]，然後登入您的 Azure 訂用帳戶。 如果您已登入，請從下拉式清單中選取包含所需訂用帳戶的帳戶。

![登入 Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>設定登錄

依據下表建議的值設定新的容器登錄。 完成後，按一下 [建立]。

| 設定  | 建議的值 | 取得詳細資訊 |
| ----------------- | ------------ | ----|
|**DNS 首碼**| 保留產生的登錄名稱，或將它變更為其他唯一名稱。 |  |
|**資源群組**| 按一下 [新增]，輸入 **myResourceGroup**，然後按一下 [確定]。 |  |
|**SKU**| 基本 | [定價層](https://azure.microsoft.com/pricing/details/container-registry/)|
|**登錄位置**| 西歐 | |

![設定 Azure 容器登錄](./media/tutorial-custom-container/configure-registry.png)

此時會開啟終端機視窗並顯示映像部署進度。 等待部署完成。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-web-app"></a>建立 Web 應用程式

從左側功能表，選取 [建立資源] > [網路] > [用於容器的 Web App]。

### <a name="configure-app-basics"></a>設定應用程式基本功能

在 [基本] 索引標籤中，根據下表進行設定，然後按 **[下一步：Docker]** 。

| 設定  | 建議的值 | 取得詳細資訊 |
| ----------------- | ------------ | ----|
|**訂用帳戶**| 請確定已選取正確的訂用帳戶。 |  |
|**資源群組**| 選取 [新建]，輸入 **myResourceGroup**，然後按一下 [確定]。 |  |
|**名稱**| 輸入唯一名稱。 | Web 應用程式的 URL 是 `http://<app-name>.azurewebsites.net`，其中 `<app-name>` 是您的應用程式名稱。 |
|**Publish**| Docker 容器 | |
|**作業系統**| Windows | |
|**區域**| 西歐 | |
|**Windows 方案**| 選取 [新建]，輸入 **myAppServicePlan**，然後按一下 [確定]。 | |

您的 [基本] 索引標籤應該會顯示如下：

![顯示用來設定 Web 應用程式的 [基本] 索引標籤。](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>設定 Windows 容器

在 [Docker] 索引標籤中，依照下表中的說明設定您的自訂 Windows 容器，然後選取 [檢閱 + 建立]。

| 設定  | 建議的值 |
| ----------------- | ------------ |
|**映像來源**| Azure 容器登錄 |
|**登錄**| 選取[您先前建立的登錄](#publish-to-azure-container-registry)。 |
|**映像**| customfontsample |
|**Tag**| 最新 |

### <a name="complete-app-creation"></a>完成應用程式建立作業

按一下 [建立]，並等候 Azure 建立所需的資源。

## <a name="browse-to-the-web-app"></a>瀏覽至 Web 應用程式

Azure 作業完成時，會顯示通知方塊。

![顯示 Azure 作業已完成。](media/tutorial-custom-container/portal-create-finished.png)

1. 按一下 [前往資源]。

2. 在應用程式頁面中，按一下 [URL] 下方的連結。

新的瀏覽器頁面隨即開啟，並顯示下列頁面：

![顯示 Web 應用程式的新瀏覽器頁面。](media/tutorial-custom-container/app-starting.png)

等候幾分鐘並再試一次，直到獲得包含所需美麗字型的首頁為止。

![以您設定的字型顯示首頁。](media/tutorial-custom-container/app-running.png)

**恭喜！** 您已運用 Windows 容器將 ASP.NET 應用程式移轉至 Azure App Service。

## <a name="see-container-start-up-logs"></a>檢視容器啟動記錄

Windows 容器載入可能需要一些時間。 若要查看進度，請將 *\<app-name>* 取代為您的應用程式名稱，以瀏覽至下列 URL。
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

串流處理的記錄會如下所示：

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service 會使用 Docker 容器技術，來裝載內建映像和自訂映像。 若要查看內建映像清單，請執行 Azure CLI 命令 ['az webapp list-runtimes --linux'](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-list-runtimes)。 如果這些映像無法滿足您的需求，可以建置和部署自訂映像。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 如果內建映像均無法滿足您的需求，請建立自訂映像
> * 將自訂映像推送到 Azure 上的私人容器登錄中
> * 在 App Service 中執行自訂映像
> * 設定環境變數
> * 更新和重新部署映像
> * 存取診斷記錄
> * 使用 SSH 連線到容器

完成本教學課程會在您的 Azure 帳戶中針對容器登錄產生少量費用，而且可能會增加超過一個月的裝載容器成本。

## <a name="set-up-your-initial-environment"></a>設定初始環境

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 安裝用來建置Docker 映像的 [Docker](https://docs.docker.com/get-started/#setup)。 安裝 Docker 可能需要將電腦重新開機。
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- 本教學課程需要 2.0.80 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

安裝 Docker 或執行 Azure Cloud Shell 後，請開啟終端機視窗，並確認已安裝 Docker：

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>複製或下載範例應用程式

您可以透過 GIT 複製或下載取得本教學課程的範例。

### <a name="clone-with-git"></a>使用 GIT 複製

複製範例存放庫：

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

請務必包含 `--config core.autocrlf=input` 引數，以確保在 Linux 容器內使用的檔案中有適當的行尾結束符號：

然後移至該資料夾中：

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>從 GitHub 下載

您可以瀏覽 [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux)、選取 [複製]，然後選取 [下載 ZIP]，而不使用 GIT 複製。 

將 ZIP 檔案解壓縮至名為 docker-django-webapp-linux 的資料夾。 

然後在該 docker-django-webapp-linux 資料夾中開啟終端機視窗。

## <a name="optional-examine-the-docker-file"></a>(選用) 檢查 Docker 檔案

範例中名為 Dockerfile 的檔案會描述 Docker 映像並包含組態指示：

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* 第一個命令群組會在環境中安裝應用程式的需求。
* 第二個命令群組會建立 [SSH](https://www.ssh.com/ssh/protocol/) 伺服器，以在容器與主機之間進行安全通訊。
* 最後一行 `ENTRYPOINT ["init.sh"]` 會叫用 `init.sh` 來啟動 SSH 服務和 Python 伺服器。

## <a name="build-and-test-the-image-locally"></a>在本機建置和測試映像

> [!NOTE]
> Docker Hub 具有 [每個 IP 提取的匿名數量配額和每個免費使用者提取的已驗證數量 (請參閱 **資料傳輸**)](https://www.docker.com/pricing)。 如果您注意到您從 Docker Hub 的提取數量受到限制，若您尚未登入，請嘗試 `docker login`。
> 

1. 執行下列命令以建置映像：

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. 在本機執行 Docker 容器，測試組建是否能正常運作：

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    此 [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 命令會指定具有 `-p` 引數的連接埠，後面接著映像的名稱。 
    
    > [!TIP]
    > 如果您在 Windows 上執行並看到 standard_init_linux.go:211: exec user process caused "no such file or directory" 錯誤，則 init.sh 檔案會包含 CR-LF 行尾結束符號，而不是預期的 LF 尾結束符號。 如果您使用 GIT 來複製範例存放庫，但省略了 `--config core.autocrlf=input` 參數，就會發生此錯誤。 在此情況下，請使用 `--config`` 引數再次複製存放庫。 如果您編輯 init.sh，並在 CRLF 結尾處儲存，您可能也會看到錯誤。 在此情況下，請只使用 LF 結束符號再次儲存檔案。

1. 瀏覽至 `http://localhost:8000`，驗證 Web 應用程式和容器是否正常運作。

    ![在本機測試 Web 應用程式](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>建立資源群組

在本節和接下來的各節中，您會在 Azure 中佈建資源，以將映像推送至其中，然後將容器部署至 Azure App Service。 一開始會先建立資源群組，以在其中收集所有這些資源。

執行 [az group create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create) 命令以建立資源群組：

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

您可以變更 `--location` 值，以指定您附近的區域。

## <a name="push-the-image-to-azure-container-registry"></a>將映像推送至 Azure Container Registry

在本節中，您會將映像推送至 App Service 可以從中部署的 Azure Container Registry。

1. 執行 [`az acr create`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-create) 命令以建立 Azure Container Registry：

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    以適用的登錄名稱取代 `<registry-name>`。 名稱只能包含字母和數字，且必須是整個 Azure 中的唯一名稱。

1. 執行 [`az acr show`](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az-acr-show) 命令以擷取登錄的認證：

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    此命令的 JSON 輸出會提供兩個密碼以及登錄的使用者名稱。
    
1. 使用 `docker login` 命令登入容器登錄：

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    以先前步驟中的值取代 `<registry-name>` 和 `<registry-username>`。 出現提示時，輸入上一個步驟中的其中一個密碼。

    您在本節的所有其他步驟中，會使用相同的登錄名稱。

1. 登入成功後，請為登錄標記本機 Docker 映像：

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. 使用 `docker push` 將映像推送至登錄：

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    第一次上傳映像可能需要幾分鐘的時間，因為其包含基礎映像。 後續的上傳通常會較快。

    在等候時，可以完成下一節中的步驟，以設定從登錄部署 App Service。

1. 使用 `az acr repository list` 命令確認是否成功推送：

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    輸出應該會顯示映像的名稱。


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>設定 App Service 從登錄部署映像

若要將容器部署至 Azure App Service，請先在 App Service 上建立 Web 應用程式，然後將 Web 應用程式連線至容器登錄。 啟動 Web 應用程式時，App Service 會自動從登錄中提取容器映像。

1. 使用 [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest&preserve-view=true#az-appservice-plan-create) 命令來建立 App Service 方案：

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    App Service 方案會對應至裝載 Web 應用程式的虛擬機器。 根據預設，先前的命令會使用第一個月免費的低成本 [B1 定價層](https://azure.microsoft.com/pricing/details/app-service/linux/)。 您可以使用 `--sku` 參數來控制層級。

1. 使用 [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az-webapp-create) 命令建立 Web 應用程式：

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    以 Web 應用程式的名稱取代 `<app-name>`，此名稱在所有 Azure 中必須是唯一的。 使用上一節的登錄名稱取代 `<registry-name>`。

1. 使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set)，以應用程式代碼所預期的內容設定 `WEBSITES_PORT` 環境變數： 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    將 `<app-name>` 取代為上一個步驟中使用的名稱。
    
    如需此環境變數的詳細資訊，請參閱[範例的 GitHub 存放庫中的讀我檔案](https://github.com/Azure-Samples/docker-django-webapp-linux)。

1. 使用 [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest&preserve-view=true#az-webapp-identity-assign) 命令，為 Web 應用程式啟用[受控識別](./overview-managed-identity.md)：

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    將 `<app-name>` 取代為上一個步驟中使用的名稱。 命令的輸出 (由 `--query` 和 `--output` 引數篩選) 是指派之身分識別的服務主體，您很快就會用到此資訊。

    受控識別可讓您將權限授與 Web 應用程式，在不需要任何特定認證的情況下存取其他 Azure 資源。

1. 使用 [`az account show`](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az-account-show) 命令來擷取您的訂用帳戶識別碼，您在下一個步驟中會需要此資訊：

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. 授與 Web 應用程式存取容器登錄的權限：

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    取代下列值：
    - `<principal-id>` 使用來自 `az webapp identity assign` 命令的服務主體識別碼
    - `<registry-name>` 使用您的容器登錄名稱
    - `<subscription-id>` 使用 `az account show` 命令中擷取的訂用帳戶識別碼

如需這些權限的詳細資訊，請參閱[什麼是 Azure 角色型存取控制](../role-based-access-control/overview.md)和 

## <a name="deploy-the-image-and-test-the-app"></a>部署映像並測試應用程式

一旦映像推送至容器登錄，而且已完整佈建 App Service 後，您就可以完成這些步驟。

1. 使用 [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest&preserve-view=true#az-webapp-config-container-set) 命令來指定容器登錄，以及要部署 Web 應用程式的映像：

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    將 `<app_name>` 取代為您的 Web 應用程式名稱，並以您的登錄名稱取代兩個位置中的 `<registry-name>`。 

    - 使用 Docker Hub 以外的登錄時 (如本範例所示)，`--docker-registry-server-url` 必須格式化為 `https://`，後面加上登錄的完整網域名稱。
    - 訊息「未提供認證以存取 Azure Container Registry。 正在嘗試查詢...」告訴您 Azure 使用應用程式的受控識別來驗證容器登錄，而不是要求使用者名稱和密碼。
    - 如果您遇到錯誤「AttributeError：'NoneType' 物件沒有 'reserved' 屬性」，請確定您的 `<app-name>` 正確。

    > [!TIP]
    > 您可以使用命令 `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg`，隨時擷取 Web 應用程式的容器設定。 映像會在屬性 `DOCKER_CUSTOM_IMAGE_NAME` 中指定。 透過 Azure DevOps 或 Azure Resource Manager 範本部署 Web 應用程式時，影像也會出現在名為 `LinuxFxVersion` 的屬性中。 這兩個屬性都有相同的功能。 如果兩者都存在於 Web 應用程式的組態中，系統會優先使用 `LinuxFxVersion`。

1. 一旦 `az webapp config container set` 命令完成，Web 應用程式應該會在 App Service 的容器中執行。

    若要測試應用程式，請瀏覽至 `http://<app-name>.azurewebsites.net`，並以您的 Web 應用程式名稱取代 `<app-name>`。 第一次存取時，應用程式可能需要一些時間才能回應，因為 App Service 必須從登錄提取整個映像。 如果瀏覽器逾時，只需重新整理頁面即可。 提取初始映像後，會更快速地執行後續測試。

    ![Azure 上成功的 Web 應用程式測試](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>修改應用程式碼並重新部署

在本節中，您會變更 Web 應用程式碼、重建容器，然後將容器推送至登錄。 App Service 接著會自動從登錄提取已更新的映像，以更新執行中的 Web 應用程式。

1. 在您的本機 docker-django-webapp-linux 資料夾中，開啟 app/templates/app/index.html檔案。

1. 變更第一個 HTML 元素以符合下列程式碼。

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. 儲存您的變更。

1. 變更為 docker-django-webapp-linux 資料夾並重建映像：

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. 將映像標記中的版本號碼更新為 1.0.1：

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    將 `<registry-name>` 取代為您的登錄名稱。

1. 將映像推送至登錄：

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. 重新啟動 Web 應用程式：

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    以 Web 應用程式名稱取代 `<app_name>`。 重新開機時，App Service 會從容器登錄提取已更新的映像。

1. 瀏覽至 `http://<app-name>.azurewebsites.net`，確認已部署更新。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

1. 開啟容器記錄：

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. 啟用記錄資料流：

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

    您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。

1. 若要隨時停止記錄資料流，請輸入 **Ctrl**+**C**。

## <a name="connect-to-the-container-using-ssh"></a>使用 SSH 連線到容器

SSH 可讓容器和用戶端之間進行安全通訊。 若要啟用與容器的 SSH 連線，則必須設定它的自訂映像。 容器執行後，您就可以開啟 SSH 連線。

### <a name="configure-the-container-for-ssh"></a>設定 SSH 的容器

本教學課程中使用的範例應用程式已經具有 Dockerfile 中的必要設定，其會安裝 SSH 伺服器，並設定登入認證。 本節內容僅供參考。 若要連線到容器，請跳到下一節

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> 此組態不允許容器的外部連線。 SSH 只能透過 Kudu/SCM 站台提供。 Kudu/SCM 站台會向 Azure 帳戶進行驗證。

Dockerfile 也會將 sshd_config 檔案複製到 /etc/ssh/ 資料夾，並在容器上公開連接埠 2222：

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

連接埠 2222 是供內部使用的連接埠，只有私人虛擬網路之橋接網路內的容器可以存取。 

最後，輸入腳本 init.sh 會啟動 SSH 伺服器。

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>開啟對容器的 SSH 連線

1. 請瀏覽至 `https://<app-name>.scm.azurewebsites.net/webssh/host` 並以您的 Azure 帳戶登入。 以 Web 應用程式名稱取代 `<app-name>`。

1. 登入之後，系統會將您重新導向至 Web 應用程式的參考頁面。 選取頁面頂端的 **SSH**，以開啟殼層並使用命令。

    例如，您可以使用 `top` 命令來檢查在其中執行的程序。
    
## <a name="clean-up-resources"></a>清除資源

您在本文中建立的資源可能會持續產生費用。 若要清除資源，您只需要刪除包含這些資源的資源群組：

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 將自訂映像部署到私人容器登錄中
> * 在 App Service 中部署和自訂映像
::: zone pivot="container-linux"
> * 更新和重新部署映像
::: zone-end
> * 存取診斷記錄
::: zone pivot="container-linux"
> * 使用 SSH 連線到容器
::: zone-end

在下一個教學課程中，您會了解如何將自訂的 DNS 名稱對應至應用程式。

> [!div class="nextstepaction"]
> [教學課程：將自訂 DNS 名稱對應至應用程式](app-service-web-tutorial-custom-domain.md)

或者，查看其他資源：

> [!div class="nextstepaction"]
> [設定自訂容器](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [教學課程：多容器 WordPress 應用程式](tutorial-multi-container-app.md)
::: zone-end
