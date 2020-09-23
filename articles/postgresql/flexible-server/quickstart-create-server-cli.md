---
title: 快速入門：建立伺服器 - Azure CLI - 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器
description: 本快速入門說明如何使用 Azure CLI 在 Azure 資源群組中建立適用於 PostgreSQL 的 Azure 資料庫彈性伺服器。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc
ms.openlocfilehash: 78824ef6f557a2df431e664158da9d858df0603f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944091"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>快速入門：使用 Azure CLI 建立適用於 PostgreSQL 的 Azure 資料庫彈性伺服器

本快速入門說明如何使用 [Azure Cloud Shell](https://shell.azure.com) 中的 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 命令，在五分鐘內建立適用於 PostgreSQL 的 Azure 資料庫彈性伺服器。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

> [!IMPORTANT] 
> 適用於 PostgreSQL 的 Azure 資料庫彈性伺服器目前為預覽狀態。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤開啟 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後選取 **Enter** 鍵加以執行。

如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>Prerequisites

您必須使用 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令登入您的帳戶。 請注意 **id** 屬性，這是指您的 Azure 帳戶的**訂用帳戶識別碼**。

```azurecli-interactive
az login
```

使用 [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) 命令來選取您帳戶底下的特定訂用帳戶。 記下 **az login** 輸出中的 **id** 值，作為命令中**訂用帳戶**引數的值。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 若要取得您的所有訂用帳戶，請使用 [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>建立彈性伺服器

使用 `az group create` 命令建立 [Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)，然後在此資源群組內建立您的 PostgreSQL 彈性伺服器。 請提供唯一的名稱。 下列範例會在 `westus` 位置建立名為 `myresourcegroup` 的資源群組。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

使用 `az postgres flexible-server create` 命令建立彈性伺服器。 一部伺服器可以包含多個資料庫。 下列命令會使用服務預設值和 Azure CLI 的[本機內容](https://docs.microsoft.com/cli/azure/local-context?view=azure-cli-latest)值，建立伺服器： 

```azurecli
az postgres flexible-server create
```

建立的伺服器具有下列屬性： 
- 自動產生的伺服器名稱、管理使用者名稱、管理員密碼、資源群組名稱 (如果未在本機內容中指定)，並與您的資源群組位於相同位置 
- 其餘伺服器組態的服務預設值：計算層 (一般用途)、計算大小/SKU (D2s_v3 - 2 虛擬核心，8 GB RAM)、備份保留期間 (7天) 和 PostgreSQL 版本 (12)
- 預設連線方法是具有自動產生的虛擬網路和子網路的私人存取 (VNet 整合)

> [!NOTE] 
> 建立伺服器後，就無法變更連線方法。 例如，如果您在建立期間選取了「私人存取 (VNet 整合)」，則在建立之後，就無法變更為「公用存取 (允許的 IP 位址)」。 強烈建議您建立具有私人存取權的伺服器，才能使用 VNet 整合安全地存取您的伺服器。 若要深入了解私人存取，請參閱[概念文章](./concepts-networking.md)。

如果您想要變更任何預設值，請參閱 Azure CLI 參考文件 <!--FIXME --> 以取得可設定的 CLI 參數完整清單。 

> [!NOTE]
> 適用於 PostgreSQL 的 Azure 資料庫的連線會透過連接埠 5432 進行通訊。 如果您嘗試從公司網路內進行連線，可能不允許透過連接埠 5432 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 5432，否則您無法連線到您的伺服器。

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，您必須提供主機資訊和存取認證。

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

結果會採用 JSON 格式。 請記下 **fullyQualifiedDomainName** 和 **administratorLogin**。

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>使用 PostgreSQL 命令列用戶端連線

如果使用私人存取 (VNet 整合) 建立彈性伺服器，就必須從與伺服器相同的 VNet 中的資源連線到您的伺服器。 您可以建立虛擬機器並將其加入建立的虛擬網路。 

建立 VM 之後，您可以透過 SSH 連線至電腦，並安裝 **[psql](https://www.postgresql.org/download/)** 命令列工具。

使用 psql，使用下列命令連線。 以實際的伺服器名稱和密碼取代這些值。 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>清除資源

如果您不需要這些資源來進行其他快速入門/教學課程，可以執行下列命令加以刪除︰

```azurecli-interactive
az group delete --name myresourcegroup
```

如果您只想刪除新建立的伺服器，可以執行 `az postgres flexible-server delete`命令。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[使用 App Service 和 PostgreSQL 部署 Django 應用程式](tutorial-django-app-service-postgres.md)