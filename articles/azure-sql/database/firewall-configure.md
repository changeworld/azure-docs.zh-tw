---
title: IP 防火牆規則
description: 針對 Azure SQL Database 或 Azure Synapse Analytics 防火牆中的資料庫設定伺服器層級 IP 防火牆規則。 管理存取權，並設定 SQL Database 的資料庫層級 IP 防火牆規則。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: ce528e268e0ed1e34f53e32196bceef5ad8a2fcb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452491"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database 和 Azure Synapse IP 防火牆規則
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

例如，當您在 Azure SQL Database 中建立新的伺服器，或在名為 *mysqlserver* 的 Azure Synapse Analytics 中，伺服器層級防火牆會封鎖可在 *mysqlserver.database.windows.net*) 存取之伺服器 (的公用端點的所有存取權。 為了簡單起見， *SQL Database* 是用來參考 SQL Database 和 Azure Synapse Analytics。

> [!IMPORTANT]
> 本文「不」適用於 *Azure SQL Database 受控執行個體*。 如需網路設定的相關資訊，請參閱 [將您的應用程式連線至 AZURE SQL 受控執行個體](../managed-instance/connect-application-instance.md)。
>
> Azure Synapse 僅支援伺服器層級 IP 防火牆規則。 它不支援資料庫層級 IP 防火牆規則。

## <a name="how-the-firewall-works"></a>防火牆的運作方式

來自網際網路和 Azure 的連線嘗試必須先通過防火牆，才能到達您的伺服器或資料庫，如下圖所示。

   ![防火牆設定圖表][1]

### <a name="server-level-ip-firewall-rules"></a>伺服器層級 IP 防火牆規則

這些規則可讓用戶端存取整部伺服器，也就是受伺服器管理的所有資料庫。 這些規則會儲存在 *master* 資料庫中。 對於伺服器，您最多可以有 128 個伺服器層級 IP 防火牆規則。 如果您已啟用 [ **允許 Azure 服務和資源存取此伺服器** ] 設定，這會計算為伺服器的單一防火牆規則。
  
您可以使用 Azure 入口網站、PowerShell 或 Transact-sql 語句來設定伺服器層級 IP 防火牆規則。

- 若要使用入口網站或 PowerShell，您必須是訂用帳戶擁有者或訂用帳戶參與者。
- 若要使用 Transact-sql，您必須以伺服器層級主體登入或 Azure Active Directory 系統管理員的身分，連接到 *master* 資料庫。  (伺服器層級 IP 防火牆規則必須先由具有 Azure 層級許可權的使用者建立。 ) 

### <a name="database-level-ip-firewall-rules"></a>資料庫層級 IP 防火牆規則

資料庫層級 IP 防火牆規則可讓用戶端存取某些 (安全) 資料庫。 您可以為每個資料庫建立規則 (包括 *master* 資料庫) ，而且它們會儲存在個別的資料庫中。
  
- 只有在您設定第一個伺服器層級防火牆之後，才可以使用 Transact-sql 語句來建立和管理 master 和使用者資料庫的資料庫層級 IP 防火牆規則。
- 如果您在資料庫層級 IP 防火牆規則中指定的 IP 位址範圍超出伺服器層級 IP 防火牆規則中的範圍，則只有在資料庫層級範圍中具有 IP 位址的用戶端可以存取資料庫。
- 對於資料庫，您最多可以有 128 個資料庫層級 IP 防火牆規則。 如需設定資料庫層級 IP 防火牆規則的詳細資訊，請參閱本文稍後的範例，並查看 [sp_set_database_firewall_rule (Azure SQL Database) ](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)。

### <a name="recommendations-for-how-to-set-firewall-rules"></a>如何設定防火牆規則的建議

建議您盡可能使用資料庫層級 IP 防火牆規則。 這種做法可增強安全性，並提升您資料庫的可攜性。 針對系統管理員使用伺服器層級 IP 防火牆規則。 當您有許多資料庫具有相同的存取需求，而且您不想個別設定每個資料庫時，也可以使用它們。

> [!NOTE]
> 如需可讓業務持續運作的可攜式資料庫資訊，請參閱[災害復原的驗證需求](active-geo-replication-security-configure.md)。

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>伺服器層級與資料庫層級 IP 防火牆規則

*某個資料庫的使用者是否應該完全與另一個資料庫隔離？*

如果 *是*，請使用資料庫層級 IP 防火牆規則來授與存取權。 此方法可避免使用伺服器層級 IP 防火牆規則，以允許透過防火牆存取所有資料庫。 這樣可以減少防禦的深度。

*IP 位址的使用者是否需要存取所有資料庫？*

如果 *是*，請使用伺服器層級 IP 防火牆規則來減少您必須設定 IP 防火牆規則的次數。

*設定 IP 防火牆規則的人員或小組是否只能透過 Azure 入口網站、PowerShell 或 REST API 取得存取權？*

如果是，您必須使用伺服器層級 IP 防火牆規則。 資料庫層級 IP 防火牆規則只能透過 Transact-sql 進行設定。  

*設定 IP 防火牆規則的人員或小組是否禁止在資料庫層級擁有高階許可權？*

若是如此，請使用伺服器層級 IP 防火牆規則。 您至少需要資料庫層級的 *CONTROL database* 許可權，才能透過 transact-sql 設定資料庫層級 IP 防火牆規則。  

*設定或審核 IP 防火牆規則的人員或小組是否集中管理許多 (可能有數百個資料庫) 的 IP 防火牆規則？*

在此案例中，最佳做法取決於您的需求和環境。 伺服器層級 IP 防火牆規則可能會比較容易設定，但是指令碼可以設定資料庫層級的規則。 即使您使用伺服器層級 IP 防火牆規則，您可能還需要審核資料庫層級 IP 防火牆規則，以查看具有資料庫 *CONTROL* 許可權的使用者是否建立資料庫層級 ip 防火牆規則。

*我可以混合使用伺服器層級和資料庫層級 IP 防火牆規則嗎？*

是。 某些使用者（例如系統管理員）可能需要伺服器層級 IP 防火牆規則。 其他使用者 (例如資料庫應用程式的使用者) 可能需要資料庫層級 IP 防火牆規則。

### <a name="connections-from-the-internet"></a>從網際網路的連接

當電腦嘗試從網際網路連線到您的伺服器時，防火牆會先根據連線所要求之資料庫的資料庫層級 IP 防火牆規則，檢查要求的原始 IP 位址。

- 如果位址是在資料庫層級 IP 防火牆規則中指定的範圍內，則會將連接授與包含規則的資料庫。
- 如果位址不在資料庫層級 IP 防火牆規則的範圍內，防火牆會檢查伺服器層級 IP 防火牆規則。 如果位址是在伺服器層級 IP 防火牆規則的範圍內，則會授與連接。 伺服器層級 IP 防火牆規則適用于伺服器管理的所有資料庫。  
- 如果位址不在任何資料庫層級或伺服器層級 IP 防火牆規則的範圍內，連接要求就會失敗。

> [!NOTE]
> 若要從本機電腦存取 Azure SQL Database，請確定您的網路和本機電腦上的防火牆允許 TCP 埠1433上的連出通訊。

### <a name="connections-from-inside-azure"></a>從 Azure 內部連接

若要允許在 Azure 中裝載的應用程式連線到您的 SQL server，必須啟用 Azure 連接。 當來自 Azure 的應用程式嘗試連線到您的伺服器時，防火牆會驗證是否允許 Azure 連接。 您可以設定防火牆規則，並在 [**防火牆和虛擬網路**] 設定中，將 [**允許 Azure 服務和資源存取此伺服器**] 設定為 [**開啟**]，直接從 Azure 入口網站的分頁器開啟此選項。 如果不允許連接，則要求不會到達伺服器。

> [!IMPORTANT]
> 此選項會將防火牆設定為允許所有來自 Azure 的連線，包括來自其他客戶之訂用帳戶的連接。 如果您選取此選項，請確定您的登入和使用者權限會限制只有授權的使用者才能存取。

## <a name="permissions"></a>權限

若要能夠建立及管理 Azure SQL Server 的 IP 防火牆規則，您必須：

- 在 [SQL Server 參與者](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 角色中
- 在 [SQL 安全性管理員](../../role-based-access-control/built-in-roles.md#sql-security-manager) 角色中
- 包含 Azure SQL Server 資源的擁有者

## <a name="create-and-manage-ip-firewall-rules"></a>建立和管理 IP 防火牆規則

您可以使用 [Azure 入口網站](https://portal.azure.com/) ，或使用 [Azure PowerShell](/powershell/module/az.sql)、 [Azure CLI](/cli/azure/sql/server/firewall-rule)或 Azure [REST API](/rest/api/sql/firewallrules/createorupdate)以程式設計方式建立第一個伺服器層級防火牆設定。 您可以使用這些方法或 Transact-sql 來建立和管理其他伺服器層級 IP 防火牆規則。

> [!IMPORTANT]
> 資料庫層級 IP 防火牆規則只能使用 Transact-sql 來建立和管理。

為提升效能，系統會暫時在資料庫層級快取伺服器層級 IP 防火牆規則。 若應重新整理快取，請參閱 [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)。

> [!TIP]
> 您可以使用 [資料庫審核](../../azure-sql/database/auditing-overview.md) 來審核伺服器層級和資料庫層級的防火牆變更。

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>使用 Azure 入口網站來管理伺服器層級 IP 防火牆規則

若要在 Azure 入口網站中設定伺服器層級 IP 防火牆規則，請移至您的資料庫或伺服器的 [總覽] 頁面。

> [!TIP]
> 如需教學課程，請參閱 [使用 Azure 入口網站建立資料庫](single-database-create-quickstart.md)。

#### <a name="from-the-database-overview-page"></a>從資料庫總覽頁面

1. 若要從 [資料庫總覽] 頁面設定伺服器層級 IP 防火牆規則，請在工具列上選取 [ **設定伺服器防火牆** ]，如下圖所示。

    ![伺服器 IP 防火牆規則](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    伺服器的 [防火牆設定] 頁面會隨即開啟。

2. 選取工具列上的 [ **新增用戶端 IP** ]，以新增您所使用之電腦的 ip 位址，然後選取 [ **儲存**]。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則。

    ![設定伺服器層級 IP 防火牆規則](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>從伺服器總覽頁面

您伺服器的 [總覽] 頁面隨即開啟。 它會顯示完整的伺服器名稱 (例如 *mynewserver20170403.database.windows.net*) ，並提供進一步設定的選項。

1. 若要從這個頁面設定伺服器層級規則，請從左側的 [**設定**] 功能表中選取 [**防火牆**]。

2. 選取工具列上的 [ **新增用戶端 IP** ]，以新增您所使用之電腦的 ip 位址，然後選取 [ **儲存**]。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則。

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>使用 Transact-sql 管理 IP 防火牆規則

| 類別目錄檢視或預存程式 | 層級 | 描述 |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |伺服器 |顯示目前的伺服器層級 IP 防火牆規則 |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |伺服器 |建立或更新伺服器層級 IP 防火牆規則 |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |伺服器 |移除伺服器層級 IP 防火牆規則 |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |資料庫 |顯示目前的伺服器層級 IP 防火牆規則 |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |資料庫 |建立或更新伺服器層級 IP 防火牆規則 |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |資料庫 |移除資料庫層級 IP 防火牆規則 |

下列範例會檢查現有的規則、啟用伺服器 *Contoso* 上的 ip 位址範圍，以及刪除 ip 防火牆規則：

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

接下來，新增伺服器層級 IP 防火牆規則。

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

若要刪除伺服器層級 IP 防火牆規則，請執行 *sp_delete_firewall_rule* 預存程式。 下列範例會刪除規則 *ContosoFirewallRule*：

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>使用 PowerShell 管理伺服器層級 IP 防火牆規則

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍支援 PowerShell Azure Resource Manager 模組，但所有開發現在都適用于 Az. Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 和 AzureRm 模組中命令的引數本質上相同。

| Cmdlet | 層級 | 描述 |
| --- | --- | --- |
| [>new-azsqlserverfirewallrule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |伺服器 |返回目前的伺服器層級防火牆規則 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |伺服器 |建立新的伺服器層級防火牆規則 |
| [設定->new-azsqlserverfirewallrule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |伺服器 |更新現有伺服器層級防火牆規則的屬性 |
| [移除->new-azsqlserverfirewallrule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |伺服器 |移除伺服器層級防火牆規則 |

下列範例會使用 PowerShell 設定伺服器層級 IP 防火牆規則：

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> 針對 $servername 指定伺服器名稱，而不是完整的 DNS 名稱，例如指定 **>mysqldbserver.database.windows.net** 而非 **mysqldbserver.database.windows.net**
>
> 如需快速入門內容中的 PowerShell 範例，請參閱 [建立 DB-powershell](powershell-script-content-guide.md) 和 [建立單一資料庫，並使用 PowerShell 設定伺服器層級 IP 防火牆規則](scripts/create-and-configure-database-powershell.md)。

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>使用 CLI 來管理伺服器層級 IP 防火牆規則

| Cmdlet | 層級 | 描述 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|伺服器|建立伺服器 IP 防火牆規則|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|伺服器|列出伺服器上的 IP 防火牆規則|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|伺服器|顯示 IP 防火牆規則的詳細資料|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|伺服器|更新 IP 防火牆規則|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|伺服器|刪除 IP 防火牆規則|

下列範例會使用 CLI 來設定伺服器層級 IP 防火牆規則：

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> 針對 $servername 指定伺服器名稱，而不是完整的 DNS 名稱，例如指定 **>mysqldbserver.database.windows.net** 而非 **mysqldbserver.database.windows.net**
>
> 如需快速入門內容中的 CLI 範例，請參閱 [建立資料庫 Azure CLI](az-cli-script-samples-content-guide.md) ，並 [使用 Azure CLI 來建立單一資料庫和設定伺服器層級 IP 防火牆規則](scripts/create-and-configure-database-cli.md)。

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>使用 REST API 來管理伺服器層級 IP 防火牆規則

| API | 層級 | 描述 |
| --- | --- | --- |
| [列出防火牆規則](/rest/api/sql/firewallrules/listbyserver) |伺服器 |顯示目前的伺服器層級 IP 防火牆規則 |
| [建立或更新防火牆規則](/rest/api/sql/firewallrules/createorupdate) |伺服器 |建立或更新伺服器層級 IP 防火牆規則 |
| [刪除防火牆規則](/rest/api/sql/firewallrules/delete) |伺服器 |移除伺服器層級 IP 防火牆規則 |
| [取得防火牆規則](/rest/api/sql/firewallrules/get) | 伺服器 | 取得伺服器層級 IP 防火牆規則 |

## <a name="troubleshoot-the-database-firewall"></a>針對資料庫防火牆問題進行疑難排解

當 Azure SQL Database 存取未如預期般運作時，請考慮下列幾點。

- **本機防火牆組態：**

  在您的電腦可以存取 Azure SQL Database 之前，您可能需要在電腦上為 TCP 連接埠 1433 建立防火牆例外狀況。 若要在 Azure 雲端界限內進行連線，您可能必須開啟其他埠。 如需詳細資訊，請參閱 [ADO.NET 4.5 和 Azure SQL Database 之埠超過 1433](adonet-v12-develop-direct-route-ports.md)的「SQL Database：在 vs 內部」區段。

- **網路位址轉譯：**

  由於網路位址轉譯 (NAT) ，您的電腦用來連線到 Azure SQL Database 的 IP 位址，可能與您電腦 IP 配置設定中的 IP 位址不同。 若要查看您的電腦用來連接到 Azure 的 IP 位址：
    1. 登入入口網站。
    1. 移至裝載您資料庫的伺服器上的 [ **設定** ] 索引標籤。
    1. **目前的用戶端 Ip 位址** 會顯示在 [**允許的 ip 位址**] 區段中。 針對允許的 **IP 位址** 選取 [**新增**]，以允許這部電腦存取伺服器。

- **允許清單的變更尚未生效：**

  Azure SQL Database 防火牆設定的變更最多可能會延遲五分鐘才會生效。

- **登入未獲授權，或使用了不正確的密碼：**

  如果登入沒有伺服器的許可權，或密碼不正確，則會拒絕與伺服器的連線。 建立防火牆設定只會讓用戶端有 *機會* 嘗試連接到您的伺服器。 用戶端仍然必須提供必要的安全性認證。 如需準備登入的詳細資訊，請參閱 [控制和授與資料庫存取權](logins-create-manage.md)。

- **動態 IP 位址：**

  如果您有使用動態 IP 位址的網際網路連線，而您在通過防火牆時遇到問題，請嘗試下列其中一個解決方案：
  
  - 要求您的網際網路服務提供者輸入指派給存取伺服器之用戶端電腦的 IP 位址範圍。 將該 IP 位址範圍新增為 IP 防火牆規則。
  - 改為針對您的用戶端電腦取得靜態 IP 位址。 將 IP 位址新增為 IP 防火牆規則。

## <a name="next-steps"></a>後續步驟

- 確認您的公司網路環境允許來自計算 IP 位址範圍的輸入通訊 (包括 Azure 資料中心所使用的 SQL 範圍) 。 您可能必須將這些 IP 位址新增至允許清單。 請參閱 [Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。  
- 如需有關建立伺服器層級 IP 防火牆規則的快速入門，請參閱 [在 Azure SQL Database 中建立單一資料庫](single-database-create-quickstart.md)。
- 如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 中資料庫的說明，請參閱 [用戶端快速入門程式碼範例，以 Azure SQL Database](connect-query-content-reference-guide.md#libraries)。
- 如需其他可能需要開啟之埠的詳細資訊，請參閱[ADO.NET 4.5 和 SQL Database 之埠超過 1433](adonet-v12-develop-direct-route-ports.md)的「SQL Database：在 vs 內部」區段
- 如需 Azure SQL Database 安全性的總覽，請參閱 [保護您的資料庫](security-overview.md)。

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
