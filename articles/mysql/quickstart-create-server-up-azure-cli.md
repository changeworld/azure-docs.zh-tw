---
title: 快速入門：使用 az mysql up 建立適用於 MySQL 的 Azure 資料庫
description: 本快速入門指南說明如何使用 Azure CLI (命令列介面) up 命令建立適用於 MySQL 的 Azure 資料庫伺服器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: f2d0d0517579608a76ccf8c1e63aa993556e8d58
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608020"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>快速入門：使用簡單的 Azure CLI 命令建立適用於 MySQL 的 Azure 資料庫 - az mysql up (預覽)

> [!IMPORTANT]
> [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI 命令在預覽狀態。

「適用於 MySQL 的 Azure 資料庫」是一個受控服務，可讓您在雲端執行、管理及調整高可用性 MySQL 資料庫。 Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門說明如何使用 [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) 命令，使用 Azure CLI 建立適用於 MySQL 的 Azure 資料庫伺服器。 除了建立伺服器，`az mysql up` 命令還會建立範例資料庫、資料庫中的根使用者、開啟 Azure 服務的防火牆，以及建立用戶端電腦的預設防火牆規則。 這有助於加速開發程序。

## <a name="prerequisites"></a>必要條件

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

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器

若要此命令，請安裝 [db-up](/cli/azure/ext/db-up) 擴充功能。 如果傳回錯誤，請確定您已安裝最新版的 Azure CLI。 請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli
az extension add --name db-up
```

使用下列命令建立適用於 MySQL 的 Azure 資料庫伺服器：

```azurecli
az mysql up
```

伺服器會以下列預設值來建立 (除非您手動覆寫它們)：

**設定** | **預設值** | **說明**
---|---|---
server-name | 由系統產生 | 可識別 Azure Database for MySQL 伺服器的唯一名稱。
resource-group | 由系統產生 | 新的 Azure 資源群組。
sku-name | GP_Gen5_2 | SKU 的名稱。 遵循簡短形式的慣例 {pricing tier}\_{compute generation}\_{vCores}。 預設是一部一般用途、具有 2 個 vCore 的第 5 代伺服器。 如需關於價格的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/mysql/)。
backup-retention | 7 | 備份應保留的時間長度。 單位為天。
geo-redundant-backup | 已停用 | 是否應針對此伺服器啟用異地備援備份。
location | westus2 | 伺服器的 Azure 位置。
ssl-enforcement | 啟用 | 是否應針對此伺服器啟用 SSL。
storage-size | 5120 | 伺服器的儲存體容量 (單位為 MB)。
version | 5.7 | MySQL 主要版本。
admin-user | 由系統產生 | 適用於系統管理員登入的使用者名稱。
admin-password | 由系統產生 | 系統管理員使用者的密碼。

> [!NOTE]
> 如需有關 `az mysql up` 命令及其額外參數的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up)。

您的伺服器建立好之後，它會隨附下列設定：

- 會建立名為 "devbox" 的防火牆規則。 Azure CLI 會嘗試偵測 `az mysql up` 命令執行來源的電腦 IP 位址，並將該 IP 位址加入允許清單。
- [允許存取 Azure 服務] 設為 [開啟]。 此設定會將伺服器的防火牆設定為接受來自所有 Azure 資源的連線，其中也包括您未訂閱的資源。
- `wait_timeout` 參數設為 8 小時
- 建立名為 "sampledb" 的空白資料庫
- 建立名為 "root"，且具有 "sampledb" 權限的新使用者

> [!NOTE]
> 適用於 MySQL 的 Azure 資料庫會透過連接埠 3306 進行通訊。 當您從公司網路內進行連線時，網路的防火牆可能不允許透過連接埠 3306 的輸出流量。 請要求您的 IT 部門開放連接埠 3306，以連線至您的伺服器。

## <a name="get-the-connection-information"></a>取得連線資訊

完成 `az mysql up` 命令之後，熱門程式語言的連接字串清單就會傳回給您。 這些連接字串是使用您新建立的適用於 MySQL 的 Azure 資料庫伺服器的特定屬性預先設定的。

您可以使用 [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) 命令，再次列出這些連接字串。

## <a name="clean-up-resources"></a>清除資源

使用下列命令清除您在快速入門中建立的所有資源。 此命令會刪除適用於 MySQL 的 Azure 資料庫伺服器和資源群組。

```azurecli
az mysql down --delete-group
```

如果您只想要刪除新建立的伺服器，可以執行 [az mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) 命令。

```azurecli
az mysql down
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 設計 MySQL 資料庫](./tutorial-design-database-using-cli.md)
