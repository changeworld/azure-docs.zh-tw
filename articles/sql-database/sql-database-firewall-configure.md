---
title: IP 防火牆規則
description: 為 SQL 資料庫或 SQL 資料倉儲防火牆佈建服務器級 IP 防火牆規則。 管理單個或池資料庫的訪問和設定資料庫級 IP 防火牆規則。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/18/2019
ms.openlocfilehash: af88fdf3378a6290c773c658ea6dd3469d7c92cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531272"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL 資料庫和 Azure SQL 資料倉儲 IP 防火牆規則

> [!NOTE]
> 本文適用于 Azure SQL 伺服器以及 Azure SQL 伺服器上的 Azure SQL 資料庫和 Azure SQL 資料倉儲資料庫。 為簡單起見 *，SQL 資料庫*用於引用 SQL 資料庫和 SQL 資料倉儲。

> [!IMPORTANT]
> 本文「不」** 適用於 *Azure SQL Database 受控執行個體*。 有關網路設定的資訊，請參閱[將應用程式連接到 Azure SQL 資料庫託管實例](sql-database-managed-instance-connect-app.md)。

例如，當您創建新的 Azure SQL 伺服器名為*mysqlserver*時，SQL 資料庫防火牆會阻止對伺服器公共終結點的所有訪問（該伺服器可在*mysqlserver.database.windows.net*訪問）。

> [!IMPORTANT]
> SQL 資料倉儲僅支援伺服器級 IP 防火牆規則。 它不支援資料庫級 IP 防火牆規則。

## <a name="how-the-firewall-works"></a>防火牆的工作原理
如下圖所示，來自 Internet 和 Azure 的連接嘗試在到達 SQL 伺服器或 SQL 資料庫之前必須通過防火牆。

   ![防火牆配置圖][1]

### <a name="server-level-ip-firewall-rules"></a>伺服器層級 IP 防火牆規則

  這些規則可讓用戶端存取整個 Azure SQL Server，也就是相同 SQL Database 伺服器內的所有資料庫。 規則存儲在*主*資料庫中。 對於 Azure SQL Server，最多可以有 128 個伺服器級 IP 防火牆規則。 如果啟用了 **"允許 Azure 服務和資源訪問此伺服器**設置"，則這計為 Azure SQL Server 的單個防火牆規則。
  
  可以使用 Azure 門戶、PowerShell 或 Transact-SQL 語句佈建服務器級 IP 防火牆規則。
  - 要使用門戶或 PowerShell，您必須是訂閱擁有者或訂閱參與者。
  - 要使用 Transact-SQL，必須連接到 SQL 資料庫實例作為伺服器級主體登錄名或 Azure 活動目錄管理員。 （伺服器級 IP 防火牆規則必須首先由具有 Azure 級別許可權的使用者創建。

### <a name="database-level-ip-firewall-rules"></a>資料庫層級 IP 防火牆規則

  這些規則可讓用戶端存取同部 SQL Database 伺服器內的特定 (安全) 資料庫。 為每個資料庫（包括*主*資料庫）創建規則，這些規則存儲在各個資料庫中。
  
  只能通過使用 Transact-SQL 語句創建和管理主資料庫和使用者資料庫的資料庫級 IP 防火牆規則，並且只能在配置第一個伺服器級防火牆之後。
  
  如果在資料庫級 IP 防火牆規則中指定的 IP 位址範圍超出伺服器級 IP 防火牆規則的範圍，則只有資料庫級範圍內具有 IP 位址的用戶端才能訪問資料庫。
  
  對於資料庫，您最多可以有 128 個資料庫層級 IP 防火牆規則。 有關設定資料庫級 IP 防火牆規則的詳細資訊，請參閱本文後面的示例，請參閱[sp_set_database_firewall_rule（Azure SQL 資料庫）。](https://msdn.microsoft.com/library/dn270010.aspx)

### <a name="recommendations-for-how-to-set-firewall-rules"></a>有關如何設置防火牆規則的建議

我們建議您盡可能使用資料庫級 IP 防火牆規則。 這種做法增強了安全性，使資料庫更加可移植。 為管理員使用伺服器級 IP 防火牆規則。 當您有許多資料庫具有相同的訪問要求，並且不希望單獨配置每個資料庫時，也可以使用它們。

> [!NOTE]
> 如需可讓業務持續運作的可攜式資料庫資訊，請參閱[災害復原的驗證需求](sql-database-geo-replication-security-config.md)。

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>伺服器層級與資料庫層級 IP 防火牆規則

*某個資料庫的使用者是否應該完全與另一個資料庫隔離？*

如果是 *，* 請使用資料庫級 IP 防火牆規則授予存取權限。 此方法避免使用伺服器級 IP 防火牆規則，這些規則允許通過防火牆訪問所有資料庫。 這將降低你的防禦深度。

*IP 位址的使用者是否需要訪問所有資料庫？*

如果是 *，* 請使用伺服器級 IP 防火牆規則來減少必須配置 IP 防火牆規則的次數。

*配置 IP 防火牆規則的個人或團隊是否僅通過 Azure 門戶、PowerShell 或 REST API 進行存取權限？*

如果是這樣，則必須使用伺服器級 IP 防火牆規則。 資料庫級 IP 防火牆規則只能通過 Transact-SQL 進行配置。  

*是否禁止配置 IP 防火牆規則的個人或團隊在資料庫級別獲得高級許可權？*

如果是這樣，請使用伺服器級 IP 防火牆規則。 至少需要資料庫級別的*CONTROL 資料庫*許可權才能通過 Transact-SQL 設定資料庫級別的 IP 防火牆規則。  

*配置或審核 IP 防火牆規則的人員或團隊是否集中管理許多（可能數百個）資料庫的 IP 防火牆規則？*

在這種情況下，最佳做法由您的需求和環境決定。 伺服器層級 IP 防火牆規則可能會比較容易設定，但是指令碼可以設定資料庫層級的規則。 即使您使用伺服器級 IP 防火牆規則，也可能需要審核資料庫級別的 IP 防火牆規則，以查看資料庫上是否具有*CONTROL*許可權的使用者創建了資料庫級 IP 防火牆規則。

*我可以使用伺服器級和資料庫級 IP 防火牆規則的組合嗎？*

是。 某些使用者（如管理員）可能需要伺服器級 IP 防火牆規則。 其他使用者 (例如資料庫應用程式的使用者) 可能需要資料庫層級 IP 防火牆規則。

### <a name="connections-from-the-internet"></a>從網際網路的連接

當電腦嘗試從 Internet 連接到資料庫伺服器時，防火牆首先根據連接請求的資料庫的資料庫級 IP 防火牆規則檢查請求的原始 IP 位址。

- 如果位址在資料庫級 IP 防火牆規則中指定的範圍內，則連接將授予包含該規則的 SQL 資料庫。
- 如果位址不在資料庫級 IP 防火牆規則範圍內，防火牆將檢查伺服器級 IP 防火牆規則。 如果位址在伺服器級 IP 防火牆規則範圍內，則授予連接。 伺服器層級 IP 防火牆規則適用於 Azure SQL Server 上的所有 SQL Database。  
- 如果位址不在任何資料庫級或伺服器級 IP 防火牆規則中的範圍內，則連接請求將失敗。

> [!NOTE]
> 要從本地電腦訪問 SQL 資料庫，請確保網路上的防火牆和本地電腦上的防火牆允許在 TCP 埠 1433 上進行傳出通信。

### <a name="connections-from-inside-azure"></a>來自 Azure 內部的連接

要允許 Azure 內部託管的應用程式連接到 SQL 伺服器，必須啟用 Azure 連接。 當 Azure 的應用程式嘗試連接到資料庫伺服器時，防火牆將驗證 Azure 連接是否允許。 啟動和結束 IP 位址等於*0.0.0.0*的防火牆設置指示允許 Azure 連接。 通過設置防火牆規則，以及切換 **"允許 Azure 服務和資源以在****防火牆和虛擬網路**設置中訪問此伺服器到**ON"，** 可以直接從 Azure 門戶邊欄選項卡打開此功能。 如果不允許連接，則請求無法到達 SQL 資料庫伺服器。

> [!IMPORTANT]
> 此選項將防火牆配置為允許來自 Azure 的所有連接，包括來自其他客戶的訂閱的連接。 如果選擇此選項，請確保您的登錄名和使用者許可權僅限制對授權使用者的訪問。

## <a name="create-and-manage-ip-firewall-rules"></a>創建和管理 IP 防火牆規則

通過使用[Azure 門戶](https://portal.azure.com/)或使用[Azure PowerShell、Azure](https://docs.microsoft.com/powershell/module/az.sql) [CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)或 Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)以程式設計方式創建第一個伺服器級防火牆設置。 通過使用這些方法或 Transact-SQL 創建和管理其他伺服器級 IP 防火牆規則。

> [!IMPORTANT]
> 資料庫級 IP 防火牆規則只能通過使用 Transact-SQL 創建和管理。

為提升效能，系統會暫時在資料庫層級快取伺服器層級 IP 防火牆規則。 若應重新整理快取，請參閱 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。

> [!TIP]
> 您可以使用 [SQL Database 稽核](sql-database-auditing.md)來稽核伺服器等級和資料庫等級防火牆的變更。

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>使用 Azure 門戶管理伺服器級 IP 防火牆規則

要在 Azure 門戶中設置伺服器級 IP 防火牆規則，請轉到 Azure SQL 資料庫或 SQL 資料庫伺服器的概述頁。

> [!TIP]
> 如需教學課程，請參閱[使用 Azure 入口網站建立 DB](sql-database-single-database-get-started.md)。

#### <a name="from-the-database-overview-page"></a>從資料庫概述頁

1. 要從資料庫概述頁設置伺服器級 IP 防火牆規則，請選擇工具列上的 **"設置伺服器防火牆**"，如下圖所示。 

    ![伺服器 IP 防火牆規則](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    將打開 SQL 資料庫伺服器的**防火牆設置**頁。

2. 選擇在工具列上**添加用戶端 IP**以添加您正在使用的電腦的 IP 位址，然後選擇"**保存**"。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則。

    ![設置伺服器級 IP 防火牆規則](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>從伺服器概述頁

伺服器的概覽頁將打開。 它顯示完全限定的伺服器名稱（如*mynewserver20170403.database.windows.net），* 並提供進一步配置的選項。

1. 要從此版面設定伺服器級規則，請從左側的 **"設置"** 功能表中選擇 **"防火牆**"。

2. 選擇在工具列上**添加用戶端 IP**以添加您正在使用的電腦的 IP 位址，然後選擇"**保存**"。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則。

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>使用 Transact-SQL 管理 IP 防火牆規則

| 目錄檢視或預存程序 | 層級 | 描述 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |伺服器 |顯示目前的伺服器層級 IP 防火牆規則 |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |伺服器 |建立或更新伺服器層級 IP 防火牆規則 |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |伺服器 |移除伺服器層級 IP 防火牆規則 |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |資料庫 |顯示目前的伺服器層級 IP 防火牆規則 |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |資料庫 |建立或更新伺服器層級 IP 防火牆規則 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |資料庫 |移除資料庫層級 IP 防火牆規則 |

下面的示例查看現有規則，*在伺服器上啟用*一系列 IP 位址 ，並刪除 IP 防火牆規則：

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

接下來，新增伺服器層級 IP 防火牆規則。

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

要刪除伺服器級 IP 防火牆規則，請執行*sp_delete_firewall_rule*預存程序。 以下示例刪除規則*Contoso 防火牆規則*：

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>使用 PowerShell 管理伺服器級 IP 防火牆規則 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但現在所有開發都用於 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 和 AzureRm 模組中命令的參數基本相同。

| Cmdlet | 層級 | 描述 |
| --- | --- | --- |
| [獲取-AzSqlServer防火牆規則](/powershell/module/az.sql/get-azsqlserverfirewallrule) |伺服器 |返回目前的伺服器層級防火牆規則 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |伺服器 |建立新的伺服器層級防火牆規則 |
| [設置-AzSqlServer防火牆規則](/powershell/module/az.sql/set-azsqlserverfirewallrule) |伺服器 |更新現有伺服器層級防火牆規則的屬性 |
| [刪除-AzSqlServer防火牆規則](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |伺服器 |移除伺服器層級防火牆規則 |

以下示例使用 PowerShell 設置伺服器級 IP 防火牆規則：

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> 對於$servername指定伺服器名稱而不是完全限定的 DNS 名稱，例如指定**mysqldbserver**而不是**mysqldbserver.database.windows.net**

> [!TIP]
> 有關快速入門上下文中的 PowerShell 示例，請參閱[創建 DB - PowerShell](sql-database-powershell-samples.md)並[創建單個資料庫，並使用 PowerShell 配置 SQL 資料庫伺服器級 IP 防火牆規則](scripts/sql-database-create-and-configure-database-powershell.md)。

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>使用 CLI 管理伺服器級 IP 防火牆規則

| Cmdlet | 層級 | 描述 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|伺服器|建立伺服器 IP 防火牆規則|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|伺服器|列出伺服器上的 IP 防火牆規則|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|伺服器|顯示 IP 防火牆規則的詳細資訊|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|伺服器|更新 IP 防火牆規則|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|伺服器|刪除 IP 防火牆規則|

以下示例使用 CLI 設置伺服器級 IP 防火牆規則：

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> 對於$servername指定伺服器名稱而不是完全限定的 DNS 名稱，例如指定**mysqldbserver**而不是**mysqldbserver.database.windows.net**

> [!TIP]
> 有關快速入門上下文中的 CLI 示例，請參閱[創建資料庫 - Azure CLI](sql-database-cli-samples.md)並[創建單個資料庫，並使用 Azure CLI 配置 SQL 資料庫 IP 防火牆規則](scripts/sql-database-create-and-configure-database-cli.md)。

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>使用 REST API 管理伺服器級 IP 防火牆規則

| API | 層級 | 描述 |
| --- | --- | --- |
| [列出防火牆規則](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |伺服器 |顯示目前的伺服器層級 IP 防火牆規則 |
| [創建或更新防火牆規則](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |伺服器 |建立或更新伺服器層級 IP 防火牆規則 |
| [刪除防火牆規則](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |伺服器 |移除伺服器層級 IP 防火牆規則 |
| [獲取防火牆規則](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | 伺服器 | 取得伺服器層級 IP 防火牆規則 |

## <a name="troubleshoot-the-database-firewall"></a>解決資料庫防火牆故障

當對 SQL 資料庫服務的訪問不按預期進行時，請考慮以下幾點。

- **本機防火牆組態：**

  在電腦可以訪問 SQL 資料庫之前，您可能需要在電腦上為 TCP 埠 1433 創建防火牆異常。 要在 Azure 雲邊界內建立連接，您可能需要打開其他埠。 有關詳細資訊，請參閱埠的"SQL 資料庫：外部與內部"部分[，這些埠ADO.NET 4.5 和 SQL 資料庫超過 1433。](sql-database-develop-direct-route-ports-adonet-v12.md)

- **網路位址翻譯：**

  由於網路位址轉譯 （NAT），電腦用於連接到 SQL 資料庫的 IP 位址可能與電腦 IP 配置設置中的 IP 位址不同。 要查看電腦用於連接到 Azure 的 IP 位址，請執行以下規定：
    1. 登入入口網站。
    1. 轉到承載資料庫的伺服器上的 **"配置**"選項卡。
    1. **當前用戶端 IP 位址**顯示在 **"允許的 IP 位址"部分中**。 選擇 **"添加****允許的 IP 位址**"以允許此電腦訪問伺服器。

- **對允許清單的更改尚未生效：**

  對 SQL 資料庫防火牆配置的更改最多延遲五分鐘才能生效。

- **登錄名未授權，或者使用了不正確的密碼：**

  如果登錄對 SQL 資料庫伺服器沒有許可權，或者密碼不正確，則與該伺服器的連接將被拒絕。 創建防火牆設置僅使用戶端*有機會*嘗試連接到伺服器。 用戶端仍必須提供必要的安全憑據。 有關準備登錄的詳細資訊，請參閱[控制和授予資料庫對 SQL 資料庫和 SQL 資料倉儲的訪問](sql-database-manage-logins.md)。

- **動態 IP 位址：**

  如果您有使用動態 IP 定址的互聯網連接，並且難以通過防火牆，請嘗試以下解決方案之一：
  
  - 向 Internet 服務提供者詢問分配給訪問 SQL 資料庫伺服器的用戶端電腦的 IP 位址範圍。 將該 IP 位址範圍添加為 IP 防火牆規則。
  - 獲取靜態 IP 定址，而不是為用戶端電腦。 將 IP 位址添加為 IP 防火牆規則。

## <a name="next-steps"></a>後續步驟

- 確認公司網路環境允許 Azure 資料中心使用的計算 IP 位址範圍（包括 SQL 範圍）進行入站通信。 您可能需要將這些 IP 位址添加到允許清單中。 請參閱[微軟 Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。  
- 有關創建伺服器級 IP 防火牆規則的快速入門，請參閱創建[Azure SQL 資料庫](sql-database-single-database-get-started.md)。
- 有關從開源或協力廠商應用程式連接到 Azure SQL 資料庫的説明，請參閱[用戶端快速入門代碼示例到 SQL 資料庫](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
- 有關可能需要打開的其他埠的資訊，請參閱 1433 以外的埠的"SQL 資料庫：外部與內部"部分[，用於ADO.NET 4.5 和 SQL 資料庫](sql-database-develop-direct-route-ports-adonet-v12.md)
- 有關 Azure SQL 資料庫安全性的概述，請參閱[保護資料庫](sql-database-security-overview.md)。

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
