---
title: 教學課程：使用 Postgres 部署 Python Django 應用程式
description: 使用 PostgreSQL 資料庫建立 Python Web 應用程式，並部署至 Azure。 此教學課程會使用 Django 架構，應用程式則裝載於 Linux 上的 Azure App Service 中。
ms.devlang: python
ms.topic: tutorial
ms.date: 10/09/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: e171ce1ab7d2b9d4a78399ee639945bde16b71ca
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019404"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>教學課程：在 Azure App Service 中使用 PostgreSQL 部署 Django Web 應用程式

此教學課程說明如何將資料驅動的 Python [Django](https://www.djangoproject.com/) \(英文\) Web 應用程式部署至 [Azure App Service](overview.md)，並連線到適用於 Postgre 的 Azure 資料庫。 App Service 提供可高度擴充、自我修復的 Web 裝載服務。

在此教學課程中，您會使用 Azure CLI 來完成下列工作：

> [!div class="checklist"]
> * 使用 Python 和 Azure CLI 來設定初始環境
> * 建立「適用於 PostgreSQL 的 Azure 資料庫」資料庫
> * 將程式碼部署至 Azure App Service 並連線至 PostgreSQL
> * 更新您的程式碼並重新部署
> * 檢視診斷記錄
> * 在 Azure 入口網站中管理 Web 應用程式

您也可以使用[本教學課程的 Azure 入口網站版本](/azure/developer/python/tutorial-python-postgresql-app-portal)。


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

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clone-or-download-the-sample-app"></a>複製或下載範例應用程式

# <a name="git-clone"></a>[Git 複製](#tab/clone)

複製範例存放庫：

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

然後，瀏覽至該資料夾：

```terminal
cd djangoapp
```

# <a name="download"></a>[下載](#tab/download)

瀏覽 [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp)、選取 [複製]，然後選取 [下載 ZIP]。 

將 ZIP 檔案解壓縮至名為 *djangoapp* 的資料夾。 

然後，在該 *djangoapp* 資料夾中開啟終端視窗。

---

djangoapp 範例包含資料驅動的 Django 投票應用程式，您可以依照 Django 文件中的[撰寫您的第一個 Django 應用程式](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) \(英文\) 提供的指示加以取得。 這裡提供完整的應用程式，以方便您使用。

此範例也會修改為在實際執行環境中執行，例如 App Service：

- 實際執行設定位於 *azuresite/production.py* 檔案中。 開發詳細資料位於 *azuresite/settings.py*。
- 將 `DJANGO_ENV` 環境變數設定為 "production" 時，應用程式就會使用實際執行設定。 您稍後會在教學課程中建立此環境變數，以及用於 PostgreSQL 資料庫設定的其他變數。

這些變更專門用來將 Django 設定為在任何實際執行環境中執行，而不是特別針對 App Service。 如需詳細資訊，請參閱 [Django 部署檢查清單](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) \(英文\)。 另請參閱 [Azure 上的 Django 適用的生產設定](configure-language-python.md#production-settings-for-django-apps)，以取得某些變更的詳細資料。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="create-postgres-database-in-azure"></a>在 Azure 中建立 Postgres 資料庫

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

安裝適用於 Azure CLI 的 `db-up` 延伸模組：

```azurecli
az extension add --name db-up
```

如果無法辨識 `az` 命令，請確定您已安裝 Azure CLI，如[設定初始環境](#set-up-your-initial-environment)中所述。

然後，使用 [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) 命令，在 Azure 中建立 Postgres 資料庫：

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- 使用在所有 Azure 中都是唯一的名稱取代 *\<postgres-server-name>* (伺服器端點會成為 `https://<postgres-server-name>.postgres.database.azure.com`)。 良好的模式是使用您的公司名稱和另一個唯一值的組合。
- 為 *\<admin-username>* 和 *\<admin-password>* ，指定認證以建立此 Postgres 伺服器的系統管理員使用者。
- 這裡使用的 B_Gen5_1 (基本、第 5 代、1 核心) [定價層](../postgresql/concepts-pricing-tiers.md)成本最低。 針對實際執行資料庫，請省略 `--sku-name` 引數，改為使用 GP_Gen5_2 (一般用途、第 5 代、2 核心) 層。

此命令會執行下列動作，這可能需要幾分鐘的時間：

- 建立稱為 `DjangoPostgres-tutorial-rg` 的[資源群組](../azure-resource-manager/management/overview.md#terminology) (如果該資源群組尚未存在)。
- 建立由 `--server-name` 引數命名的 Postgres 伺服器。
- 使用 `--admin-user` 和 `--admin-password` 引數建立系統管理員帳戶。 您可以省略這些引數，以允許命令為您產生唯一的認證。
- 建立由 `--database-name` 引數命名的 `pollsdb` 資料庫。
- 啟用從您的本機 IP 位址進行存取。
- 啟用從 Azure 服務進行存取。
- 建立資料庫使用者，並提供 `pollsdb` 資料庫的存取權。

您可以分別使用其他 `az postgres` 和 `psql` 命令來執行所有步驟，但 `az postgres up` 會一併完成所有步驟。

當命令完成時，即會輸出 JSON 物件，其中包含適用於資料庫的不同連接字串，以及伺服器 URL、產生的使用者名稱 (例如 "joyfulKoala@msdocs-djangodb-12345") 及 GUID 密碼。 將使用者名稱與密碼複製到暫存文字檔，因為您稍後在此教學課程中將需要用到。

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`，可以設定為任何一個 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。 您可以使用 [`az account list-locations`](/cli/azure/account#az-account-list-locations) 命令，取得您訂用帳戶可用的區域。 針對生產應用程式，請將您的資料庫和應用程式放在相同的位置。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="deploy-the-code-to-azure-app-service"></a>將程式碼部署到 Azure App Service

在此節中，您會在 App Service 應用程式中建立應用程式主機、將此應用程式連線到 Postgres 資料庫，然後將程式碼部署到該主機。

### <a name="create-the-app-service-app"></a>建立 App Service 應用程式

在終端機中，確定您位於包含應用程式程式碼的 *djangoapp* 存放庫資料夾中。

使用 [`az webapp up`](/cli/azure/webapp#az-webapp-up) 命令來建立 App Service 應用程式 (主機處理序)：

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- 針對 `--location` 引數，使用與您在上一節針對資料庫所做的相同位置。
- 使用所有 Azure 中的唯一名稱取代 *\<app-name>* (伺服器端點為 `https://<app-name>.azurewebsites.net`)。 *\<app-name>* 的有效字元是 `A`-`Z`、`0`-`9` 和 `-`。 良好的模式是使用您的公司名稱和應用程式識別碼的組合。

此命令會執行下列動作，這可能需要幾分鐘的時間：

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- 如果[資源群組](../azure-resource-manager/management/overview.md#terminology)尚未存在，請加以建立。 (在此命令中，您使用先前用來建立資料庫的相同資源群組)。
- 在基本定價層 (B1) 中建立 [App Service 方案](overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* (如果不存在)。 `--plan` 和 `--sku` 是選擇性的。
- 建立 App Service 應用程式 (如果不存在)。
- 啟用應用程式的預設記錄 (如果尚未啟用)。
- 使用已啟用建置自動化的 ZIP 部署來上傳存放庫。
- 將一般參數 (例如，資源群組的名稱和 App Service 方案) 快取到檔案 *.azure/config* 中。因此，您不需使用後續命令來指定所有相同參數。 例如，若要在進行變更之後重新部署應用程式，只需再次執行 `az webapp up`，而不需任何參數。 不過，來自 CLI 延伸模組的命令 (例如 `az postgres up`) 目前不會使用快取，因此，在此您必須開始使用 `az webapp up` 指定資源群組和位置。

部署成功時，此命令會產生 JSON 輸出，如下列範例所示：

![範例 az webapp up 命令輸出](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> 如果您此時嘗試瀏覽應用程式的 URL，就會遇到 "DisallowedHost at /" 錯誤。 發生此錯誤的原因是，您尚未將應用程式設定為使用稍早討論過的實際執行設定，而您可以在下一節進行設定。

### <a name="configure-environment-variables-to-connect-the-database"></a>設定環境變數以連線資料庫

現在已將程式碼部署到 App Service，下一個步驟是將應用程式連線到 Azure 中的 Postgres 資料庫。

應用程式程式碼預期會在名為 `DBHOST`、`DBNAME`、`DBUSER` 和 `DBPASS` 的四個環境變數中尋找資料庫資訊。 若要使用生產設定，則也需要設定為 `production` 的 `DJANGO_ENV` 環境變數。

若要在 App Service 中設定環境變數，請使用下列 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令建立「應用程式設定」。

```azurecli
az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- 使用您稍早用來搭配 `az postgres up` 命令的名稱取代 *\<postgres-server-name>* 。 *azuresite/production.py* 中的程式碼會自動附加 `.postgres.database.azure.com`，以建立完整的 Postgres 伺服器 URL。
- 將 *\<username>* 和 *\<password>* 取代為您先前用於 `az postgres up` 命令的系統管理員認證，或 `az postgres up` 為您產生的認證。 *azuresite/production.py* 中的程式碼會自動從 `DBUSER` 和 `DBHOST` 中建構完整的 Postgres 使用者名稱。
- 資源群組和應用程式名稱均提取自 *.azure/config* 檔案中的快取值。

在您的 Python 程式碼中，您可以使用 `os.environ.get('DJANGO_ENV')` 之類的陳述式，存取這些設定作為環境變數。 如需詳細資訊，請參閱[存取環境變數](configure-language-python.md#access-environment-variables)。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="run-django-database-migrations"></a>執行 Django 資料庫移轉

Django 資料庫移轉可確保 Azure 資料庫上 PostgreSQL 中的結構描述符合您程式碼中所述的結構描述。

1. 瀏覽至下列 URL，並使用您的 Azure 帳戶認證 (而非資料庫伺服器認證) 登入，以在瀏覽器中開啟 SSH 工作階段。

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    將 `<app-name>` 取代為先前在 `az webapp up` 命令中使用的名稱。

    在 macOS 和 Linux 上，您可以選擇使用 [`az webapp ssh`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az_webapp_ssh) 命令連線至 SSH 工作階段。

    如果您無法連線至 SSH 工作階段，表示應用程式本身無法啟動。 如需詳細資訊，請[查看診斷記錄](#stream-diagnostic-logs)。 例如，如果您在上一節中未建立必要的應用程式設定，記錄將會指出 `KeyError: 'DBNAME'`。

1. 在 SSH 工作階段中，執行下列命令 (您可以使用 **Ctrl**+**Shift**+**V** 來貼上命令)：

    ```bash
    # Change to the folder where the app code is deployed
    cd site/wwwroot
    
    # Activate default virtual environment in App Service container
    source /antenv/bin/activate

    # Install packages
    pip install -r requirements.txt

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. `createsuperuser` 命令會提示您輸入超級使用者認證。 基於此教學課程的目的，請使用預設的使用者名稱 `root`、按 **Enter** 以讓電子郵件地址保留空白，然後輸入 `Pollsdb1` 作為密碼。

1. 如果您看到資料庫已遭鎖定的錯誤，請確定您已執行上一節中的 `az webapp settings` 命令。 若未完成這些設定，遷移命令就無法與資料庫通訊，而會導致錯誤。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)
    
### <a name="create-a-poll-question-in-the-app"></a>在應用程式中建立投票問題

1. 在瀏覽器中，開啟 URL `http://<app-name>.azurewebsites.net`。 應用程式應該會顯示訊息「沒有可用的投票」，因為資料庫中尚未有特定的投票。

    如果您看到「應用程式錯誤」，則可能是您未在先前的步驟[設定環境變數以連線資料庫](#configure-environment-variables-to-connect-the-database)中建立必要的設定，或是這些值包含錯誤。 請執行 `az webapp config appsettings list` 命令以檢查設定。 您也可以[檢查診斷記錄](#stream-diagnostic-logs)，以查看應用程式啟動期間的特定錯誤。 例如，如果您未建立設定，記錄將會顯示錯誤 `KeyError: 'DBNAME'`。

    更新設定以更正任何錯誤後，請稍候片刻讓應用程式重新啟動，然後重新整理瀏覽器。

1. 瀏覽至 `http://<app-name>.azurewebsites.net/admin`。 使用上一節的超級使用者認證 (`root` 和 `Pollsdb1`) 登入。 在 [投票] 下方，選取 [問題] 旁的 [新增]，然後建立具有一些選項的投票問題。

1. 再次瀏覽至 `http://<app-name>.azurewebsites.net`，以確認現在可向使用者呈現問題。 回答問題，但是您想要在資料庫中產生一些資料。

**恭喜！** 您正在適用於 Linux 的 Azure App Service 中執行 Python Django Web 應用程式，其中具有作用中的 Postgres 資料庫。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> App Service 會藉由尋找每個子資料夾中的 *wsgi.py* 檔案 (依預設會由 `manage.py startproject` 建立)，來偵測 Django 專案。 當 App Service 找到該檔案時，就會載入 Django Web 應用程式。 如需詳細資訊，請參閱[設定內建的 Python 映像](configure-language-python.md)。

## <a name="make-code-changes-and-redeploy"></a>進行程式碼變更並重新部署

在此節中，您會對應用程式進行本機變更，然後將程式碼重新部署到 App Service。 在此程序中，您會設定支援進行中工作的 Python 虛擬環境。

### <a name="run-the-app-locally"></a>在本機執行應用程式

在終端機視窗中，執行下列命令。 建立超級使用者時，請務必遵循提示：

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

完整載入 Web 應用程式之後，Django 開發伺服器會在下列訊息中提供本機應用程式 URL：「正在 http://127.0.0.1:8000/ 中啟動開發伺服器。 請使用 CTRL-BREAK 結束伺服器」。

![範例 Django 開發伺服器輸出](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

使用下列步驟，在本機測試應用程式：

1. 在瀏覽器中移至 `http://localhost:8000`，您應該會看到「沒有可用的投票」訊息。 

1. 移至 `http:///localhost:8000/admin`，然後使用您先前建立的管理使用者進行登入。 在 [投票] 下方，再次選取 [問題] 旁的 [新增]，然後建立具有一些選項的投票問題。 

1. 再次移至 *http:\//localhost:8000*，然後回答問題以測試應用程式。 

1. 按 **Ctrl**+**C** 以停止 Django 伺服器。

在本機執行時，應用程式會使用本機 Sqlite3 資料庫，而且不會干擾您的實際執行資料庫。 如有需要，您也可以使用本機 PostgreSQL 資料庫，以更好方式模擬實際執行環境。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="update-the-app"></a>更新應用程式

在 `polls/models.py` 中，找出開頭為 `choice_text` 的程式碼行，然後將 `max_length` 參數變更為 100：

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

由於您變更了資料模型，因此，請建立新的 Django 移轉，然後移轉資料庫：

```
python manage.py makemigrations
python manage.py migrate
```

使用 `python manage.py runserver` 再次執行開發伺服器，並在 *http:\//localhost:8000/admin* 上測試應用程式：

使用 **Ctrl**+**C** 再次停止 Django Web 伺服器。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="redeploy-the-code-to-azure"></a>將程式碼重新部署到 Azure

在存放庫根路徑中，執行下列命令：

```azurecli
az webapp up
```

此命令會使用 *.azure/config* 檔案中快取的參數。 由於 App Service 偵測到應用程式已經存在，因此，只會重新部署程式碼。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="rerun-migrations-in-azure"></a>在 Azure 中重新執行移轉

因為您對資料模型進行了變更，所以需要在 App Service 中重新執行資料庫移轉。

瀏覽至 `https://<app-name>.scm.azurewebsites.net/webssh/host`，再次於瀏覽器中開啟 SSH 工作階段。 然後，執行下列命令：

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="review-app-in-production"></a>檢閱生產環境中的應用程式

瀏覽至 `http://<app-name>.azurewebsites.net`，再次於生產環境中測試應用程式。 (由於您只變更了資料庫欄位的長度，因此，只有在建立問題期間嘗試輸入較長的回應時，才會察覺到此變更。)

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄

您可以存取從在 Azure 上裝載應用程式的容器內產生的主控台記錄。

執行下列 Azure CLI 命令以查看記錄資料流。 此命令會使用 *.azure/config* 檔案中快取的參數。

```azurecli
az webapp log tail
```

如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

若要隨時停止記錄資料流，請輸入 **Ctrl**+**C**。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> 您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。
>
> `az webapp up` 為您開啟預設記錄。 基於效能考量，此記錄會在一段時間後會自行關閉，但每次您再次執行 `az webapp up` 時，就會重新開啟。 若要手動開啟，請執行下列命令：
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 入口網站管理您的應用程式

在 [Azure 入口網站](https://portal.azure.com)中，搜尋應用程式名稱，然後在結果中選取應用程式。

![瀏覽至您在 Azure 入口網站中的 Python Django 應用程式](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

根據預設，入口網站會顯示您應用程式的 [概觀] 頁面，以提供一般效能檢視。 在此，您也可以執行基本管理工作，例如瀏覽、停止、重新啟動和刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![在 Azure 入口網站的 [概觀] 頁面中管理您的 Python Django 應用程式](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clean-up-resources"></a>清除資源

如果您想要保留應用程式或繼續進行下一個教學課程，請直接跳至[後續步驟](#next-steps)。 否則，若要避免產生持續費用，您可以刪除針對此教學課程建立的資源群組：

```azurecli
az group delete --no-wait
```

此命令會使用 *.azure/config* 檔案中快取的資源群組名稱。 藉由刪除資源群組，您也會解除配置並刪除其中包含的所有資源。

刪除所有資源可能需要一些時間。 `--no-wait` 引數可讓命令立即傳回。

[有任何問題嗎？請告訴我們。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>後續步驟

了解如何將自訂 DNS 名稱對應至您的應用程式：

> [!div class="nextstepaction"]
> [教學課程：將自訂 DNS 名稱對應至應用程式](app-service-web-tutorial-custom-domain.md)

了解 App Service 如何執行 Python 應用程式：

> [!div class="nextstepaction"]
> [設定 Python 應用程式](configure-language-python.md)
