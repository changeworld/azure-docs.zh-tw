---
title: 快速入門：建立 Linux Python 應用程式
description: 藉由將您的第一個 Python 應用程式部署至 App Service 中的 Linux 容器，在 Azure App Service 上開始使用 Linux 應用程式。
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
ms.openlocfilehash: 5b055c3ed93d5f093295b52c7a28a73e242bfe75
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690869"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>快速入門：在 Linux 上的 Azure App Service 中建立 Python 應用程式

在本快速入門中，您會將 Python 應用程式部署到 [Linux 上的 App Service](app-service-linux-intro.md)，這是 Azure 可高度擴充、自我修復的 Web 裝載服務。 您會在 Mac、Linux 或 Windows 電腦上使用本機 [Azure 命令列介面 (CLI)](/cli/azure/install-azure-cli)。 您設定的 Web 應用程式會使用免費的 App Service 層，因此在本文過程中不會產生任何費用。

如果您想要透過 IDE 部署應用程式，請參閱[從 Visual Studio Code 將 Python 應用程式部署至 App Service](/azure/python/tutorial-deploy-app-service-on-linux-01)。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (也支援 Python 3.6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 或更高版本。 執行 `az --version` 來檢查您的版本。

## <a name="download-the-sample"></a>下載範例

在終端機視窗中執行下列命令，將範例應用程式複製到本機電腦。 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

然後移至該資料夾中：

```terminal
cd python-docs-hello-world
```

存放庫包含 *application.py* 檔案，該檔案會向 App Service 指出程式碼包含 Flask 應用程式。 如需詳細資訊，請參閱[容器的啟動程序和自訂項目](how-to-configure-python.md)。

## <a name="run-the-sample"></a>執行範例

在終端機視窗中，使用下列命令 (適用於您的作業系統) 安裝必要的相依性，然後啟動內建的開發伺服器。 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

開啟網頁瀏覽器，然後移至位於 `http://localhost:5000/` 的範例應用程式。 應用程式會顯示 **Hello World!** 訊息。

![在本機執行範例 Python 應用程式](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

在終端機視窗中，按 **Ctrl**+**C** 結束網頁伺服器。

## <a name="sign-in-to-azure"></a>登入 Azure

Azure CLI 提供給您許多可從本機終端機使用的便利命令，以便從命令列佈建和管理 Azure 資源。 您可以使用這些命令來完成您在瀏覽器中透過 Azure 入口網站執行的相同工作。 您也可以在指令碼中使用 CLI 命令，將管理程序自動化。

若要在 Azure CLI 中執行 Azure 命令，您必須先使用 `az login` 命令進行登入。 此命令會開啟瀏覽器來收集您的認證。

```azurecli
az login
```

## <a name="deploy-the-sample"></a>部署範例

[`az webapp up`](/cli/azure/webapp#az-webapp-up) 命令會在 App Service 上建立 Web 應用程式並部署您的程式碼。

在包含範例程式碼的 *python-docs-hello-world* 資料夾中，執行下列 `az webapp up` 命令。 將 `<app-name>` 取代為全域唯一的應用程式名稱 (有效字元為 `a-z`、`0-9` 和 `-` )。


```azurecli
az webapp up --sku F1 -n <app-name>
```
> [!CAUTION]
> 如果您使用 **Azure CLI 2.5.0 版**，`az webapp up` 中會發生迴歸，也就是如果未包含 `-l <location-name>` 參數，特定案例將會失敗。 我們[已在這裡追蹤](https://github.com/Azure/azure-cli/issues/13257)此問題。  
> 
>您可以使用 `az --version` 命令來檢查您所使用的 Azure CLI 版本。
>

`--sku F1` 引數會在免費定價層上建立 Web 應用程式。 您可以省略這個引數，改為使用進階層，如此會產生每小時成本。

您也可以選擇納入引數，其中 `-l <location-name>`centralus`<location_name>` 是 Azure 區域，例如 **centralus**, **eastasia**、**westeurope**、**koreasouth**、**brazilsouth**、**centralindia** 等等。 您可執行 [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) 命令，擷取 Azure 帳戶的允許區域清單。

`az webapp up` 命令可能需要數分鐘才能完成執行。 執行上述命令時，系統會顯示類似下列範例的資訊，其中 `<app-name>` 會是您稍早提供的名稱：

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '&lt;app-name&gt;' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name&gt;.azurewebsites.net
{
  "URL": "http://&lt;app-name&gt;.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "FREE",
  "src_path": "D:\\Examples\\python-docs-hello-world"
}
</pre>

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>瀏覽至應用程式

使用網頁瀏覽器瀏覽至已部署的應用程式 (URL 為 `http://<app-name>.azurewebsites.net`)。

Python 範例程式碼目前使用內建映像在 App Service 中執行 Linux 容器。

![在 Azure 中執行範例 Python 應用程式](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**恭喜！** 您已將 Python 應用程式部署至 Linux 上的 App Service。

## <a name="redeploy-updates"></a>重新部署更新

在您偏好的程式碼編輯器中，開啟 application.py  並更新 `hello` 函式，如下所示。 這項變更會新增 `print` 陳述式，以產生您在下一節中使用的記錄輸出。 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

儲存您的變更並結束編輯器。 

再次使用 `az webapp up` 命令重新部署應用程式：

```azurecli
az webapp up
```

此命令會使用在 .azure/config  檔案中快取的值，包括應用程式名稱、資源群組和 App Service 方案。

完成部署後，切換回開啟至 `http://<app-name>.azurewebsites.net` 的瀏覽器視窗並重新整理頁面，這應會顯示修改過的訊息：

![在 Azure 中執行已更新的範例 Python 應用程式](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code 針對 Python 和 Azure App Service 提供強大的擴充功能，以簡化將 Python Web 應用程式部署至 App Service 的程序。 如需詳細資訊，請參閱[從 Visual Studio Code 將 Python 應用程式部署至 App Service](/azure/python/tutorial-deploy-app-service-on-linux-01)。

## <a name="stream-logs"></a>串流記錄

您可以存取從應用程式及其執行所在的容器產生的主控台記錄。 記錄包含使用 `print` 陳述式產生的任何輸出。

若要串流記錄，請執行下列命令：

```azurecli
az webapp log tail
```

在瀏覽器中重新整理應用程式以產生主控台記錄，其中應包含類似以下文字的文字行。 如果您沒有立即看到輸出，請在 30 秒後再試一次。

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。

若要隨時停止記錄資料流，請輸入 `Ctrl`+`C`。

## <a name="manage-the-azure-app"></a>管理 Azure 應用程式

移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，以管理您所建立的應用程式。 搜尋並選取 [應用程式服務]  。

![在 Azure 入口網站中瀏覽至應用程式服務](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

選取您 Azure 應用程式的名稱。

![在 Azure 入口網站的 App Services 中瀏覽至您的 Python 應用程式](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

您會看到應用程式的 [概觀] 頁面。 您可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。

![在 Azure 入口網站的 [概觀] 頁面中管理您的 Python 應用程式](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service 功能表提供不同的頁面來設定您的應用程式。

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在資源群組中建立了 Azure 資源。 視您的位置而定，資源群組的名稱類似於 "appsvc_rg_Linux_CentralUS"。 如果您使用免費 F1 層以外的 App Service SKU，這些資源會產生持續成本 (請參閱 [App Service 定價](https://azure.microsoft.com/pricing/details/app-service/linux/))。

如果您未來不需要這些資源，請執行下列命令來刪除資源群組，並將 `<resource-group-name>` 取代為 `az webapp up` 命令輸出中所顯示的資源群組，例如 "appsvc_rg_Linux_centralus"。 此命令可能需要一分鐘才能完成。

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：採用 PostgreSQL 的 Python (Django) Web 應用程式](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [將使用者登入新增至 Python Web 應用程式](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [設定 Python 應用程式](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [教學課程：在自訂容器中執行 Python 應用程式](tutorial-custom-docker-image.md)
