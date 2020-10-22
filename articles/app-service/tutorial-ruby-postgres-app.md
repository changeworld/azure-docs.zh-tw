---
title: 教學課程：使用 Postgres 的 Linux Ruby 應用程式
description: 了解如何藉由連線至 Azure 中的 PostgreSQL 資料庫，讓 Linux Ruby 應用程式在 Azure App Service 中運作。 本教學課程中將使用 Rails。
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: c2baccec75c7b525c0837cebd9d828dff3a79543
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150175"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>在 Linux 上的 Azure App Service 中建置 Ruby 和 Postgres 應用程式

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。 本教學課程示範如何建立 Ruby 應用程式，並將它連線至 PostgreSQL 資料庫。 完成後，Linux 上將有一個在 App Service 上執行的 [Ruby on Rails](https://rubyonrails.org/) 應用程式。

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="標題為工作的 Ruby on Rails 應用程式範例的螢幕擷取畫面。":::

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 PostgreSQL 資料庫
> * 將 Ruby on Rails 應用程式連線到 PostgreSQL
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

* [安裝 Git](https://git-scm.com/)
* [安裝 Ruby 2.6](https://www.ruby-lang.org/en/documentation/installation/)
* [安裝 Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [安裝及執行 PostgreSQL](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>準備本機 Postgres

在此步驟中，您可以在本機 Postgres 伺服器中建立資料庫，供您在本教學課程中使用。

### <a name="connect-to-local-postgres-server"></a>連線至本機 Postgres 伺服器

開啟終端機視窗，然後執行 `psql` 來連線至本機 Postgres 伺服器。

```bash
sudo -u postgres psql
```

如果連線成功，則您的 Postgres 資料庫就已在執行中。 如果沒有，請確定您的本機 Postgres 資料庫已遵循[下載 - PostgresQL 核心散發](https://www.postgresql.org/download/)中的步驟來啟動。

輸入 `\q` 來結束 Postgres 用戶端。 

使用您的登入 Linux 使用者名稱，建立可藉由執行下列命令來建立資料庫的 Postgres 使用者。

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>在本機上建立 Ruby on Rails 應用程式
在此步驟中，您會取得 Ruby on Rails 範例應用程式、設定其資料庫連線，並在本機執行。 

### <a name="clone-the-sample"></a>複製範例

在終端機視窗中，使用 `cd` 移至工作目錄。

執行下列命令來複製範例存放庫。

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

使用 `cd` 移至您複製的目錄。 安裝必要的套件。

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>在本機執行範例

執行 [Rails 移轉](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations)來建立應用程式所需的資料表。 若要查看移轉中會建立哪些資料表，請查看 Git 存放庫中的 _db/migrate_ 目錄。

```bash
rake db:create
rake db:migrate
```

執行應用程式。

```bash
rails server
```

在瀏覽器中，瀏覽至 `http://localhost:3000` 。 在頁面中新增幾項工作。

![Ruby on Rails 成功連線至 Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

若要停止 Rails 伺服器，請在終端機中輸入 `Ctrl + C`。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>在 Azure 中建立 Postgres

在此步驟中，您會在[適用於 PostgreSQL 的 Azure 資料庫](../postgresql/index.yml)中建立 Postgres 資料庫。 稍後，您會將 Ruby on Rails 應用程式設定為連線至此資料庫。

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>在 Azure 中建立 Postgres 資料庫

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

在本節中，您會建立「適用於 PostgreSQL 的 Azure 資料庫」伺服器和資料庫。 若要啟動，請執行下列命令安裝 `db-up` 延伸模組：

```azurecli
az extension add --name db-up
```

使用 [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) 命令建立 Postgres 資料庫，如下列範例所示。 以「唯一的」名稱取代 *\<postgresql-name>* (伺服器端點是 *https://\<postgresql-name>.postgres.database.azure.com*)。 為 *\<admin-username>* 和 *\<admin-password>* ，指定認證以建立此 Postgres 伺服器的系統管理員使用者。

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

此命令可能需要一些時間，因為會執行下列動作：

- 建立稱為 `myResourceGroup` 的[資源群組](../azure-resource-manager/management/overview.md#terminology) (如果不存在的話)。 每個 Azure 資源都必須具備其中一項。 `--resource-group` 是選擇性的。
- 建立具有管理使用者的 Postgres 伺服器。
- 建立 `sampledb` 資料庫。
- 允許從您的本機 IP 位址進行存取。
- 允許從 Azure 服務存取。
- 建立資料庫使用者，並提供 `sampledb` 資料庫的存取權。

您可以使用其他 `az postgres` 命令和 `psql` 分別執行所有步驟，但 `az postgres up` 會在一個步驟中全部完成。

當命令完成時，尋找 `Ran Database Query:` 的輸出行。 這些程式碼行會顯示為您建立的資料庫使用者，並包含使用者名稱 `root` 和密碼 `Sampledb1`。 稍後您會使用這些資料將應用程式連線到資料庫。

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`，可以設定為任何一個 [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。 您可以使用 [`az account list-locations`](/cli/azure/account#az-account-list-locations) 命令，取得您訂用帳戶可用的區域。 針對生產應用程式，請將您的資料庫和應用程式放在相同的位置。

## <a name="connect-app-to-azure-postgres"></a>將應用程式連線至 Azure Postgres

在此步驟中，您會將 Ruby on Rails 應用程式連線至您在適用於 PostgreSQL 的 Azure 資料庫中建立的 Postgres 資料庫。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>設定資料庫連接

在存放庫中，開啟 config/database.yml__。 在檔案最下方，使用下列程式碼取代生產變數。 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

儲存變更。

### <a name="test-the-application-locally"></a>在本機測試應用程式

回到本機終端機，設定下列環境變數：

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
```

使用您剛設定的生產值來執行 Rails 資料庫移轉，可在適用於 PostgreSQL 的 Azure 資料庫中的 Postgres 資料庫中建立資料表。

```bash
rake db:migrate RAILS_ENV=production
```

當 Rails 應用程式在生產環境中執行時，其需要預先編譯的資產。 請使用下列命令產生必要資產：

```bash
rake assets:precompile
```

Rails 生產環境也會使用祕密來管理安全性。 產生祕密金鑰。

```bash
rails secret
```

將祕密金鑰儲存至 Rails 生產環境所使用的個別變數。 為了方便起見，您可對兩個變數使用相同的金鑰。

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

讓 Rails 生產環境可支援 JavaScript 和 CSS 檔案。

```bash
export RAILS_SERVE_STATIC_FILES=true
```

在生產環境中執行範例應用程式。

```bash
rails server -e production
```

瀏覽至 `http://localhost:3000`。 如果頁面載入無誤，Ruby on Rails 應用程式就會連線至 Azure 中的 Postgres 資料庫。

在頁面中新增幾項工作。

![Ruby on Rails 順利連線至適用於 PostgreSQL 的 Azure 資料庫](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

若要停止 Rails 伺服器，請在終端機中輸入 `Ctrl + C`。

### <a name="commit-your-changes"></a>認可變更

執行下列的 Git 命令認可您的變更：

```bash
git add .
git commit -m "database.yml updates"
```

您的應用程式已準備好進行部署。

## <a name="deploy-to-azure"></a>部署至 Azure

在此步驟中，您會將與 Postgres 連線的 Rails 應用程式部署至 Azure App Service。

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>建立 Web 應用程式

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>設定資料庫設定

在 App Service 中，您可以在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) 命令將環境變數設定為「應用程式設定」__。

下列 Cloud Shell 命令會設定 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME`和 `DB_PASSWORD` 應用程式設定。 取代預留位置 &lt;appname>__ 和 &lt;postgres-server-name>__。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>設定 Rails 環境變數

在本機終端機中，為 Azure 中的 Rails 生產環境[產生新的秘密](configure-language-ruby.md#set-secret_key_base-manually)。

```bash
rails secret
```

設定 Rails 生產環境所需的變數。

在下列 Cloud Shell 命令中，以本機終端機中產生的新秘密金鑰取代兩個 &lt;output-of-rails-secret>__ 預留位置。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` 會告知預設的 Ruby 容器要預先編譯各個 Git 部署上的資產。 如需詳細資訊，請參閱[先行編譯資產](configure-language-ruby.md#precompile-assets)和[處理靜態資產](configure-language-ruby.md#serve-static-assets)。

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

在本機終端中，將 Azure 遠端新增至本機 Git 存放庫。

```bash
git remote add azure <paste-copied-url-here>
```

推送至 Azure 遠端以部署 Ruby on Rails 應用程式。 建立部署使用者時，系統會提示您輸入稍早提供的密碼。

```bash
git push azure master
```

在部署期間，Azure App Service 會與 Git 溝通其進度。

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

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="標題為工作的 Ruby on Rails 應用程式範例的螢幕擷取畫面。":::

恭喜，您正在 Azure App Service 中執行資料驅動的 Ruby on Rails 應用程式。

## <a name="update-model-locally-and-redeploy"></a>在本機更新模型並重新部署

在此步驟中，您會簡單變更 `task` 資料模型和 webapp，然後將更新發行至 Azure。

在此工作案例中，您將修改應用程式，讓您可以將工作標示為完成。

### <a name="add-a-column"></a>新增資料行

在終端機中，瀏覽至 Git 存放庫的根目錄。

產生新的移轉，以將名為 `Done` 的布林資料行新增至 `Tasks` 資料表：

```bash
rails generate migration AddDoneToTasks Done:boolean
```

此命令會在 db/migrate__ 目錄中產生新的移轉檔案。


在終端機中，執行 Rails 資料庫移轉，以在本機資料庫中進行變更。

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>更新應用程式邏輯

開啟 app/controllers/tasks_controller.rb** 檔案。 在檔案結尾處，尋找下列這一行︰

```rb
params.require(:task).permit(:Description)
```

修改此行，以包含新的 `Done` 參數。

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>更新檢視

開啟 app/views/tasks/_form.html.erb** 檔案，這是編輯表單。

找到 `<%=f.error_span(:Description) %>` 行，並直接將下列程式碼插入該行下方：

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

開啟 *app/views/tasks/show.html.erb* 檔案，這是單一記錄的檢視頁面。 

找到 `<dd><%= @task.Description %></dd>` 行，並直接將下列程式碼插入該行下方：

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

開啟 app/views/tasks/index.html.erb** 檔案，這是所有記錄的索引頁面。

找到 `<th><%= model_class.human_attribute_name(:Description) %></th>` 行，並直接將下列程式碼插入該行下方：

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

在相同檔案中，找到 `<td><%= task.Description %></td>` 行，並直接將下列程式碼插入該行下方：

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>本機測試變更

在本機終端機中，執行 Rails 伺服器。

```bash
rails server
```

若要查看工作狀態的變更，請瀏覽至 `http://localhost:3000` 並新增或編輯項目。

![已將核取方塊新增至工作](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

若要停止 Rails 伺服器，請在終端機中輸入 `Ctrl + C`。

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

在終端機中，針對生產環境執行 Rails 資料庫移轉，以在 Azure 資料庫中進行變更。

```bash
rake db:migrate RAILS_ENV=production
```

在 Git 中認可所有變更，然後將程式碼變更推送至 Azure。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

完成 `git push` 之後，巡覽至 Azure 應用程式，然後測試新功能。

![發佈至 Azure 的模型和資料庫變更](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

如果您新增任何工作，它們會保留在資料庫中。 對資料結構描述進行的更新，現有資料會原封不動。

## <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>管理 Azure 應用程式

移至 [Azure 入口網站](https://portal.azure.com)，以管理您所建立的應用程式。

按一下左側功能表中的 [應用程式服務]****，然後按一下 Azure 應用程式的名稱。

![入口網站瀏覽至 Azure 應用程式](./media/tutorial-php-mysql-app/access-portal.png)

您會看到應用程式的 [概觀] 頁面。 您可以在這裡執行基本管理工作，像是停止、啟動、重新啟動、瀏覽、刪除。

左側功能表提供的頁面可用來設定您的應用程式。

![Azure 入口網站中的 App Service 頁面](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 Postgres 資料庫
> * 將 Ruby on Rails 應用程式連線到 Postgres
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至應用程式。

> [!div class="nextstepaction"]
> [教學課程：將自訂 DNS 名稱對應至應用程式](app-service-web-tutorial-custom-domain.md)

或者，查看其他資源：

> [!div class="nextstepaction"]
> [設定 Ruby 應用程式](configure-language-ruby.md)