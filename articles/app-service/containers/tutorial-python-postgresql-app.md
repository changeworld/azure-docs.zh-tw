---
title: 教學課程：使用 Postgres 部署 Python (Django)
description: 了解如何使用 PostgreSQL 資料庫建立 Python 應用程式，並將其部署至 Linux 上的 Azure App Service。 本教學課程會使用 Django 範例應用程式來示範。
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 504e2f7c07d8d29e4fe4dad52dc008c895517a3d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609777"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>教學課程：在 Azure App Service 中透過 PostgreSQL 部署 Python (Django) Web 應用程式

本教學課程說明如何將資料驅動的 Python (Django) Web 應用程式部署至 [Azure App Service](app-service-linux-intro.md)，並連線到適用於 PostgreSQL 的 Azure 資料庫。 App Service 提供可高度擴充、自我修復的 Web 裝載服務。

![將 Python Django Web 應用程式部署到 Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立「適用於 PostgreSQL 的 Azure 資料庫」資料庫
> * 將程式碼部署至 Azure App Service 並連線至 Postgres
> * 更新您的程式碼並重新部署
> * 檢視診斷記錄
> * 在 Azure 入口網站中管理 Web 應用程式

您可以在 macOS、Linux 或 Windows 上依照本文中的步驟操作。

## <a name="install-dependencies"></a>安裝相依性

開始進行本教學課程之前，請先：

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- 安裝 [Azure CLI](/cli/azure/install-azure-cli)。
- 安裝 [Git](https://git-scm.com/)。
- 安裝 [Python 3](https://www.python.org/downloads/)。

## <a name="clone-the-sample-app"></a>複製範例應用程式

在終端機視窗中執行下列命令，以複製範例應用程式存放庫，並變更至儲存機制根路徑：

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

djangoapp 範例存放庫包含資料驅動的 [Django](https://www.djangoproject.com/) 輪詢應用程式，您可以依照 Django 文件中的[撰寫您的第一個 Django 應用程式](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)提供的指示加以取得。 提供此資訊以方便您使用。

## <a name="prepare-app-for-app-service"></a>準備適用於 App Service 的應用程式

如同許多 Python Web 架構，Django [需要進行某些變更，才能在生產伺服器中執行](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)，而且與 App Service 並無不同。 您必須在預設 azuresite/settings.py  檔案中變更並新增一些設定，讓應用程式在部署至 App Service 之後能夠運作。 

請參閱 azuresite/production.py  ，這會為 App Service 進行必要的組態設定。 基本上會執行下列設定：

- 從 azuresite/settings.py  繼承所有設定。
- 將 App Service 應用程式的完整網域名稱新增至允許的主機。 
- 使用 [WhiteNoise](https://whitenoise.evans.io/en/stable/) 以在生產環境中提供靜態檔案，因為 Django 預設不會在生產環境中提供靜態檔案。 WhiteNoise 套件已包含在 requirements.txt  中。
- 新增適用於 PostgreSQL 資料庫的組態。 根據預設，Django 會使用 Db.sqlite3 作為資料庫，但不適用於生產應用程式。 [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) 套件已包含在 requirements.txt  中。
- Postgres 組態會使用環境變數。 稍後，您將了解如何在 App Service 中設定環境變數。

azuresite/production.py  包含在存放庫中以方便使用，但應用程式尚未使用。 為確保在 App Service 中使用其設定，您需要設定兩個檔案，分別是 manage.py  和 azuresite/wsgi.py  才能存取。

- 在 manage.py  中變更以下程式碼行：

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    變更為下列程式碼：

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    稍後當您設定 App Service 應用程式時，將會設定 `DJANGO_ENV` 環境變數。

- 在 azuresite/wsgi.py  中，進行相同的變更。

    在 App Service 中，您可以使用 manage.py  來執行資料庫移轉，App Service 會使用 azuresite/wsgi.py  ，在生產環境中執行您的 Django 應用程式。 這兩個檔案中進行的這項變更可確保在這兩種情況下都會使用生產環境設定。

## <a name="sign-in-to-azure-cli"></a>登入 Azure CLI

您應該已經安裝 Azure CLI。 [Azure CLI](/cli/azure/what-is-azure-cli) 可讓您從命令列終端機使用 Azure 資源。 

要登入 Azure，請執行 [`az login`](/cli/azure/reference-index#az-login) 命令：

```azurecli
az login
```

依照終端機中的指示登入您的 Azure 帳戶。 當您完成時，訂用帳戶會以 JSON 格式顯示在終端機輸出中。

## <a name="create-postgres-database-in-azure"></a>在 Azure 中建立 Postgres 資料庫

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

在本節中，您會建立「適用於 PostgreSQL 的 Azure 資料庫」伺服器和資料庫。 若要啟動，請執行下列命令安裝 `db-up` 延伸模組：

```azurecli
az extension add --name db-up
```

使用 [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) 命令建立 Postgres 資料庫，如下列範例所示。 以「唯一」  名稱取代 \<postgresql-name>  (伺服器端點是 https://\<postgresql-name>.postgres.database.azure.com  )。 針對 \<admin-username>  和 \<admin-password>  ，請指定認證以建立此 Postgres 伺服器的管理員使用者。

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

此命令可能需要一些時間，因為會執行下列動作：

- 建立稱為 `myResourceGroup` 的[資源群組](../../azure-resource-manager/management/overview.md#terminology) (如果不存在的話)。 每個 Azure 資源都必須具備其中一項。 `--resource-group` 是選擇性的。
- 建立具有管理使用者的 Postgres 伺服器。
- 建立 `pollsdb` 資料庫。
- 允許從您的本機 IP 位址進行存取。
- 允許從 Azure 服務存取。
- 建立資料庫使用者，並提供 `pollsdb` 資料庫的存取權。

您可以使用其他 `az postgres` 命令和 `psql` 分別執行所有步驟，但 `az postgres up` 會在一個步驟中全部完成。

當命令完成時，尋找 `Ran Database Query:` 的輸出行。 這些程式碼行會顯示為您建立的資料庫使用者，並包含使用者名稱 `root` 和密碼 `Pollsdb1`。 稍後您會使用這些資料將應用程式連線到資料庫。

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`，可以設定為任何一個 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。 您可以使用 [`az account list-locations`](/cli/azure/account#az-account-list-locations) 命令，取得您訂用帳戶可用的區域。 針對生產應用程式，請將您的資料庫和應用程式放在相同的位置。

## <a name="deploy-the-app-service-app"></a>部署至 App Service 應用程式

在本節中，您會建立 App Service 應用程式。 您會將此應用程式連線到您所建立的 Postgres 資料庫，並部署您的程式碼。

### <a name="create-the-app-service-app"></a>建立 App Service 應用程式

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

請確定您已回到儲存機制根路徑 (`djangoapp`)，因為應用程式將會從這個目錄進行部署。

使用 [`az webapp up`](/cli/azure/webapp#az-webapp-up) 命令建立 App Service 應用程式，如下列範例所示。 以「唯一」  名稱取代 \<app-name>  (伺服器端點為 https://\<app-name>.azurewebsites.net  )。 \<app-name>  的有效字元為 `A`-`Z`、`0`-`9` 和 `-`。

```azurecli
az webapp up --plan myAppServicePlan --location westus2 --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

此命令可能需要一些時間，因為會執行下列動作：

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- 自動產生[資源群組](../../azure-resource-manager/management/overview.md#terminology)。
- 在基本定價層 (B1) 中建立 [App Service 方案](../overview-hosting-plans.md) *myAppServicePlan* (如果不存在)。 `--plan` 和 `--sku` 是選擇性的。
- 建立 App Service 應用程式 (如果不存在)。
- 啟用應用程式的預設記錄 (如果尚未啟用)。
- 使用已啟用組建自動化的 ZIP 部署來上傳存放庫。

部署完成後，您會看到如下所示的 JSON 輸出：

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

複製 \<app-resource-group>  的值。 您稍後需要用來設定應用程式。 

> [!TIP]
> 相關的設定會儲存到您存放庫中隱藏的 .azure  目錄。 您稍後可以使用簡單的命令來重新部署任何變更，並立即啟用診斷記錄：
> 
> ```azurecli
> az webapp up
> ```

範例程式碼現在已部署完成，但應用程式尚未連線到 Azure 中的 Postgres 資料庫。 您接下來將執行該作業。

### <a name="configure-environment-variables"></a>設定環境變數

在本機執行應用程式時，您可以在終端機工作階段中設定環境變數。 在 App Service 中，您可以使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令，使用 app settings  設定環境變數。

執行下列命令，將資料庫連線詳細資料指定為應用程式設定。 請將 \<app-name>  、\<app-resource-group>  和 \<postgresql-name>  取代為您自己的值。 請記住，`az postgres up` 會為您建立使用者認證 `root` 和 `Pollsdb1`。

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

如需程式碼如何存取這些應用程式設定的詳細資訊，請參閱[存取環境變數](how-to-configure-python.md#access-environment-variables)。

### <a name="run-database-migrations"></a>執行資料庫移轉

若要在 App Service 中執行資料庫移轉，請在瀏覽器中開啟 SSH 工作階段，方法是瀏覽至 https://\<app-name>.scm.azurewebsites.net/webssh/host  ：

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

在 SSH 工作階段中執行下列命令：

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>瀏覽至 Azure 應用程式

在瀏覽器中透過 URL http:\//\<app-name>.azurewebsites.net  瀏覽至已部署的應用程式。 您應該會看到**沒有可用的輪詢**訊息。 

瀏覽至 http:\//\<app-name>.azurewebsites.net/admin  ，然後使用您在上一個步驟中建立的管理使用者進行登入。 選取 [問題]  旁的 [新增]  ，然後建立具有一些選項的投票問題。

透過 URL http:\//\<app-name>.azurewebsites.net/admin  瀏覽至已部署的應用程式，再建立一些輪詢問題。 您可以在 http:\//\<app-name>.azurewebsites.net/  中查看問題。 

![在 Azure App Service 中執行 Python Django 應用程式](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

再次透過 URL http:\//\<app-name>.azurewebsites.net  瀏覽至已部署的應用程式，建立一些輪詢問題並回答問題。

App Service 會藉由尋找每個子目錄中的 wsgi.py  檔案 (依預設會由 `manage.py startproject` 建立)，來偵測存放庫中的 Django 專案。 App Service 找到此檔案時，就會載入 Django Web 應用程式。 如需 App Service 如何載入 Python 應用程式的詳細資訊，請參閱[設定內建 Python 映像](how-to-configure-python.md)。

**恭喜！** 您正在適用於 Linux 的 Azure App Service 中執行 Python (Django) Web 應用程式。

## <a name="develop-app-locally-and-redeploy"></a>在本機開發應用程式並重新部署

在本節中，您會在本機環境中開發應用程式，並將您的程式碼重新部署至 App Service。

### <a name="set-up-locally-and-run"></a>在本機設定並執行

若要設定您的本機開發環境並第一次執行範例應用程式，請執行下列命令：

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

```CMD
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

Django Web 應用程式完全載入後，會傳回如下的訊息：

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

在瀏覽器中移至 *http:\//localhost:8000*。 您應該會看到**沒有可用的輪詢**訊息。 

移至 *http:\//localhost:8000/admin*，然後使用您在上一個步驟中建立的管理使用者進行登入。 選取 [問題]  旁的 [新增]  ，然後建立具有一些選項的投票問題。

![在本機 App Service 中執行 Python Django 應用程式](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

再次移至 *http:\//localhost:8000*，以查看輪詢問題並回答問題。 本機 Django 範例應用程式會將使用者資料寫入並儲存到本機 Db.sqlite3 資料庫，因此您不需要擔心會混淆產環境資料庫。 若要讓您的開發環境符合 Azure 環境，請考慮改為在本機使用 Postgres 資料庫。

如需停止 Django 伺服器，請輸入 Ctrl+C。

### <a name="update-the-app"></a>更新應用程式

若要查看如何讓應用程式更新運作，請在 `polls/models.py` 中進行小幅變更。 請尋找以下這行︰

<pre>
choice_text = models.CharField(max_length=200)
</pre>

變更為：

```python
choice_text = models.CharField(max_length=100)
```

藉由變更資料模型，您必須建立新的 Django 移轉。 使用下列命令來進行：

```
python manage.py makemigrations
```

您可以執行移轉、執行程式開發伺服器，然後瀏覽至 http:\//localhost:8000/admin  ，在本機測試您的變更：

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>將程式碼重新部署至 Azure

若要重新部署變更，請從儲存機制根路徑執行下列命令：

```azurecli
az webapp up
```

App Service 會偵測到應用程式存在，而且只會部署程式碼。

### <a name="rerun-migrations-in-azure"></a>在 Azure 中重新執行移轉

因為您對資料模型進行了變更，所以需要在 App Service 中重新執行資料庫移轉。 請在瀏覽器中開啟 SSH 工作階段，方法是瀏覽至 https://\<app-name>.scm.azurewebsites.net/webssh/host  。 執行下列命令：

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>檢閱生產環境中的應用程式

瀏覽至 http:\//\<app-name>.azurewebsites.net  並查看生產環境中即時執行的變更。 

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄

您可以存取從容器產生的主控台記錄。

> [!TIP]
> `az webapp up` 為您開啟預設記錄。 基於效能考量，此記錄會在一段時間後會自行關閉，但每次您再次執行 `az webapp up` 時，就會重新開啟。 若要手動開啟，請執行下列命令：
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

執行下列 Azure CLI 命令以查看記錄串流：

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

> [!NOTE]
> 您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。

若要隨時停止記錄資料流，請輸入 `Ctrl`+`C`。

## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 入口網站管理您的應用程式

在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取您所建立的應用程式。

![瀏覽至您在 Azure 入口網站中的 Python Django 應用程式](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

根據預設，入口網站會顯示應用程式的 [概觀]  頁面。 此頁面可讓您檢視應用程式的執行方式。 在此，您也可以執行基本管理工作，例如瀏覽、停止、重新啟動和刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![在 Azure 入口網站的 [概觀] 頁面中管理您的 Python Django 應用程式](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>清除資源

如果您在未來不需使用這些資源，請執行下列命令來刪除資源群組：

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 建立「適用於 PostgreSQL 的 Azure 資料庫」資料庫
> * 將程式碼部署至 Azure App Service 並連線至 Postgres
> * 更新您的程式碼並重新部署
> * 檢視診斷記錄
> * 在 Azure 入口網站中管理 Web 應用程式

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至應用程式：

> [!div class="nextstepaction"]
> [教學課程：將自訂 DNS 名稱對應至應用程式](../app-service-web-tutorial-custom-domain.md)

或者，查看其他資源：

> [!div class="nextstepaction"]
> [設定 Python 應用程式](how-to-configure-python.md)
