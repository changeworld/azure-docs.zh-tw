---
title: 教學課程：設計適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器 - Azure PowerShell
description: 本教學課程說明如何使用 Azure PowerShell 來建立、設定及查詢您的第一個適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 70edf224494fdf1395d59d1c591d0369b9b20557
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332979"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-powershell"></a>教學課程：使用 PowerShell 設計適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器

適用於 PostgreSQL 的 Azure 資料庫是 Microsoft 雲端中以 PostgreSQL Community Edition 資料庫引擎為基礎的關聯式資料庫服務。 在本教學課程中，您將使用 PowerShell 和其他公用程式來了解如何：

> [!div class="checklist"]
> - 建立適用於 PostgreSQL 的 Azure 資料庫
> - 設定伺服器防火牆
> - 使用 [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) 公用程式來建立資料庫
> - 載入範例資料
> - 查詢資料
> - 更新資料
> - 還原資料

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 雖然 Az.PostgreSql PowerShell 模組處於預覽狀態，但您仍必須使用下列命令，將其與 Az PowerShell 模組分開安裝：`Install-Module -Name Az.PostgreSql -AllowPrerelease`。
> 在 Az.PostgreSql PowerShell 模組正式推出後，其會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

如果這是您第一次使用適用於 PostgreSQL 的 Azure 資料庫服務，則必須註冊 **Microsoft.DBforPostgreSQL** 資源提供者。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶識別碼。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會在 [美國西部] 區域中建立名為 myresourcegroup 的資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>建立適用於 PostgreSQL 的 Azure 資料庫伺服器

使用 `New-AzPostgreSqlServer` Cmdlet 建立適用於 PostgreSQL 的 Azure 資料庫伺服器。 一部伺服器可以管理多個資料庫。 一般而言，每個專案或每個使用者會使用個別的資料庫。

下列範例會在 **美國西部** 區域中建立名為 **mydemoserver** 的 PostgreSQL 伺服器，其位於 **myresourcegroup** 資源群組中，且伺服器系統管理員登入為 **myadmin** 。 這是一般用途定價層中的 Gen 5 伺服器，已啟用 2 個虛擬核心和異地備援備份。 記錄範例第一行中使用的密碼，因為這是 PostgreSQL 伺服器系統管理員帳戶的密碼。

> [!TIP]
> 伺服器名稱會對應至 DNS 名稱，而且在 Azure 中必須是全域唯一的。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

**Sku** 參數值會遵循慣例 **pricing-tier\_compute-generation\_vCores** ，如下列範例所示。

- `-Sku B_Gen5_1` 對應於基本、第 5 代和 1 個虛擬核心。 此選項是最小的可用 SKU。
- `-Sku GP_Gen5_32` 對應於一般用途、第 5 代和 32 個 vCore。
- `-Sku MO_Gen5_2` 對應於記憶體最佳化、第 5 代和 2 個 vCore。

如需各區域和各層有效 **Sku** 值的相關資訊，請參閱 [適用於 PostgreSQL 的 Azure 資料庫定價層](./concepts-pricing-tiers.md)。

如果您的工作負載只需要輕量計算和 I/O，請考慮使用基本定價層。

> [!IMPORTANT]
> 在基本定價層中建立的伺服器後續無法調整為一般用途或記憶體最佳化，而且無法進行異地複寫。

## <a name="configure-a-firewall-rule"></a>設定防火牆規則

使用 `New-AzPostgreSqlFirewallRule`Cmdlet 建立適用於 PostgreSQL 的 Azure 資料庫伺服器層級防火牆規則。 伺服器層級的防火牆規則允許外部應用程式 (例如 `psql` 命令列工具或 PostgreSQL Workbench) 透過適用於 PostgreSQL 的 Azure 資料庫服務防火牆連線到您的伺服器。

下列範例會建立名為 **AllowMyIP** 的防火牆規則，以允許來自特定 IP 位址 192.168.0.1 的連線。 請替換為與您的連線來源位置相對應的 IP 位址或 IP 位址範圍。

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> 適用於 PostgreSQL 的 Azure 資料庫的連線透過連接埠 3306 進行通訊。 如果您嘗試從公司網路內進行連線，可能不允許透過連接埠 3306 的輸出流量。 在此案例中，唯有 IT 部門開啟連接埠 3306，您才能連線到伺服器。

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，您必須提供主機資訊和存取認證。 使用下列範例來判斷連線資訊。 請記下 **FullyQualifiedDomainName** 和 **AdministratorLogin** 的值。

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgresql.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>使用 psql 連線到 PostgreSQL 資料庫

如果您的用戶端電腦已安裝 PostgreSQL，您可以使用 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 的本機執行個體來連線到 Azure PostgreSQL 伺服器。 對本文中的程式碼範例選取 [試試看] **`psql` 按鈕，也可以存取 Azure Cloud Shell 中預先安裝的**  命令列工具版本。 其他存取 Azure Cloud Shell 的方法是選取 Azure 入口網站右上方工具列上的 **>_** 按鈕，或造訪 [shell.azure.com](https://shell.azure.com/)。

1. 使用 `psql` 命令列公用程式連線到您的 Azure PostgreSQL 伺服器。

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   例如，下列命令會使用存取認證，連線到 PostgreSQL 伺服器 `mydemoserver.postgres.database.azure.com` 上名為 **postgres** 的預設資料庫。 系統提示輸入密碼時，請輸入您選擇的 `<server_admin_password>`。

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > 如果您想要使用 URL 路徑來連線到 Postgres，在進行 URL 編碼時，請將使用者名稱中的 @ 符號編碼為 `%40`。 例如，psql 的連線字串會是 `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. 連線到伺服器後，請在提示字元建立空白資料庫。

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. 在提示字元，執行下列命令以將連線切換到新建立的資料庫 **mypgsqldb** ：

   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>在資料庫中建立資料表

既然您已經知道如何連線到適用於 PostgreSQL 資料庫的 Azure 資料庫，請完成一些基本工作。

首先，建立資料表並在其中載入一些資料。 我們將建立一個儲存清查資訊的資料表。

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>將資料載入到資料表

既然您已經有資料表，請在其中插入一些資料。 在開啟的命令提示字元視窗，執行下列查詢以插入幾列資料。

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

您現在已將兩列範例資料插入到先前建立的資料表。

## <a name="query-and-update-the-data-in-the-tables"></a>查詢並更新資料表中的資料

執行下列查詢，以從資料庫資料表中擷取資訊。

```sql
SELECT * FROM inventory;
```

您也可以更新資料表中的資料。

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

當您擷取資料時，資料列會相應地更新。

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>將資料庫還原至先前的時間點

您可以將伺服器還原至先前的時間點。 還原的資料會複製到新的伺服器，而現有的伺服器則不會變更。 例如，如果資料表意外卸除，您可以還原到卸除之前的時間。 然後，您可從伺服器的還原複本擷取遺失的資料表和資料。

若要還原伺服器，請使用 `Restore-AzPostgreSqlServer` PowerShell Cmdlet。

### <a name="run-the-restore-command"></a>執行 restore 命令

若要還原伺服器，請從 PowerShell 執行下列範例。

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

當您將伺服器還原到之前的時間點時，會建立新的伺服器。 指定時間點的原始伺服器及其資料庫會複製到新的伺服器。

已還原伺服器的位置與定價層值與原始伺服器相同。

完成還原程序後，找出新的伺服器，確認資料如預期般還原。 新伺服器的伺服器管理員登入名稱和密碼，與還原啟動時有效的現有伺服器相同。 您可以從新伺服器的 [概觀] 頁面變更密碼。

在還原期間建立的新伺服器不會有原始伺服器中的 VNet 服務端點。 您必須為新伺服器分別設定這些規則。 系統會還原原始伺服器的防火牆規則。

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在伺服器群組中建立了 Azure 資源。 如果您認為未來不需要這些資源，請刪除伺服器群組。 在您伺服器群組的 [概觀]  頁面中，按一下 [刪除]  按鈕。 當快顯頁面上出現提示時，請確認伺服器群組的名稱，然後按一下最後一個 [刪除]  按鈕。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何使用 PowerShell 備份和還原適用於 PostgreSQL 的 Azure 資料庫伺服器](howto-restore-server-powershell.md)