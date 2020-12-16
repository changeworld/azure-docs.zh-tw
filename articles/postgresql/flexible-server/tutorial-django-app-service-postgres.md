---
title: 教學課程：在虛擬網路中使用 App Service 和適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器 (預覽) 部署 Django 應用程式
description: 在虛擬網路中使用 App Service 和適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器 (預覽) 部署 Django 應用程式
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490096"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>教學課程：使用 App Service 和適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器 (預覽) 部署 Django 應用程式

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

在本教學課程中，您會了解如何在虛擬網路中使用應用程式服務 和適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器，在 Azure 中部署 Django 應用程式。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

本文需要您以本機方式執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須使用 [az login](/cli/azure/authenticate-azure-cli) 命令登入您的帳戶。 請記下命令輸出中的 **識別碼** 屬性，以取得對應的訂用帳戶名稱。

```azurecli
az login
```

如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [az account set](/cli/azure/account) 命令來選取您帳戶底下的特定訂用帳戶 ID。 以訂用帳戶之 **az login** 輸出中的 **訂用帳戶識別碼** 屬性，替代訂用帳戶識別碼的預留位置。

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>複製或下載範例應用程式

# <a name="git-clone"></a>[Git 複製](#tab/clone)

複製範例存放庫：

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

然後移至該資料夾中：

```terminal
cd djangoapp
```

# <a name="download"></a>[下載](#tab/download)

瀏覽 [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp)、選取 [複製]，然後選取 [下載 ZIP]。

將 ZIP 檔案解壓縮至名為 *djangoapp* 的資料夾。

然後，在該 *djangoapp* 資料夾中開啟終端視窗。

---

djangoapp 範例包含資料驅動的 Django 投票應用程式，您可以依照 Django 文件中的[撰寫您的第一個 Django 應用程式](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) \(英文\) 提供的指示加以取得。 這裡提供完整的應用程式，以方便您使用。

此範例也會修改為在實際執行環境中執行，例如 App Service：

- 實際執行設定位於 *azuresite/production.py* 檔案中。 開發詳細資料位於 *azuresite/settings.py*。
- 將 `DJANGO_ENV` 環境變數設定為 "production" 時，應用程式就會使用實際執行設定。 您稍後會在教學課程中建立此環境變數，以及用於 PostgreSQL 資料庫設定的其他變數。

這些變更專門用來將 Django 設定為在任何實際執行環境中執行，而不是特別針對 App Service。 如需詳細資訊，請參閱 [Django 部署檢查清單](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) \(英文\)。

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>在虛擬網路中建立 PostgreSQL 彈性伺服器

使用下列命令，在虛擬網路 (VNET) 中建立私人彈性伺服器和資料庫：
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

此命令會執行下列動作，這可能需要幾分鐘的時間：

- 建立資源群組 (若尚不存在)。
- 若未提供，系統會產生伺服器名稱。
- 為新的 postgreSQL 伺服器建立新的虛擬網路。 **記下為伺服器建立的虛擬網路名稱和子網路名稱**，因為您必須將 Web 應用程式新增至相同的虛擬網路。
- 建立伺服器的管理使用者名稱、密碼 (如果未提供)。 將後續步驟會用到的 **使用者名稱和密碼記下**。
- 建立可用於開發的資料庫 ```postgres```。 您可以執行 [**psql** 以連線到資料庫](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql)，建立不同的資料庫。

> [!NOTE]
> 記下您的密碼，如果未提供，系統會產生密碼。 如果忘記密碼，就必須使用 ``` az postgres flexible-server update``` 命令重設


## <a name="deploy-the-code-to-azure-app-service"></a>將程式碼部署到 Azure App Service

在此節中，您會在 App Service 應用程式中建立應用程式主機、將此應用程式連線到 Postgres 資料庫，然後將程式碼部署到該主機。


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>在虛擬網路中建立 App Service Web 應用程式

在終端中，確定您位於包含應用程式程式碼的存放庫根路徑 (`djangoapp`)。

使用 [`az webapp up`](/cli/azure/webapp#az-webapp-up) 命令來建立 App Service 應用程式 (主機處理序)：

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- 針對 `--location` 引數，使用與您在上一節針對資料庫所做的相同位置。
- 使用所有 Azure 中的唯一名稱取代 *\<app-name>* (伺服器端點為 `https://\<app-name>.azurewebsites.net`)。 *\<app-name>* 的有效字元是 `A`-`Z`、`0`-`9` 和 `-`。 良好的模式是使用您的公司名稱和應用程式識別碼的組合。
- 在基本定價層 (B1) 中建立 [App Service 方案](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* (如果不存在)。 `--plan` 和 `--sku` 是選擇性的。
- 建立 App Service 應用程式 (如果不存在)。
- 啟用應用程式的預設記錄 (如果尚未啟用)。
- 使用已啟用建置自動化的 ZIP 部署來上傳存放庫。
- **az webapp vnet-integration** 命令會在與 postgres 伺服器相同的虛擬網路中新增 Web 應用程式。
- 應用程式程式碼預期會在數個環境變數中尋找資料庫資訊。 若要在 App Service 中設定環境變數，您可以使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令來建立「應用程式設定」。

> [!TIP]
> 許多 Azure CLI 命令會將一般參數 (例如，資源群組的名稱和 App Service 方案) 快取到檔案 *.azure/config*。因此，您不需使用後續命令來指定所有相同參數。 例如，若要在進行變更之後重新部署應用程式，只需再次執行 `az webapp up`，而不需任何參數。

### <a name="run-django-database-migrations"></a>執行 Django 資料庫移轉

Django 資料庫移轉可確保 Azure 資料庫上 PostgreSQL 中的結構描述符合您程式碼中所述的結構描述。

1. 瀏覽至 *https://\<app-name>.scm.azurewebsites.net/webssh/host* 並使用您的 Azure 帳戶認證 (而非資料庫伺服器認證) 登入，以在瀏覽器中開啟 SSH 工作階段。

1. 在 SSH 工作階段中，執行下列命令 (您可以使用 **Ctrl**+**Shift**+**V** 來貼上命令)：

    ```bash
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

1. `createsuperuser` 命令會提示您輸入超級使用者認證。 基於此教學課程的目的，請使用預設的使用者名稱 `root`、按 **Enter** 以讓電子郵件地址保留空白，然後輸入 `postgres1` 作為密碼。

### <a name="create-a-poll-question-in-the-app"></a>在應用程式中建立投票問題

1. 在瀏覽器中，開啟 URL *http:\//\<app-name>.azurewebsites.net*。 應用程式應該會顯示訊息「沒有可用的投票」，因為資料庫中尚未有特定的投票。

1. 瀏覽至 *http:\//\<app-name>.azurewebsites.net/admin*。使用上一節的超級使用者認證 (`root` 和 `postgres1`) 登入。 在 [投票] 下方，選取 [問題] 旁的 [新增]，然後建立具有一些選項的投票問題。

1. 再次瀏覽至 *http:\//\<app-name>.azurewebsites.net/* ，以確認現在可向使用者呈現問題。 回答問題，但是您想要在資料庫中產生一些資料。

**恭喜！** 您正在適用於 Linux 的 Azure App Service 中執行 Python Django Web 應用程式，其中具有作用中的 Postgres 資料庫。

> [!NOTE]
> App Service 會藉由尋找每個子資料夾中的 *wsgi.py* 檔案 (依預設會由 `manage.py startproject` 建立)，來偵測 Django 專案。 當 App Service 找到該檔案時，就會載入 Django Web 應用程式。 如需詳細資訊，請參閱[設定內建的 Python 映像](../../app-service/configure-language-python.md)。

## <a name="make-code-changes-and-redeploy"></a>進行程式碼變更並重新部署

在此節中，您會對應用程式進行本機變更，然後將程式碼重新部署到 App Service。 在此程序中，您會設定支援進行中工作的 Python 虛擬環境。

### <a name="run-the-app-locally"></a>在本機執行應用程式

在終端機視窗中，執行下列命令。 建立超級使用者時，請務必遵循提示：

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
完整載入 Web 應用程式之後，Django 開發伺服器會在下列訊息中提供本機應用程式 URL：「正在 http://127.0.0.1:8000/ 中啟動開發伺服器。 請使用 CTRL-BREAK 結束伺服器」。

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="範例 Django 開發伺服器輸出":::

使用下列步驟，在本機測試應用程式：

1. 在瀏覽器中移至 *http:\//localhost:8000*，應該會顯示「沒有可用的投票」訊息。

1. 移至 *http:\//localhost:8000/admin*，然後使用您先前建立的管理使用者進行登入。 在 [投票] 下方，再次選取 [問題] 旁的 [新增]，然後建立具有一些選項的投票問題。

1. 再次移至 *http:\//localhost:8000*，然後回答問題以測試應用程式。

1. 按 **Ctrl**+**C** 以停止 Django 伺服器。

在本機執行時，應用程式會使用本機 Sqlite3 資料庫，而且不會干擾您的實際執行資料庫。 如有需要，您也可以使用本機 PostgreSQL 資料庫，以更好方式模擬實際執行環境。



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


### <a name="redeploy-the-code-to-azure"></a>將程式碼重新部署到 Azure

在存放庫根路徑中，執行下列命令：

```azurecli
az webapp up
```

此命令會使用 *.azure/config* 檔案中快取的參數。 由於 App Service 偵測到應用程式已經存在，因此，只會重新部署程式碼。



### <a name="rerun-migrations-in-azure"></a>在 Azure 中重新執行移轉

因為您對資料模型進行了變更，所以需要在 App Service 中重新執行資料庫移轉。

瀏覽至 *https://\<app-name>.scm.azurewebsites.net/webssh/host*，再次於瀏覽器中開啟 SSH 工作階段。 然後，執行下列命令：

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>檢閱生產環境中的應用程式

瀏覽至 *http:\//\<app-name>.azurewebsites.net*，再次於實際執行環境中測試應用程式。 (因為您只變更了資料庫欄位的長度，所以，只有當您在建立問題時嘗試輸入較長的回應時，此變更才會引起注意)。

> [!TIP]
> 您可以使用 [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) 來存放 Azure 儲存體中的靜態和媒體資產。 您可以使用 Azure CDN 將靜態檔案解壓縮。


## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 入口網站管理您的應用程式

在 [Azure 入口網站](https://portal.azure.com)中，搜尋應用程式名稱，然後在結果中選取應用程式。

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="瀏覽至您在 Azure 入口網站中的 Python Django 應用程式":::

根據預設，入口網站會顯示您應用程式的 [概觀] 頁面，以提供一般效能檢視。 在此，您也可以執行基本管理工作，例如瀏覽、停止、重新啟動和刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="在 Azure 入口網站的 [概觀] 頁面中管理您的 Python Django 應用程式":::


## <a name="clean-up-resources"></a>清除資源

如果您想要保留應用程式或繼續進行下一個教學課程，請直接跳至[後續步驟](#next-steps)。 否則，若要避免產生持續費用，您可以刪除針對此教學課程建立的資源群組：

```azurecli
az group delete -g myresourcegroup
```

此命令會使用 *.azure/config* 檔案中快取的資源群組名稱。 藉由刪除資源群組，您也會解除配置並刪除其中包含的所有資源。

## <a name="next-steps"></a>後續步驟

了解如何將自訂 DNS 名稱對應至您的應用程式：

> [!div class="nextstepaction"]
> [教學課程：將自訂 DNS 名稱對應至應用程式](../../app-service/app-service-web-tutorial-custom-domain.md)

了解 App Service 如何執行 Python 應用程式：

> [!div class="nextstepaction"]
> [設定 Python 應用程式](../../app-service/configure-language-python.md)
