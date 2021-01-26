---
title: 快速入門：在 Linux 上建立 Python 應用程式
description: 藉由將 Python 應用程式部署至 App Service 中的 Linux 容器，開始使用 Azure App Service。
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
robots: noindex
ms.openlocfilehash: ea320938f3e4f3b2d257947f605bb3b3f6f0886a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786203"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>快速入門：在 Linux 上的 Azure App Service 中建立 Python 應用程式

在本快速入門中，您會將 Python 應用程式部署到 [Linux 上的 App Service](overview.md#app-service-on-linux)，這是 Azure 可高度擴充、自我修復的 Web 裝載服務。 您會在 Mac、Linux 或 Windows 電腦上使用本機 [Azure 命令列介面 (CLI)](/cli/azure/install-azure-cli)，以部署具有 Flask 或 Django 架構的範例。 您設定的 Web 應用程式會使用免費的 App Service 層，因此在本文過程中不會產生任何費用。

> [!TIP]
> 如果您想要透過 IDE 部署應用程式，請參閱 **[從 Visual Studio Code 將 Python 應用程式部署至 App Service](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** 。

## <a name="set-up-your-initial-environment"></a>設定初始環境

1. 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
1. 安裝 <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 或更高版本</a>。
1. 安裝 <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 或更高版本，您可以在任何殼層中執行命令以佈建和設定 Azure 資源。

開啟終端視窗，並檢查您的 Python 版本為 3.6 或更高版本：

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

檢查您的 Azure CLI 版本為 2.0.80 或更高版本：

```azurecli
az --version
```

接著，透過 CLI 登入 Azure：

```azurecli
az login
```

此命令會開啟瀏覽器來收集您的認證。 當命令完成時，即會顯示 JSON 輸出，其中包含您的訂用帳戶相關資訊。

登入之後，您可以使用 Azure CLI 執行 Azure 命令，以使用訂用帳戶中的資源。

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>複製範例

使用下列命令複製範例存放庫，然後瀏覽至範例資料夾。 (如果您還沒有 GIT，請[安裝 GIT](https://git-scm.com/downloads)。)

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

然後，瀏覽至該資料夾：

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

然後，瀏覽至該資料夾：

```terminal
cd python-docs-hello-django
```
::: zone-end

此範例包含 Azure App Service 在啟動應用程式時所辨識的架構特定程式碼。 如需詳細資訊，請參閱[容器的啟動程序](configure-language-python.md#container-startup-process)。

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>部署範例

使用 `az webapp up` 命令，將程式碼部署在本機資料夾 (python-docs-hello-world) 中：

```azurecli
az webapp up --sku B1 --name <app-name>
```

- 如果無法辨識 `az` 命令，請確定您已安裝 Azure CLI，如[設定初始環境](#set-up-your-initial-environment)所說明。
- 如果無法辨識 `webapp` 命令，是因為您的 Azure CLI 版本為 2.0.80 或更高版本。 否則，請[安裝最新版本](/cli/azure/install-azure-cli)。
- 以 Azure 中的唯一名稱 (有效字元為 `a-z`、`0-9` 和`-`) 取代 `<app_name>`。 良好的模式是使用您的公司名稱和應用程式識別碼的組合。
- `--sku B1` 引數會在基本定價層上建立 Web 應用程式，而這會產生少量的每小時成本。 省略這個引數並使用進階層。
- 您可以選擇性地加入引數 `--location <location-name>`，其中 `<location_name>` 是可用的 Azure 區域。 您可執行 [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) 命令，擷取 Azure 帳戶的允許區域清單。
- 如果您看到「無法自動偵測您應用程式的執行階段堆疊」錯誤，請確定您正在含有 *requirements.txt* 檔案的 *python-docs-hello-world* 資料夾 (Flask) 或 *python-docs-hello-django* 資料夾 (Django) 中執行命令。 (請參閱 [使用 az webapp up 針對自動偵測問題進行疑難排解](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub)。)

此命令可能需要數分鐘才能完成。 執行時，此命令會提供有關建立資源群組、App Service 方案和裝載應用程式、設定記錄，然後執行 ZIP 部署的訊息。 接著會提供「您可以在 http://&lt;app-name&gt;.azurewebsites.net 啟動應用程式」的訊息，這是 Azure 上應用程式的 URL。

![az webapp up 命令範例輸出](./media/quickstart-python/az-webapp-up-output.png)

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>瀏覽至應用程式

使用網頁瀏覽器瀏覽至已部署的應用程式 (URL 為 `http://<app-name>.azurewebsites.net`)。 初次啟動應用程式需要一些時間。

Python 範例程式碼目前使用內建映像在 App Service 中執行 Linux 容器。

![在 Azure 中執行範例 Python 應用程式](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**恭喜！** 您已將 Python 應用程式部署至 App Service。

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>執行範例 

::: zone pivot="python-framework-flask"
1. 請確定您位於 *python-docs-hello-world* 資料夾中。 

1. 建立虛擬環境並安裝必要的相依性：

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    如果出現「[Errno 2] 沒有這類檔案或目錄：'requirements.txt'。」，請確定您位於 *python-docs-hello-world* 資料夾中。

1. 執行開發伺服器。

    ```terminal  
    flask run
    ```
    
    根據預設，伺服器會假設應用程式的輸入模組位於 *app.py* 中，如範例中所使用。 (如果您使用不同的模組名稱，請將 `FLASK_APP` 環境變數設定為該名稱。)

1. 開啟網頁瀏覽器，然後移至位於 `http://localhost:5000/` 的範例應用程式。 應用程式會顯示 **Hello, World!** 訊息。

    ![在本機執行範例 Python 應用程式](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. 在終端機視窗中，按 **Ctrl**+**C** 以結束開發伺服器。
::: zone-end

::: zone pivot="python-framework-django"
1. 請確定您位於 *python-docs-hello-django* 資料夾中。 

1. 建立虛擬環境並安裝必要的相依性：

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    如果出現「[Errno 2] 沒有這類檔案或目錄：'requirements.txt'。」，請確定您位於 *python-docs-hello-django* 資料夾中。
    
1. 執行開發伺服器。

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python3 manage.py runserver
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -3 manage.py runserver
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -3 manage.py runserver
    ```

    ---    

1. 開啟網頁瀏覽器，然後移至位於 `http://localhost:8000/` 的範例應用程式。 應用程式會顯示 **Hello, World!** 訊息。

    ![在本機執行範例 Python 應用程式](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. 在終端機視窗中，按 **Ctrl**+**C** 以結束開發伺服器。
::: zone-end

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>重新部署更新

在本節中，您會進行較小的程式碼變更，然後將程式碼重新部署至 Azure。 此程式碼變更會加入 `print` 陳述式，以產生您在下一節中使用的記錄輸出。

::: zone pivot="python-framework-flask"
在編輯器中開啟 *app.py* 並更新 `hello` 函式，以符合下列程式碼。 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
在編輯器中開啟 *hello/views.py* 並更新 `hello` 函式，以符合下列程式碼。

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
儲存您的變更，然後再次使用 `az webapp up` 命令重新部署應用程式：

```azurecli
az webapp up
```

此命令會使用在 .azure/config 檔案中本機快取的值，包括應用程式名稱、資源群組和 App Service 方案。

部署完成後，切換回開啟至 `http://<app-name>.azurewebsites.net` 的瀏覽器視窗。 重新整理頁面，如此應該會顯示修改過的訊息：

![在 Azure 中執行已更新的範例 Python 應用程式](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code 針對 Python 和 Azure App Service 提供強大的擴充功能，以簡化將 Python Web 應用程式部署至 App Service 的程序。 如需詳細資訊，請參閱[從 Visual Studio Code 將 Python 應用程式部署至 App Service](/azure/python/tutorial-deploy-app-service-on-linux-01)。

## <a name="stream-logs"></a>串流記錄

您可以存取從應用程式及其執行所在的容器產生的主控台記錄。 記錄包含使用 `print` 陳述式產生的任何輸出。

若要串流記錄，請執行 [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail) 命令：

```azurecli
az webapp log tail
```

您也可以加入 `--logs` 參數，然後使用 `az webapp up` 命令在部署時自動開啟記錄資料流。

在瀏覽器中重新整理應用程式以產生主控台記錄，其中應包含描述應用程式 HTTP 要求的訊息。 如果沒有立即看到輸出，請在 30 秒後再試一次。

您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。

您可以隨時在終端機中按 **Ctrl**+**C**，以停止記錄串流。

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>管理 Azure 應用程式

移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，以管理您所建立的應用程式。 搜尋並選取 [應用程式服務]  。

![在 Azure 入口網站中瀏覽至應用程式服務](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

選取您 Azure 應用程式的名稱。

![在 Azure 入口網站的 App Services 中瀏覽至您的 Python 應用程式](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

選取應用程式並開啟其 **概觀** 頁面，您可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。

![在 Azure 入口網站的 [概觀] 頁面中管理您的 Python 應用程式](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service 功能表提供不同的頁面來設定您的應用程式。

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在資源群組中建立了 Azure 資源。 視您的位置而定，資源群組的名稱類似於 "appsvc_rg_Linux_CentralUS"。 如果您讓 Web 應用程式保持執行狀態，將會產生一些持續成本 (請參閱 [App Service 定價](https://azure.microsoft.com/pricing/details/app-service/linux/))。

如果您在未來不需使用這些資源，請執行下列命令來刪除資源群組：

```azurecli
az group delete --no-wait
```

此命令會使用 .azure/config 檔案中快取的資源群組名稱。

`--no-wait` 引數可讓命令在作業完成之前傳回。

[有任何問題嗎？請告訴我們。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：採用 PostgreSQL 的 Python (Django) Web 應用程式](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [設定 Python 應用程式](configure-language-python.md)

> [!div class="nextstepaction"]
> [將使用者登入新增至 Python Web 應用程式](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [教學課程：在自訂容器中執行 Python 應用程式](tutorial-custom-container.md)
