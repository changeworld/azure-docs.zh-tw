---
title: 快速入門：建立伺服器 - Azure PowerShell - 適用於 MySQL 的 Azure 資料庫
description: 本快速入門說明如何使用 PowerShell 在 Azure 資源群組中建立 Azure Database for MySQL 伺服器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 04/28/2020
ms.custom: mvc
ms.openlocfilehash: 2e12da29a8388bf4a232930c3737be7ddce80d12
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611937"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-powershell"></a>快速入門：使用 PowerShell 建立適用於 MySQL 的 Azure 資料庫伺服器

本快速入門說明如何使用 PowerShell 在 Azure 資源群組中建立 Azure Database for MySQL 伺服器。 您可以使用 PowerShell，以互動方式或在指令碼中建立和管理 Azure 資源。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 雖然 Az.MySql PowerShell 模組處於預覽狀態，但您仍必須使用下列命令，將其與 Az PowerShell 模組分開安裝：`Install-Module -Name Az.MySql -AllowPrerelease`。
> 在 Az.MySql PowerShell 模組正式推出後，其會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

如果這是您第一次使用適用於 MySQL 的 Azure 資料庫服務，則必須註冊 **Microsoft.DBforMySQL** 資源提供者。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶識別碼。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會在 [美國西部]  區域中建立名為 myresourcegroup  的資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器

使用 `New-AzMySqlServer`Cmdlet 建立適用於 MySQL 伺服器的 Azure 資料庫。 一部伺服器可以管理多個資料庫。 一般而言，每個專案或每個使用者會使用個別的資料庫。

下表包含 `New-AzMySqlServer` Cmdlet 常用的參數和範例值清單。

|        **設定**         | **範例值** |                                                                                                                                                             **說明**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 名稱                       | mydemoserver     | 選擇 Azure 中可識別 Azure Database for MySQL 伺服器的全域唯一名稱。 伺服器名稱只能包含字母、數字及連字號 (-) 字元。 在建立過程中，指定的任何大寫字元都會自動轉換成小寫。 此名稱必須包含 3 到 63 個字元。 |
| resourceGroupName          | myresourcegroup  | 提供 Azure 資源群組的名稱。                                                                                                                                                                                                                                                                                            |
| SKU                        | GP_Gen5_2        | SKU 的名稱。 遵循簡短形式的慣例 **pricing-tier\_compute-generation\_vCores**。 如需 Sku 參數的詳細資訊，請參閱此表格後面的資訊。                                                                                                                                           |
| BackupRetentionDay         | 7                | 備份應保留的時間長度。 單位為天。 範圍為 7-35 天。                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | 啟用          | 是否應針對此伺服器啟用異地備援備份。 此值無法針對基本定價層中的伺服器啟用，而且在伺服器建立之後就無法變更。 允許的值：Enabled、Disabled。                                                                                                      |
| Location                   | westus           | 伺服器的 Azure 區域。                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | 啟用          | 是否應針對此伺服器啟用 SSL。 允許的值：Enabled、Disabled。                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | 伺服器的儲存體容量 (單位為 MB)。 有效的 StorageInMb 最小值為 5120 MB，並以 1024 MB 的增量增加。 如需儲存體大小限制的詳細資訊，請參閱[適用於 MySQL 的 Azure 資料庫定價層](./concepts-pricing-tiers.md)。                                                                               |
| 版本                    | 5.7              | MySQL 主要版本。                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | 適用於系統管理員登入的使用者名稱。 此名稱不得為 **azure_superuser**、**admin**、**administrator**、**root**、**guest** 或 **public**。                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | 系統管理員使用者的密碼，其格式為安全字串。 其必須包含 8 到 128 個字元。 您的密碼必須包含下列類別中三種類別的字元：英文大寫字母、英文小寫字母、數字及非英數字元。                                       |

**Sku** 參數值會遵循慣例 **pricing-tier\_compute-generation\_vCores**，如下列範例所示。

- `-Sku B_Gen5_1` 對應於基本、第 5 代和 1 個虛擬核心。 此選項是最小的可用 SKU。
- `-Sku GP_Gen5_32` 對應於一般用途、第 5 代和 32 個 vCore。
- `-Sku MO_Gen5_2` 對應於記憶體最佳化、第 5 代和 2 個 vCore。

如需各區域和各層有效 **Sku**值的相關資訊，請參閱[適用於 MySQL 的 Azure 資料庫定價層](./concepts-pricing-tiers.md)。

下列範例會在 [美國西部]  區域中建立名為 **mydemoserver**的 MySQL 伺服器，其位於 **myresourcegroup** 資源群組中，且伺服器系統管理員登入為 **myadmin**。 這是一般用途定價層中的 Gen 5 伺服器，已啟用 2 個虛擬核心和異地備援備份。 記錄範例第一行中使用的密碼，因為這是 MySQL 伺服器系統管理員帳戶的密碼。

> [!TIP]
> 伺服器名稱會對應至 DNS 名稱，而且在 Azure 中必須是全域唯一的。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

如果您的工作負載只需要輕量計算和 I/O，請考慮使用基本定價層。

> [!IMPORTANT]
> 在基本定價層中建立的伺服器後續無法調整為一般用途或記憶體最佳化，而且無法進行異地複寫。

## <a name="configure-a-firewall-rule"></a>設定防火牆規則

使用 `New-AzMySqlFirewallRule`Cmdlet 建立適用於 MySQL 的 Azure 資料庫伺服器層級防火牆規則。 伺服器層級的防火牆規則允許外部應用程式 (例如 `mysql` 命令列工具或 MySQL Workbench) 穿過適用於 MySQL 的 Azure 資料庫服務防火牆連線到您的伺服器。

下列範例會建立名為 **AllowMyIP** 的防火牆規則，以允許來自特定 IP 位址 192.168.0.1 的連線。 請替換為與您的連線來源位置相對應的 IP 位址或 IP 位址範圍。

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Azure Database for MySQL 的連線透過連接埠 3306 進行通訊。 如果您嘗試從公司網路內進行連線，可能不允許透過連接埠 3306 的輸出流量。 在此案例中，唯有 IT 部門開啟連接埠 3306，您才能連線到伺服器。

## <a name="configure-ssl-settings"></a>進行 SSL 設定

預設會強制執行您的伺服器與用戶端應用程式之間的 SSL 連線。 此預設值可藉由加密透過網際網路的資料流，確保「移動中」  資料的安全性。 針對本快速入門，請停用您伺服器的 SSL 連線。 如需詳細資訊，請參閱[在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫](./howto-configure-ssl.md)。

> [!WARNING]
> 不建議對生產伺服器停用 SSL。

下列範例會在適用於 MySQL 的 Azure 資料庫伺服器上停用 SSL。

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，您必須提供主機資訊和存取認證。 使用下列範例來判斷連線資訊。 請記下 **FullyQualifiedDomainName** 和 **AdministratorLogin** 的值。

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>使用 mysql 命令列工具連線至伺服器

使用 `mysql` 命令列工具連線到伺服器。 若要下載並安裝命令列工具，請參閱 [MySQL 社群下載](https://dev.mysql.com/downloads/shell/)。 對本文中的程式碼範例選取 [試試看] **`mysql` 按鈕，也可以存取 Azure Cloud Shell 中預先安裝的**  命令列工具版本。 其他存取 Azure Cloud Shell 的方法是選取 Azure 入口網站右上方工具列上的 **>_** 按鈕，或造訪 [shell.azure.com](https://shell.azure.com/)。

1. 使用 `mysql` 命令列工具連線至伺服器。

   ```azurepowershell-interactive
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

1. 檢視伺服器狀態。

   ```sql
   mysql> status
   ```

    ```Output
    C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
    Enter password: *************
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65512
    Server version: 5.6.42.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.29, for Win64 (x86_64)

    Connection id:          65512
    Current database:
    Current user:           myadmin@myipaddress
    SSL:                    Not in use
    Using delimiter:        ;
    Server version:         5.6.42.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 1 hour 2 min 12 sec

    Threads: 7  Questions: 952  Slow queries: 0  Opens: 66  Flush tables: 3  Open tables: 16  Queries per second avg: 0.255
    --------------

    mysql>
    ```

如需其他命令，請參閱 [MySQL 5.7 參考手冊 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

## <a name="connect-to-the-server-using-mysql-workbench"></a>使用 MySQL Workbench 來連線到伺服器

1. 在您的用戶端電腦上啟動 MySQL Workbench 應用程式。 若要下載並安裝 MySQL Workbench，請參閱[下載 MySQL Workbench](https://dev.mysql.com/downloads/workbench/)。

1. 在 [設定新連線]  對話方塊的 [參數]  索引標籤上，輸入下列資訊︰

   ![設定新連線](./media/quickstart-create-mysql-server-database-using-azure-powershell/setup-new-connection.png)

    |    **設定**    |           **建議的值**           |                      **說明**                       |
    | ----------------- | --------------------------------------- | ---------------------------------------------------------- |
    | 連線名稱   | 我的連線                           | 指定此連線的標籤                        |
    | 連線方式 | 標準 (TCP/IP)                       | 使用 TCP/IP 通訊協定來連線到適用於 MySQL 的 Azure 資料庫 |
    | 主機名稱          | `mydemoserver.mysql.database.azure.com` | 您先前記下的伺服器名稱                           |
    | 連接埠              | 3306                                    | MySQL 的預設連接埠                                 |
    | 使用者名稱          | myadmin@mydemoserver                    | 您先前記下的伺服器管理員登入                |
    | 密碼          | *************                           | 使用您稍早設定的管理帳戶密碼      |

1. 若要測試參數是否設定正確，請按一下 [測試連線]  按鈕。

1. 選取要連到伺服器的連線。

## <a name="clean-up-resources"></a>清除資源

如果其他快速入門或教學課程不需要在本快速入門中建立的資源，您可以執行下列範例加以刪除。

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本快速入門範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

若只要刪除在本快速入門中建立的伺服器，而不刪除資源群組，請使用 `Remove-AzMySqlServer` Cmdlet。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 PowerShell 來設計適用於 MySQL 的 Azure 資料庫](tutorial-design-database-using-powershell.md)
