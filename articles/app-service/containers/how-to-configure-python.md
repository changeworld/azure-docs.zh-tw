---
title: 設定 Linux Python 應用程式
description: 了解如何為您的應用程式設定預先建置的 Python 容器。 本文說明最常見的設定工作。
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8a9276f73c1d9bdf0289f41bb59340b29f5a2575
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046030"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>設定適用於 Azure App Service 的 Linux Python 應用程式

本文說明 [Azure App Service](app-service-linux-intro.md) 會如何執行 Python 應用程式，以及要如何在需要時自訂 App Service 的行為。 您必須使用所有必要的 [pip](https://pypi.org/project/pip/) 模組來部署 Python 應用程式。

在您部署 [Git 存放庫](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)或 [Zip 套件](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (在開啟建置程序的情況下) 時，App Service 部署引擎會自動啟動虛擬環境，並為您執行 `pip install -r requirements.txt`。

本指南為在 App Service 中使用內建 Linux 容器的 Python 開發人員提供了重要概念和指示。 如果您從未使用過 Azure App Service，則首先應該遵循 [Python 快速入門](quickstart-python.md)和 [Python with PostgreSQL 教學課程](tutorial-python-postgresql-app.md)。

> [!NOTE]
> Linux 目前是在 App Service 中執行 Python 應用程式的建議選項。 如需 Windows 選項的資訊，請參閱 [Windows風格 App Service 的 Python](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service)。
>

## <a name="show-python-version"></a>顯示 Python 版本

若要顯示目前的 Python 版本，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要顯示所有支援的 Python 版本，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

您可以改為建置您自己的容器映像，以執行不支援的 Python 版本。 如需詳細資訊，請參閱[使用自訂 Docker 映像](tutorial-custom-docker-image.md)。

## <a name="set-python-version"></a>設定 Python 版本

在 [Cloud Shell](https://shell.azure.com) 中執行下列命令，以將 Python 版本設定為 3.7：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>自訂組建自動化

如果您使用 Git 或 zip 套件並開啟組建自動化來部署應用程式，App Service 組建自動化將會依下列順序逐步執行：

1. 執行自訂指令碼 (如果 `PRE_BUILD_SCRIPT_PATH` 已指定)。
1. 執行 `pip install -r requirements.txt`。
1. 如果在存放庫的根目錄位於 *manage.py* 中，請執行 *manage.py collectstatic*。 但若 `DISABLE_COLLECTSTATIC` 設定為 `true`，則可略過此步驟。
1. 執行自訂指令碼 (如果 `POST_BUILD_SCRIPT_PATH` 已指定)。

`PRE_BUILD_COMMAND`、`POST_BUILD_COMMAND` 和 `DISABLE_COLLECTSTATIC` 是預設為空值的環境變數。 若要執行建置前命令，請定義 `PRE_BUILD_COMMAND`。 若要執行建置後命令，請定義 `POST_BUILD_COMMAND`。 若要停用在建置 Django 應用程式時執行 collectstatic 的功能，請設定 `DISABLE_COLLECTSTATIC=true`。

下列範例會將兩個變數指定給一系列的命令 (以逗號分隔)。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

若要了解其他可自訂組建自動化的環境變數，請參閱 [Oryx 設定](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

若要深入了解 App Service 如何在 Linux 中執行和建置 Python 應用程式，請參閱 [Oryx 文件：如何偵測和建置 Python 應用程式](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)。

## <a name="container-characteristics"></a>容器的特性

Python 應用程式部署至 Linux 上的 App Service 後，則會在 [App Service Python GitHub 存放庫](https://github.com/Azure-App-Service/python)中所定義的 Docker 容器內執行。 您可以在屬於特定版本的目錄內找到映像設定。

此容器具有下列特性︰

- 應用程式在執行時所使用的是 [Gunicorn WSGI HTTP 伺服器](https://gunicorn.org/)，並且會使用額外的引數 `--bind=0.0.0.0 --timeout 600`。

- 根據預設，基底映像包含 Flask Web 架構，但容器也可支援其他符合 WSGI 標準且相容於 Python 3.7 的架構，例如 Django。

- 若要安裝其他套件 (例如 Django)，請使用 `pip freeze > requirements.txt` 在專案的根目錄中建立 [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) 檔案。 然後，使用 Git 部署將專案發佈至 App Service，該部署會在容器中自動執行 `pip install -r requirements.txt` 以安裝應用程式的相依性。

## <a name="container-startup-process"></a>容器的啟動程序

在啟動期間，Linux 容器上的 App Service 會執行下列步驟：

1. 使用[自訂的啟動命令](#customize-startup-command) (如果有提供的話)。
2. 檢查 [Django 應用程式](#django-app)是否存在，如果偵測到，則為其啟動 Gunicorn。
3. 檢查 [Flask 應用程式](#flask-app)是否存在，如果偵測到，則為其啟動 Gunicorn。
4. 如果找不到其他應用程式，則請啟動容器內建的預設應用程式。

下列各節會提供每個選項的其他詳細資料。

### <a name="django-app"></a>Django 應用程式

針對 Django 應用程式，App Service 會尋找應用程式的程式碼內名為 `wsgi.py` 的檔案，然後使用下列命令執行 Gunicorn：

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

如果您想要更加具體地控制啟動命令，請使用[自訂啟動命令](#customize-startup-command)，並將 `<module>` 替換為包含 wsgi.py  的模組名稱。

### <a name="flask-app"></a>Flask 應用程式

針對 Flask，App Service 會尋找名為 application.py  或 app.py  的檔案，並啟動 Gunicorn，如下所示：

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

如果主要的應用程式模組放在不同檔案內，請為該應用程式物件使用不同的名稱，如果您想要對 Gunicorn 提供額外的引數，則請使用[自訂啟動命令](#customize-startup-command)。

### <a name="default-behavior"></a>預設行為

如果 App Service 沒找到自訂命令、Django 應用程式或 Flask 應用程式，則會執行位於 opt/defaultsite  資料夾中的預設唯讀應用程式。 預設應用程式看起來會像下面這樣：

![Linux 網頁上的預設 App Service](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>自訂啟動命令

您可以藉由提供自訂 Gunicorn 啟動命令來控制容器的啟動行為。 若要這樣做，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

例如，如果您擁有的 Flask 應用程式，而其主要模組是 *hello.py*，且該檔案中的 Flask 應用程式物件名為 `myapp`，則 *\<custom-command>* 如下所示：

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

如果您的主要模組位於子資料夾中 (例如 `website`)，請使用 `--chdir` 引數指定該資料夾：

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

您也可以對 *\<custom-command>* 新增任何額外的 Gunicorn 引數，例如 `--workers=4`。 如需詳細資訊，請參閱[執行 Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org)。

若要使用非 Gunicorn 伺服器，例如 [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)，您可以使用以下內容取代 *\<custom-command>* ：

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> App Service 會忽略 自訂命令檔在處理時所發生的任何錯誤，然後透過尋找 Django 和 Flask 應用程式來繼續其啟動程序。 如果您沒有看到您所預期的行為，請檢查啟動檔案是否已部署至 App Service，且未包含任何錯誤。

## <a name="access-environment-variables"></a>存取環境變數

在 App Service 中，您可以於應用程式的程式碼外部[設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然後，您可以使用標準的 [os.environ](https://docs.python.org/3/library/os.html#os.environ) 模式來存取這些設定。 例如，若要存取稱為 `WEBSITE_SITE_NAME` 的應用程式設定，請使用下列程式碼：

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>偵測 HTTPS 工作階段

在 App Service 中，[SSL 終止](https://wikipedia.org/wiki/TLS_termination_proxy)會在網路負載平衡器上發生，因此所有的 HTTPS 要求都會以未加密 HTTP 要求的形式進入您的應用程式。 如果您的應用程式邏輯需要檢查使用者要求是否有加密，請檢查 `X-Forwarded-Proto` 標頭。

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

熱門的 Web 架構可讓您在標準的應用程式模式中存取 `X-Forwarded-*` 資訊。 在 [CodeIgniter](https://codeigniter.com/) 中，[is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) 預設會檢查 `X_FORWARDED_PROTO` 的值。

## <a name="access-diagnostic-logs"></a>存取診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在瀏覽器中開啟 SSH 工作階段

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑難排解

- **您在部署自己的應用程式程式碼之後，卻看到預設應用程式。** 之所以出現預設應用程式，原因可能是您並未將應用程式程式碼部署至 App Service，也可能是 App Service 找不到應用程式程式碼，而改為執行預設應用程式。
- 重新啟動 App Service，等候 15 到 20 秒，然後再檢查一次應用程式。
- 請確定您使用的是適用於 Linux 的 App Service，而不是以 Windows 為基礎的執行個體。 從 Azure CLI 執行 `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` 命令，並替換對應的 `<resource_group_name>` 和 `<app_service_name>`。 輸出應該會是 `app,linux`；若非如此，請重新建立 App Service 並選擇 Linux。
- 使用 SSH 或 Kudu 主控台直接連線到 App Service，並確認檔案有在 site/wwwroot  底下。 如果檔案不存在，請檢閱部署程序，並重新部署應用程式。
- 如果檔案存在，App Service 卻無法識別特定的啟動檔案。 請檢查應用程式的結構是否符合 App Service 對 [Django](#django-app) 或 [Flask](#flask-app) 的預期，或者，您也可以使用[自訂啟動命令](#customize-startup-command)。
- **您在瀏覽器中看到「服務無法使用」訊息。** 瀏覽器在等候 App Service 的回應時逾時，這表示 App Service 已啟動 Gunicorn 伺服器，但用來指定應用程式程式碼的引數不正確。
- 重新整理瀏覽器，如果您使用 App Service 方案中的最低定價層，更應該如此。 舉例來說，如果您使用免費層，應用程式可能需要更久的時間才能啟動，在您重新整理瀏覽器後，應用程式便會有回應。
- 請檢查應用程式的結構是否符合 App Service 對 [Django](#django-app) 或 [Flask](#flask-app) 的預期，或者，您也可以使用[自訂啟動命令](#customize-startup-command)。
- [存取記錄資料流](#access-diagnostic-logs)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 PostgreSQL 的 Python 應用程式](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [教學課程：從私人容器存放庫部署](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [App Service Linux 常見問題集](app-service-linux-faq.md)
