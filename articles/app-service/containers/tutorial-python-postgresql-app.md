---
title: 教學課程：使用 Postgre 的 Linux Python 應用程式
description: 了解如何藉由連線至 Azure 中的 PostgreSQL 資料庫，讓 Linux Python 應用程式在 Azure App Service 中運作。 本教學課程中使用 Django。
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523916"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>教學課程：在 Azure App Service 中透過 PostgreSQL 執行 Python (Django) Web 應用程式

[Azure App Service](app-service-linux-intro.md) 可提供可高度擴充、自我修復的 Web 主控服務。 本教學課程說明如何將資料驅動的 Python Django Web 應用程式連線至「適用於 PostgreSQL 的 Azure 資料庫」資料庫，並在 Azure App Service 上部署和執行應用程式。

![Azure App Service 中的 Python Django Web 應用程式](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立「適用於 PostgreSQL 的 Azure 資料庫」資料庫，並將 Web 應用程式連線至該處
> * 將 Web 應用程式部署至 Azure App Service
> * 檢視診斷記錄
> * 在 Azure 入口網站中管理 Web 應用程式

您可以在 macOS、Linux 或 Windows 上依照本文中的步驟操作。 這些步驟在多數情況下都很類似，但在本教學課程中不會詳述其差異。 下列大部分的範例都會使用 Linux 上的 `bash` 終端機視窗。 

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，請先：

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- 安裝 [Git](https://git-scm.com/)。
- 安裝 [Python 3](https://www.python.org/downloads/)。
- 安裝並執行 [PostgreSQL](https://www.postgresql.org/download/)。

## <a name="test-postgresql-installation-and-create-a-database"></a>測試 PostgreSQL 安裝並建立資料庫

首先，連線至您的本機 PostgreSQL 伺服器，並建立資料庫： 

在本機終端機視窗中，以內建 `postgres` 使用者身分執行 `psql`，以連線至本機 PostgreSQL 伺服器。

```bash
sudo su - postgres
psql
```
或
```PowerShell
psql -U postgres
```

如果連線成功，則您的 PostgreSQL 資料庫就已在執行中。 如果沒有，請確定本機 PostgresQL 資料庫已遵循 [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (下載 - PostgresQL 核心散發) 中針對您作業系統的指示來啟動。

建立名為 pollsdb  的新資料庫，並設定名為 manager  、密碼為 supersecretpass  的資料庫使用者：

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

輸入 `\q` 來結束 PostgreSQL 用戶端。

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>建立並執行本機 Python 應用程式

接著，設定並執行範例 Python Django Web 應用程式。

[djangoapp](https://github.com/Azure-Samples/djangoapp) 範例存放庫包含資料驅動的 [Django](https://www.djangoproject.com/) 輪詢應用程式，您可以依照 Django 文件中的[撰寫您的第一個 Django 應用程式](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)提供的指示加以取得。

### <a name="clone-the-sample-app"></a>複製範例應用程式

在終端機視窗中執行下列命令，以複製範例應用程式存放庫，並切換至新的工作目錄：

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>設定 Python 虛擬環境

建立並啟用 Python 虛擬環境，以執行您的應用程式。

```bash
python3 -m venv venv
source venv/bin/activate
```
或
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

在 `venv` 環境中執行 *env.sh* 或 *env.ps1*，以設定 *azuresite/settings.py* 將用於資料庫連線設定的環境變數。

```bash
source ./env.sh
```
或
```PowerShell
.\env.ps1
```

從 *requirements.txt* 安裝所需的套件、執行 [Django 移轉](https://docs.djangoproject.com/en/2.1/topics/migrations/)，並[建立管理使用者](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)：

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>執行 Web 應用程式

建立管理使用者後，請執行 Django 伺服器。

```bash
python manage.py runserver
```

Django Web 應用程式完全載入後，會傳回如下的訊息：

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

在瀏覽器中移至 *http:\//localhost:8000*。 您應該會看到**沒有可用的輪詢**訊息。 

移至 *http:\//localhost:8000/admin*，然後使用您在上一個步驟中建立的管理使用者進行登入。 選取 [問題]  旁的 [新增]  ，然後建立具有一些選項的投票問題。

![在本機 App Service 中執行 Python Django 應用程式](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

再次移至 *http:\//localhost:8000*，以查看輪詢問題並回答問題。 本機 Django 範例應用程式會將使用者資料寫入本機 PostgreSQL 資料庫，並加以儲存。

如需停止 Django 伺服器，請在終端機上輸入 Ctrl+C。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

本文中的其餘步驟大多會使用 Azure Cloud Shell 中 Azure CLI 命令。 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>建立適用於 PostgreSQL 的 Azure 資料庫並連線至該處

在本節中，您會建立「適用於 PostgreSQL 的 Azure 資料庫」伺服器和資料庫，並將您的 Web 應用程式連線至該處。 當您將 Web 應用程式部署至 Azure App Service 時，應用程式就會使用此雲端資料庫。 

### <a name="create-a-resource-group"></a>建立資源群組

您可以為適用於 PostgreSQL 的 Azure 資料庫伺服器建立新的資源群組，或使用現有的資源群組。 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

您可以在 Cloud Shell 中使用 [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 命令建立 PostgreSQL 伺服器。

> [!NOTE]
> 在建立適用於 PostgreSQL 的 Azure 資料庫伺服器之前，請先確認可在您的區域中使用的[計算世代](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores)。 如果您的區域不支援第 4 代硬體，請將下列命令列中的 *--sku-name* 變更為您的區域中支援的值，例如第 5 代。 

在下列命令中，請將 *\<postgresql-name>* 取代為唯一的伺服器名稱。 此伺服器名稱是您的 PostgreSQL 端點 *https://\<postgresql-name>.postgres.database.azure.com* 的一部分，因此在 Azure 中的所有伺服器間必須是唯一的名稱。 

將 *\<resourcegroup-name>* 和 *\<region>* 取代為您要使用之資源群組的名稱和區域。 針對 *\<admin-username>* 和 *\<admin-password>* ，請建立資料庫管理員帳戶的使用者認證。 請記下 *\<admin-username>* 和 *\<admin-password>* ，以便稍後用來登入 PostgreSQL 伺服器和資料庫。

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

建立適用於 PostgreSQL 的 Azure 資料庫伺服器後，Azure CLI 會傳回類似於下列範例的 JSON 程式碼：

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器防火牆規則

執行 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) 命令，以允許從 Azure 資源存取資料庫。 請將 *\<postgresql-name>* 和 *\<resourcegroup-name>* 預留位置取代為您自己的值。

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> 前述設定允許來自 Azure 網路內所有 IP 位址的網路連線。 針對生產用途，請藉由[僅允許您的應用程式所使用的輸出 IP 位址](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)，盡可能設定最嚴格的防火牆規則。

再次執行 `firewall-rule create` 命令，以允許從本機電腦進行存取。 請將 *\<your-ip-address>* 取代為[您的本機 IPv4 IP 位址](https://www.whatsmyip.org/)。 將 *\<postgresql-name>* 和 *\<resourcegroup-name>* 預留位置取代為您自己的值。

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>建立「適用於 PostgreSQL 的 Azure 資料庫」資料庫並連線至該處

執行下列命令，連線至適用於 PostgreSQL 的 Azure 資料庫伺服器。 使用您自己的 *\<postgresql-name>* 和 *\<admin-username>* ，並使用您建立的密碼登入。

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

比照您在本機 PostgreSQL 伺服器中的操作，在適用於 PostgreSQL 的 Azure 資料庫伺服器中建立資料庫和使用者：

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> 最佳做法是建立具有特定應用程式限制權限的資料庫使用者，而不是使用管理使用者。 `manager` 使用者「只」  具有 `pollsdb` 資料庫的完整權限。

輸入 `\q` 來結束 PostgreSQL 用戶端。

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>測試應用程式與 Azure PostgreSQL 資料庫的連線

編輯您的本機 *env.sh* 或 *env.ps1* 檔案，將 *\<postgresql-name>* 取代為適用於 PostgreSQL 的 Azure 資料庫伺服器名稱，以指向您的雲端 PostgreSQL 資料庫。

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
或
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

在本機終端機視窗的 `venv` 環境中，執行編輯過的 *env.sh* 或 *env.ps1*。 
```bash
source ./env.sh
```
或
```PowerShell
.\env.ps1
```

將 Django 遷移至 Azure 資料庫，並建立管理使用者。

```bash
python manage.py migrate
python manage.py createsuperuser
```

建立了管理使用者之後，請執行 Django 伺服器。

```bash
python manage.py runserver
```

在瀏覽器中移至 *http:\//localhost:8000*，您應該會再次看到**沒有可用的輪詢**訊息。 

移至 *http:\//localhost:8000/admin*，然後使用您建立的管理使用者登入，並建立如同前面的投票問題。

![在本機 App Service 中執行 Python Django 應用程式](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

再次移至 *http:\//localhost:8000*，然後查看顯示的投票問題。 您的應用程式此時會將資料寫入至「適用於 PostgreSQL 的 Azure 資料庫」資料庫。

如需停止 Django 伺服器，請在終端機上輸入 Ctrl+C。

## <a name="deploy-the-web-app-to-azure-app-service"></a>將 Web 應用程式部署至 Azure App Service

在此步驟中，您會將與「適用於 PostgreSQL 的 Azure 資料庫」資料庫連線的 Python 應用程式部署至 Azure App Service。

### <a name="configure-repository"></a>設定存放庫

由於本教學課程使用 Django 範例，因此您必須在 *djangoapp/azuresite/settings.py* 檔案中變更並新增一些設定，才能在 Azure App Service 中使用。 

1. Django 會驗證連入要求中的 `HTTP_HOST` 標題。 若要讓 Django Web 應用程式在 App Service 中運作，您必須將應用程式的完整網域名稱新增至允許的主機。 
   
   編輯 *azuresite/settings.py* 以變更 `ALLOWED_HOSTS` 行，如下所示：
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django 不支援[在生產環境中提供靜態檔案](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)。 在本教學課程中，您會使用 [WhiteNoise](https://whitenoise.evans.io/en/stable/) 來提供檔案。 WhiteNoise 套件已透過 *requirements.txt* 進行安裝。 
   
   若要將 Django 設定為使用 WhiteNoise，請在 *azuresite/settings.py* 中找出 `MIDDLEWARE` 設定，並將 `whitenoise.middleware.WhiteNoiseMiddleware` 新增至清單，緊接在 `django.middleware.security.SecurityMiddleware` 一行後面。 `MIDDLEWARE` 設定應該如下所示：
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. 在 azuresite/settings.py  結尾處新增以下幾行：
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   如需如何設定 WhiteNoise 的詳細資訊，請參閱 [WhiteNoise 文件](https://whitenoise.evans.io/en/stable/)。

> [!IMPORTANT]
> [資料庫設定] 區段已遵循使用環境變數的安全性最佳做法。 如需完整的部署建議，請參閱 [Django 文件：部署檢查清單](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)。

將變更認可至您 *djangoapp* 存放庫的分支中：

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>建立 App Service 方案

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>建立 Web 應用程式

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>設定環境變數

稍早在本教學課程中，您定義了環境變數來連線至 PostgreSQL 資料庫。

在 Azure App Service 中，您可以使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令將環境變數設定為「應用程式設定」  。

在 Azure Cloud Shell 中執行下列命令，將資料庫連線詳細資料指定為應用程式設定。 請將 *\<app-name>* 、 *\<resourcegroup-name>* 和 *\<postgresql-name>* 取代為您自己的值。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

如需程式碼如何存取這些應用程式設定的詳細資訊，請參閱[存取環境變數](how-to-configure-python.md#access-environment-variables)。

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

App Service 部署伺服器會在存放庫的根目錄看到 requirements.txt  ，並在 `git push` 之後自動執行 Python 套件管理。

### <a name="browse-to-the-azure-app"></a>瀏覽至 Azure 應用程式

透過 URL *http:\//\<app-name>.azurewebsites.net* 瀏覽至已部署的應用程式。 啟動作業需要一些時間，因為在第一次要求應用程式時必須下載並執行容器。 如果頁面逾時或顯示錯誤訊息，請稍候幾分鐘並重新整理頁面。

您應該會看到您稍早建立的投票問題。 

App Service 會藉由尋找每個子目錄中的 wsgi.py  檔案 (依預設會由 `manage.py startproject` 建立)，來偵測存放庫中的 Django 專案。 App Service 找到此檔案時，就會載入 Django Web 應用程式。 如需 App Service 如何載入 Python 應用程式的詳細資訊，請參閱[設定內建 Python 映像](how-to-configure-python.md)。

移至 *http:\//\<app-name>.azurewebsites.net/admin*，然後使用您建立的管理使用者進行登入。 如有需要，您可以再建立一些投票問題。

![在 Azure App Service 中執行 Python Django 應用程式](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**恭喜！** 您正在適用於 Linux 的 Azure App Service 中執行 Python (Django) Web 應用程式。

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 入口網站管理您的應用程式

在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取您所建立的應用程式。

![瀏覽至您在 Azure 入口網站中的 Python Django 應用程式](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

根據預設，入口網站會顯示應用程式的 [概觀]  頁面。 此頁面可讓您檢視應用程式的執行方式。 在此，您也可以執行基本管理工作，例如瀏覽、停止、重新啟動和刪除。 分頁左側的索引標籤會顯示您可開啟的各種設定分頁。

![在 Azure 入口網站的 [概觀] 頁面中管理您的 Python Django 應用程式](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至應用程式：

> [!div class="nextstepaction"]
> [教學課程：將自訂 DNS 名稱對應至應用程式](../app-service-web-tutorial-custom-domain.md)

或者，查看其他資源：

> [!div class="nextstepaction"]
> [設定 Python 應用程式](how-to-configure-python.md)
