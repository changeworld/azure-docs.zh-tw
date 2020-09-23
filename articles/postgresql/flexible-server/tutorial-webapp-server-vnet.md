---
title: 教學課程：在相同的虛擬網路中建立適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器和 Azure App Service Web 應用程式
description: 在虛擬網路中建立適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器和 Web 應用程式的快速入門指南
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20401a3c96a9a20399c07d1a30370d27f2858e29
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929355"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>教學課程：在虛擬網路中使用應用程式服務 Web 應用程式建立適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

本教學課程說明如何在[虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)中，使用適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器 (預覽) 建立 Azure App Service Web 應用程式。

在本教學課程中，您將會
>[!div class="checklist"]
> * 在虛擬網路中建立 PostgreSQL 彈性伺服器
> * 建立 Web 應用程式
> * 將 Web 應用程式新增至虛擬網路
> * 從 Web 應用程式連線到 Postgres 

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

本文需要您以本機方式執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須使用 [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) 命令登入您的帳戶。 請記下命令輸出中的**識別碼**屬性，以取得對應的訂用帳戶名稱。

```azurecli
az login
```

如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [az account set](/cli/azure/account) 命令來選取您帳戶底下的特定訂用帳戶 ID。 以訂用帳戶之 **az login** 輸出中的**訂用帳戶識別碼**屬性，替代訂用帳戶識別碼的預留位置。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>在虛擬網路中建立 PostgreSQL 彈性伺服器

使用下列命令，在虛擬網路 (VNET) 中建立私人彈性伺服器：
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
此命令會執行下列動作，這可能需要幾分鐘的時間：

- 建立資源群組 (若尚不存在)。
- 若未提供，系統會產生伺服器名稱。
- 為新的 postgreSQL 伺服器建立新的虛擬網路。 記下為伺服器建立的虛擬網路名稱和子網路名稱，因為您必須將 Web 應用程式新增至相同的虛擬網路。
- 建立伺服器的管理使用者名稱、密碼 (如果未提供)。
- 建立名為 **postgres** 的空白資料庫

> [!NOTE]
> - 記下您的密碼，如果未提供，系統會產生密碼。 如果忘記密碼，就必須使用 ``` az postgres flexible-server update``` 命令重設
> - 如果您不是使用 App Service 環境，則必須使用此命令啟用「允許從任何 Azure IP 存取」。 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```


## <a name="create-a-web-app"></a>建立 Web 應用程式
在此節中，您會在 App Service 應用程式中建立應用程式主機、將此應用程式連線到 Postgres 資料庫，然後將程式碼部署到該主機。 確定您位於終端中包含應用程式程式碼的儲存機制根路徑。

使用 az webapp up 命令建立 App Service 應用程式 (主機處理序)

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - 針對 --location 引數，使用與您在上一節針對資料庫所做的相同位置。
> - 以跨所有 Azure 的唯一名稱取代 <app-name> (伺服器端點為 https://\<app-name>.azurewebsites.net)。 <app-name> 的允許字元為 A-Z、0-9 和 -。 良好的模式是使用您的公司名稱和應用程式識別碼的組合。

此命令會執行下列動作，這可能需要幾分鐘的時間：

- 建立資源群組 (若尚不存在)。 (在此命令中，您使用先前用來建立資料庫的相同資源群組)。
- 如果不存在，請在基本定價層 (B1) 中建立 App Service 方案 ```testappserviceplan```。 --plan 和 --sku 是選用引數。
- 建立 App Service 應用程式 (如果不存在)。
- 啟用應用程式的預設記錄 (如果尚未啟用)。
- 使用已啟用建置自動化的 ZIP 部署來上傳存放庫。

## <a name="add-the-web-app-to-the-virtual-network"></a>將 Web 應用程式新增至虛擬網路
使用 **az webapp vnet-integration** 命令，將區域虛擬網路整合新增至 WebApp。 以彈性伺服器所使用的虛擬網路和子網路名稱取代 <vnet-name> 和 <subnet-name>。

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>設定環境變數以連線資料庫
現在已將程式碼部署到 App Service，下一個步驟是將應用程式連線到 Azure 中的彈性伺服器。 應用程式程式碼預期會在數個環境變數中尋找資料庫資訊。 若要在 App Service 中設定環境變數，您可以使用 ```az webapp config appsettings``` 設定命令來建立「應用程式設定」。

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- 取代新建立的彈性伺服器命令 ```postgres-server-name```、```username``` 和 ```password```。
- 使用此命令也會為您產生的認證取代 <username> 和 <password> 。
- 資源群組和應用程式名稱均提取自 .azure/config 檔案中的快取值。
- 命令會建立名為 ```DBHOST```、```DBNAME```、```DBUSER``` 和 ```DBPASS``` 的設定。 如果您的應用程式程式碼針對資料庫資訊使用不同的名稱，則請使用這些名稱作為程式碼中提到的應用程式設定。

## <a name="clean-up-resources"></a>清除資源

使用下列命令清除您在教學課程中建立的所有資源。 此命會刪除此資源群組內的所有資源。

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)