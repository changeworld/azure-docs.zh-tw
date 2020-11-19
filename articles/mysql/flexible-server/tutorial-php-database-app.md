---
title: 教學課程：建置具有適用於 MySQL 的 Azure 資料庫彈性伺服器的 PHP (Laravel) 應用程式
description: 本教學課程說明如何建置具有彈性伺服器的 PHP 應用程式。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 426cf59c9fb9d88039231ed441b2ffc7246716c7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844432"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>教學課程：在 Azure App Service 中建置 PHP (Laravel) 和 MySQL 彈性伺服器 (預覽) 應用程式


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Azure 中具有彈性伺服器的 PHP Web 應用程式":::

[Azure App Service](../../app-service/overview.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本教學課程示範如何在 Azure 中建立 PHP 應用程式，並將它連線到 MySQL 資料庫。 完成後，Linux 上的 Azure App Service 上將會執行 [Laravel](https://laravel.com/) 應用程式。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 使用本機 MySQL 設定 PHP (Laravel) 應用程式
> * 建立 MySQL 彈性伺服器 (預覽)
> * 將 PHP 應用程式連線到 MySQL 彈性伺服器 (預覽)
> * 將應用程式部署至 Azure App Service
> * 將資料模型更新並將應用程式重新部署
> * 在 Azure 入口網站中管理應用程式

如果您沒有 [Azure 訂用帳戶](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

1. [安裝 Git](https://git-scm.com/)
2. [安裝 PHP 5.6.4 或更新版本](https://php.net/downloads.php)
3. [安裝編輯器](https://getcomposer.org/doc/00-intro.md)
4. 啟用 Laravel 所需的下列 PHP 擴充功能：OpenSSL、PDO-MySQL、Mbstring、Tokenizer、XML
5. [下載並啟動 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>準備本機 MySQL

在此步驟中，您可以在本機 MySQL 伺服器中建立資料庫，供您在本教學課程中使用。

### <a name="connect-to-local-mysql-server"></a>連線至本機 MySQL 伺服器

在終端機視窗中，連線到您的本機 MySQL 伺服器。 您可使用這個終端機視窗來執行本教學課程中的所有命令。

```bash
mysql -u root -p
```

如果系統提示您輸入密碼，請輸入 `root` 帳戶的密碼。 如果您不記得根帳戶密碼，請參閱 [MySQL︰如何重設根密碼](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。

如果您的命令執行成功，表示您的 MySQL 伺服器正在執行。 如果沒有，請遵循 [MySQL 後續安裝步驟](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)，確定您已啟動本機 MySQL 伺服器。

### <a name="create-a-database-locally"></a>在本機建立資料庫

在 `mysql` 提示中，建立資料庫。

```sql
CREATE DATABASE sampledb;
```

輸入 `quit` 即可結束您的伺服器連線。

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>在本機建立 PHP 應用程式
在此步驟中，您會取得 Laravel 範例應用程式、設定其資料庫連線，並在本機執行。

### <a name="clone-the-sample"></a>複製範例

在終端機視窗中，瀏覽至空白目錄以便複製範例應用程式。  執行下列命令來複製範例存放庫。

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

使用 `cd` 移至您複製的目錄。
安裝必要的套件。

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>設定 MySQL 連線

在存放庫的根目錄中，建立名為 *.env* 的檔案。 將下列變數複製到 *.env* 檔案。 將 _&lt;root_password>_ 預留位置取代為 MySQL 根使用者的密碼。

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

如需有關 Laravel 如何使用 _.env_ 檔案的資訊，請參閱 [Laravel 環境設定](https://laravel.com/docs/5.4/configuration#environment-configuration)。

### <a name="run-the-sample-locally"></a>在本機執行範例

執行 [Laravel 資料庫移轉](https://laravel.com/docs/5.4/migrations)來建立應用程式所需的資料表。 若要查看移轉中會建立哪些資料表，請參閱 Git 存放庫中的 _database/migrations_ 目錄。

```bash
php artisan migrate
```

產生新的 Laravel 應用程式金鑰。

```bash
php artisan key:generate
```

執行應用程式。

```bash
php artisan serve
```

在瀏覽器中，瀏覽至 `http://localhost:8000` 。 在頁面中新增幾項工作。

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP 成功連線至 MySQL":::

若要停止 PHP，請在終端機中輸入 `Ctrl + C`。

## <a name="create-a-mysql-flexible-server-preview"></a>建立 MySQL 彈性伺服器 (預覽)
在此步驟中，您會在[適用於 MySQL 的 Azure 資料庫彈性伺服器](../index.yml) (目前為公開預覽) 中建立 MySQL 資料庫。 稍後，您要將 PHP 應用程式設定為連線至此資料庫。 在 [Azure Cloud Shell](../../cloud-shell/overview.md) 中，使用 [`az flexible-server create`](/cli/azure/mysql/server#az-mysql-flexible-server-create) 命令在其中建立伺服器。

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - 請記下 **伺服器名稱** 和 **連接字串**，以便在下一個步驟中用來連線和執行 laravel 資料移轉。
> - 針對 **IP-Address** 引數，提供您用戶端電腦的 IP。 建立伺服器時系統會加以鎖定，您必須允許存取您的用戶端電腦，才能在本機管理伺服器。

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>設定伺服器防火牆，允許 Web 應用程式連線到伺服器

在 Cloud Shell 中，使用 az mysql server firewall-rule create 命令，建立 MySQL 伺服器的防火牆規則來允許用戶端連線。 當起始 IP 和結束 IP 都設為 ```0.0.0.0``` 時，系統只會針對沒有靜態 IP 以連線到伺服器的其他 Azure 服務開啟防火牆。

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>在本機連線到生產環境 MySQL 伺服器

在本機終端機視窗中，連線至 Azure 中的 MySQL 伺服器。 針對 ```<admin-user>``` 和 ```<mysql-server-name>```，使用您先前指定的值。 當系統提示您輸入密碼時，請使用您在 Azure 中建立資料庫時指定的密碼。

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>建立生產環境資料庫

在 `mysql` 提示中，建立資料庫。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>建立具有權限的使用者

建立名為 _phpappuser_ 的資料庫使用者，並將 `sampledb` 資料庫中所有的權限授權給它。 為了簡化教學課程，請使用 MySQLAzure2020 作為密碼。

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

輸入 `quit` 結束伺服器連線。

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>將應用程式連線到 MySQL 彈性伺服器

在此步驟中，您會將 PHP 應用程式連線至您在適用於 MySQL 的 Azure 資料庫中建立的 MySQL 資料庫。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>設定資料庫連接

在存放庫根目錄中，建立 _.env.production_ 檔案，並將下列變數複製到檔案中。 取代 DB_HOST 和 DB_USERNAME 中的預留位置 &lt;mysql-server-name>。

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

儲存變更。

> [!TIP]
> 為了保護您的 MySQL 連接資訊，Git 存放庫中已經排除此檔案 (請看 _.gitignore_ 存放庫的根目錄)。 稍後，您將了解如何設定 App Service 中的環境變數，以連線至適用於 MySQL 的 Azure 資料庫。 使用環境變數，您在 App Service 中就不需要 *.env* 檔案。
>

### <a name="configure-tlsssl-certificate"></a>設定 TLS/SSL 憑證

根據預設，MySQL 彈性伺服器會強制用戶端使用 TLS 連線。 若要連線至 Azure 中的 MySQL 資料庫，您必須使用適用於 MySQL 的 Azure 資料庫彈性伺服器所提供的 [.pem 憑證](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)。 下載 [此憑證](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)) 並放在範例應用程式存放庫本機複本的 **ssl** 資料夾中。

開啟 _config/database.php_，在 `connections.mysql` 中新增 `sslmode` 和 `options` 參數，如下列程式碼所示。

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>在本機測試應用程式

使用 _.env.production_ 作為環境檔案以執行 Laravel 資料庫移轉，可在適用於 MySQL 的 Azure 資料庫中建立資料表。 請記住， _.env.production_ 中有連線至您在 Azure 中的 MySQL 資料庫的連線資訊。

```bash
php artisan migrate --env=production --force
```

_.env.production_ 沒有有效的應用程式金鑰。 在終端機中為它產生一個新的。

```bash
php artisan key:generate --env=production --force
```

使用 _.env.production_ 作為環境檔案來執行範例應用程式。

```bash
php artisan serve --env=production
```

瀏覽至 `http://localhost:8000`。 如果頁面載入無誤，PHP 應用程式就會連線至 Azure 中的 MySQL 資料庫。

在頁面中新增幾項工作。

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP 順利連線至適用於 MySQL 的 Azure 資料庫":::

若要停止 PHP，請在終端機中輸入 `Ctrl + C`。

### <a name="commit-your-changes"></a>認可變更

執行下列的 Git 命令認可您的變更：

```bash
git add .
git commit -m "database.php updates"
```

您的應用程式已準備好進行部署。

## <a name="deploy-to-azure"></a>部署至 Azure

在此步驟中，您要將已與 MySQL 連線的 PHP 應用程式部署至 Azure App Service。

### <a name="configure-a-deployment-user"></a>設定部署使用者

FTP 和本機 Git 可以透過使用「部署使用者」部署到 Azure Web 應用程式。 部署使用者只需設定一次，就能供所有 Azure 部署使用。 使用者名稱和密碼屬於帳戶等級部署，因此與 Azure 訂用帳戶認證不同。

若要設定部署使用者，請在 Azure Cloud Shell 中執行 [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) 命令。 以部署使用者的使用者名稱和和密碼來取代&lt;username> 和 &lt;password>。

使用者名稱在 Azure 服務及本機 Git 推送中都必須是唯一的，且不能包含 ‘@’ 符號。
密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

JSON 輸出會將密碼顯示為 Null。 如果您收到的結果為「衝突」。 詳細資料：409 錯誤，變更使用者名稱。 如果您收到的結果為「錯誤的要求」。 詳細資料：400 錯誤，請使用更強式的密碼。 **將使用者名稱和密碼記錄下來，在部署 Web 應用程式時還會用到。**

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

在 Cloud Shell 中，使用 [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) 命令在資源群組中建立 App Service 方案。 下列範例會在免費定價層 (--sku F1) 和 Linux 容器 (--is-linux) 中，建立名為 myAppServicePlan 的 App Service 方案。

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>建立 Web 應用程式

在 myAppServicePlan App Service 方案中建立 [Web 應用程式](../../app-service/overview.md#app-service-on-linux)。

在 Cloud Shell 中，您可以使用 [az webapp create](/cli/azure/webapp#az-webapp-create) 命令。 在下列範例中，使用全域唯一的應用程式名稱 (有效的字元為 `a-z`、`0-9` 和 `-`) 取代 &lt;app-name>。 執行階段設定為 `PHP|7.0`。 若要查看所有支援的執行階段，請執行 [az webapp list-runtimes --linux](/cli/azure/webapp#az-webapp-list-runtimes)。

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

您已建立空的新 Web 應用程式，其中已啟用 Git 部署。

> [!NOTE]
> Git 遠端的 URL 會顯示在 deploymentLocalGitUrl 屬性中，格式為 https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git。 儲存此 URL，稍後您會用到此資訊。

### <a name="configure-database-settings"></a>設定資料庫設定

在 App Service 中，您可以使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令將環境變數設定為「應用程式設定」。

下列命令會設定 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME`、`DB_PASSWORD` 應用程式設定。 取代預留位置 _&lt;app-name>_ 和 _&lt;mysql-server-name>_ 。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

您可以使用 PHP [getenv](https://www.php.net/manual/en/function.getenv.php) 方法來存取這些設定。 Laravel 程式碼會透過 PHP `getenv` 使用 [env](https://laravel.com/docs/5.4/helpers#method-env) 包裝函式。 例如，在 _config/database.php_ 中的 MySQL 設定看起來像這樣︰

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>設定 Laravel 環境變數

Laravel 在 App Service 中需要應用程式金鑰。 您可以使用應用程式設定加以設定。

在本機終端機視窗中，使用 `php artisan` 產生新的應用程式金鑰，而不將它儲存為 _.env_。

```bash
php artisan key:generate --show
```

在 Cloud Shell 中，使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令，在 App Service 應用程式中設定應用程式金鑰。 取代 _&lt;app-name>_ 和 _&lt;outputofphpartisankey:generate>_ 預留位置。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

當部署的應用程式遇到錯誤，`APP_DEBUG="true"` 會告訴 Laravel 傳回偵錯資訊。 執行生產環境應用程式時，將它設為 `false` 會更安全。

### <a name="set-the-virtual-application-path"></a>設定虛擬應用程式路徑

[Laravel 應用程式生命週期](https://laravel.com/docs/5.4/lifecycle)是在「公用」目錄中啟動，而不是在應用程式的根目錄。 App Service 的預設 PHP Docker 映像會使用 Apache，且不會讓您自訂 Laravel 的 `DocumentRoot`。 不過，您可以使用 `.htaccess` 將所有要求重寫為指向 /public，而不是指向根目錄。 在存放庫根路徑中，已針對此目的新增 `.htaccess`。 因此，您的 Laravel 應用程式已準備好進行部署。

如需詳細資訊，請參閱[變更站台根目錄](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root)。

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

回到本機終端視窗，將 Azure 遠端新增至本機 Git 存放庫。 將 _&lt;deploymentLocalGitUrl-from-create-step>_ 取代為您從 [建立 Web 應用程式](#create-a-web-app)儲存之 Git 遠端的 URL。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

推送到 Azure 遠端，使用下列命令來部署您的應用程式。 當 Git 認證管理員提示輸入認證時，請務必輸入您在 **設定部署使用者** 中建立的認證，而不是您用來登入 Azure 入口網站的認證。

```bash
git push azure master
```

此命令可能會花數分鐘執行。 執行上述命令時，會顯示類似下列範例的資訊：

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>瀏覽至 Azure 應用程式

瀏覽至 `http://<app-name>.azurewebsites.net` 並將幾項工作新增至清單。

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="Azure 中的 PHP Web App":::

恭喜，您正在 Azure App Service 中執行資料驅動的 PHP 應用程式。

## <a name="update-model-locally-and-redeploy"></a>在本機更新模型並重新部署

在此步驟中，您會簡單變更 `task` 資料模型和 webapp，然後將更新發行至 Azure。

在此工作案例中，您將修改應用程式，讓您可以將工作標示為完成。

### <a name="add-a-column"></a>新增資料行

在本機終端機視窗中，瀏覽至 Git 存放庫的根目錄。

為 `tasks` 資料表產生新的資料庫移轉：

```bash
php artisan make:migration add_complete_column --table=tasks
```

此命令會顯示所產生的移轉檔案名稱。 在 _database/migrations_ 中找到這個檔案並開啟它。

以下列程式碼取代 `up` 方法：

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

上方的程式碼會在名為 `complete` 的 `tasks` 資料表中新增布林值資料行。

將 `down` 方法取代為下列程式碼以進行復原動作︰

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

在本機終端機視窗中，執行 Laravel 資料庫移轉，以在本機資料庫中進行變更。

```bash
php artisan migrate
```

根據 [Laravel 命名慣例](https://laravel.com/docs/5.4/eloquent#defining-models)，依預設 `Task` 模型 (請看 app/Task.php) 會對應至 `tasks` 資料表。

### <a name="update-application-logic"></a>更新應用程式邏輯

開啟 *routes/web.php* 檔案。 應用程式會在這裡定義其路由和商務邏輯。

在檔案的結尾，使用下列程式碼新增路由︰

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

上方的程式碼會切換 `complete` 的值，對資料模型進行簡單的更新。

### <a name="update-the-view"></a>更新檢視

開啟 *resources/views/tasks.blade.php* 檔案。 尋找 `<tr>` 開頭標記，並將它取代為︰

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

上方的程式碼會視工作是否完成來變更資料列色彩。

在下一行中，您會有下列程式碼︰

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

將這一整行取代為下列程式碼：

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

上方的程式碼新增的提交按鈕會參考您稍早定義的路由。

### <a name="test-the-changes-locally"></a>本機測試變更

在本機終端機視窗中，從 Git 儲存機制的根目錄，執行程式開發伺服器。

```bash
php artisan serve
```

若要查看工作狀態的變更，瀏覽至 `http://localhost:8000`，然後選取核取方塊。

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="已將核取方塊新增至工作":::

若要停止 PHP，請在終端機中輸入 `Ctrl + C`。

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

在本機終端機視窗中，使用生產環境連接字串執行 Laravel 資料庫移轉，以在 Azure 資料庫中進行變更。

```bash
php artisan migrate --env=production --force
```

在 Git 中認可所有變更，然後將程式碼變更推送至 Azure。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

完成 `git push` 之後，巡覽至 Azure 應用程式，然後測試新功能。

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="發佈至 Azure 的模型和資料庫變更":::

如果您新增任何工作，它們會保留在資料庫中。 對資料結構描述進行的更新，現有資料會原封不動。

## <a name="clean-up-resources"></a>清除資源
在前述步驟中，您在資源群組中建立了 Azure 資源。 如果您在未來不需要這些資源，請在 Cloud Shell 中執行下列命令，刪除資源群組：

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何管理 Azure 入口網站中的資源](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [如何管理您的伺服器](how-to-manage-server-cli.md)
