---
title: IP 防火牆規則
description: 在 Azure SQL Database 或 Azure Synapse Analytics 防火牆中設定資料庫的伺服器層級 IP 防火牆規則。 管理 SQL Database 的存取和設定資料庫層級 IP 防火牆規則。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 06/17/2020
ms.openlocfilehash: e18c0470a2d1be8323c2fe1c0780bfd47f3f64cb
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085084"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database 和 Azure Synapse IP 防火牆規則
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

例如，當您在 Azure SQL Database 或名為*mysqlserver*的 Azure Synapse Analytics 中建立新伺服器時，伺服器層級防火牆會封鎖對伺服器之公用端點的所有存取（可在*mysqlserver.database.windows.net*存取）。 為了簡單起見， *SQL Database*用來參考 SQL Database 和 Azure Synapse Analytics （先前為 Azure SQL 資料倉儲）。

> [!IMPORTANT]
> 本文「不」適用於 *Azure SQL Database 受控執行個體*。 如需網路設定的相關資訊，請參閱[將您的應用程式連線到 AZURE SQL 受控執行個體](../managed-instance/connect-application-instance.md)。
>
> Azure Synapse 只支援伺服器層級 IP 防火牆規則。 它不支援資料庫層級 IP 防火牆規則。

## <a name="how-the-firewall-works"></a>防火牆的運作方式

來自網際網路和 Azure 的連接嘗試必須通過防火牆，才能到達您的伺服器或資料庫，如下圖所示。

   ![防火牆設定圖表][1]

### <a name="server-level-ip-firewall-rules"></a>伺服器層級 IP 防火牆規則

這些規則可讓用戶端存取整部伺服器，也就是受伺服器管理的所有資料庫。 這些規則會儲存在*master*資料庫中。 對於伺服器，您最多可以有 128 個伺服器層級 IP 防火牆規則。 如果您已啟用 [**允許 Azure 服務和資源存取此伺服器**] 設定，則這會計算為伺服器的單一防火牆規則。
  
您可以使用 Azure 入口網站、PowerShell 或 Transact-sql 語句來設定伺服器層級 IP 防火牆規則。

- 若要使用入口網站或 PowerShell，您必須是訂用帳戶擁有者或訂用帳戶參與者。
- 若要使用 Transact-sql，您必須以伺服器層級主體登入或 Azure Active Directory 系統管理員的身分，連接到*master*資料庫。 （伺服器層級 IP 防火牆規則必須先由具有 Azure 層級許可權的使用者建立）。

### <a name="database-level-ip-firewall-rules"></a>資料庫層級 IP 防火牆規則

資料庫層級 IP 防火牆規則可讓用戶端存取特定（安全）資料庫。 您可以為每個資料庫（包括*master*資料庫）建立規則，並將它們儲存在個別的資料庫中。
  
- 只有在您設定第一個伺服器層級防火牆之後，才可以使用 Transact-sql 語句來建立和管理主要和使用者資料庫的資料庫層級 IP 防火牆規則。
- 如果您在資料庫層級 IP 防火牆規則中指定的 IP 位址範圍超出伺服器層級 IP 防火牆規則的範圍，只有在資料庫層級範圍中具有 IP 位址的用戶端可以存取資料庫。
- 對於資料庫，您最多可以有 128 個資料庫層級 IP 防火牆規則。 如需設定資料庫層級 IP 防火牆規則的詳細資訊，請參閱本文稍後的範例，並參閱[sp_set_database_firewall_rule （Azure SQL Database）](https://msdn.microsoft.com/library/dn270010.aspx)。

### <a name="recommendations-for-how-to-set-firewall-rules"></a>如何設定防火牆規則的建議

我們建議您盡可能使用資料庫層級 IP 防火牆規則。 這種做法可增強安全性，並提升您資料庫的可攜性。 針對系統管理員使用伺服器層級 IP 防火牆規則。 當您有多個資料庫具有相同的存取需求，而且不想要個別設定每個資料庫時，也可以使用它們。

> [!NOTE]
> 如需可讓業務持續運作的可攜式資料庫資訊，請參閱[災害復原的驗證需求](active-geo-replication-security-configure.md)。

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>伺服器層級與資料庫層級 IP 防火牆規則

*某個資料庫的使用者是否應該完全與另一個資料庫隔離？*

如果*是*，請使用資料庫層級 IP 防火牆規則來授與存取權。 這個方法可避免使用伺服器層級 IP 防火牆規則，以允許透過防火牆存取所有資料庫。 這會降低您的防禦深度。

*IP 位址的使用者是否需要存取所有資料庫？*

如果*是*，請使用伺服器層級 IP 防火牆規則來減少您必須設定 IP 防火牆規則的次數。

*設定 IP 防火牆規則的人員或小組是否只能透過 Azure 入口網站、PowerShell 或 REST API 存取？*

若是如此，您就必須使用伺服器層級 IP 防火牆規則。 資料庫層級 IP 防火牆規則只能透過 Transact-sql 來設定。  

*設定 IP 防火牆規則的人員或小組是否禁止在資料庫層級擁有高階許可權？*

若是如此，請使用伺服器層級 IP 防火牆規則。 您至少需要資料庫層級的*CONTROL DATABASE*許可權，才能透過 transact-sql 設定資料庫層級 IP 防火牆規則。  

*設定或審核 IP 防火牆規則的人員或小組是否會集中管理許多（或許上百）資料庫的 IP 防火牆規則？*

在此案例中，最佳作法取決於您的需求和環境。 伺服器層級 IP 防火牆規則可能會比較容易設定，但是指令碼可以設定資料庫層級的規則。 而且，即使您使用伺服器層級 IP 防火牆規則，您可能還是需要審核資料庫層級 IP 防火牆規則，以查看具有資料庫之*CONTROL*許可權的使用者是否會建立資料庫層級 ip 防火牆規則。

*是否可以混合使用伺服器層級和資料庫層級的 IP 防火牆規則？*

是。 某些使用者（例如系統管理員）可能需要伺服器層級 IP 防火牆規則。 其他使用者 (例如資料庫應用程式的使用者) 可能需要資料庫層級 IP 防火牆規則。

### <a name="connections-from-the-internet"></a>從網際網路的連接

當電腦嘗試從網際網路連線到您的伺服器時，防火牆會先根據連接要求之資料庫的資料庫層級 IP 防火牆規則來檢查要求的原始 IP 位址。

- 如果位址是在資料庫層級 IP 防火牆規則中指定的範圍內，則會將連接授與包含規則的資料庫。
- 如果位址不在資料庫層級 IP 防火牆規則的範圍內，防火牆會檢查伺服器層級 IP 防火牆規則。 如果位址在伺服器層級 IP 防火牆規則的範圍內，則會授與連線。 伺服器層級 IP 防火牆規則適用于伺服器管理的所有資料庫。  
- 如果位址不在任何資料庫層級或伺服器層級 IP 防火牆規則的範圍內，連接要求就會失敗。

> [!NOTE]
> 若要從您的本機電腦存取 Azure SQL Database，請確定網路和本機電腦上的防火牆允許 TCP 埠1433上的連出通訊。

### <a name="connections-from-inside-azure"></a>從 Azure 內部連線

若要允許裝載于 Azure 內的應用程式連線到您的 SQL server，必須啟用 Azure 連線。 當來自 Azure 的應用程式嘗試連線到您的伺服器時，防火牆會驗證是否允許 Azure 連線。 您可以藉由設定防火牆規則，以及在 [**防火牆和虛擬網路**] 設定中，將 [**允許 Azure 服務和資源存取此伺服器**] 切換為 [**開啟**]，直接從 [Azure 入口網站] 分頁開啟此選項。 如果不允許連接，要求就不會到達伺服器。

> [!IMPORTANT]
> 此選項會將防火牆設定為允許所有來自 Azure 的連線，包括來自其他客戶訂用帳戶的連線。 如果您選取此選項，請確定您的登入和使用者權限僅限制授權使用者的存取權。

## <a name="permissions"></a>權限

若要能夠建立及管理 Azure SQL Server 的 IP 防火牆規則，您必須：

- 在 [ [SQL Server 參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)] 角色中
- 在[SQL 安全性管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)角色中
- 包含 Azure SQL Server 的資源擁有者

## <a name="create-and-manage-ip-firewall-rules"></a>建立和管理 IP 防火牆規則

您可以使用[Azure 入口網站](https://portal.azure.com/)或以程式設計方式使用[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql)、 [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)或 Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)來建立第一個伺服器層級防火牆設定。 您可以使用這些方法或 Transact-sql 來建立和管理其他伺服器層級 IP 防火牆規則。

> [!IMPORTANT]
> 資料庫層級 IP 防火牆規則只能使用 Transact-sql 來建立和管理。

為提升效能，系統會暫時在資料庫層級快取伺服器層級 IP 防火牆規則。 若應重新整理快取，請參閱 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。

> [!TIP]
> 您可以使用[資料庫審核](../../azure-sql/database/auditing-overview.md)來審查伺服器層級和資料庫層級的防火牆變更。

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>使用 Azure 入口網站管理伺服器層級 IP 防火牆規則

若要在 Azure 入口網站中設定伺服器層級 IP 防火牆規則，請移至您資料庫或伺服器的 [總覽] 頁面。

> [!TIP]
> 如需教學課程，請參閱[使用 Azure 入口網站建立資料庫](single-database-create-quickstart.md)。

#### <a name="from-the-database-overview-page"></a>從資料庫的 [總覽] 頁面

1. 若要從 [資料庫總覽] 頁面設定伺服器層級 IP 防火牆規則，請選取工具列上的 [**設定伺服器防火牆**]，如下圖所示。

    ![伺服器 IP 防火牆規則](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    伺服器的 [防火牆設定] 頁面隨即開啟。

2. 選取工具列上的 [**新增用戶端 IP** ]，以新增您所使用之電腦的 ip 位址，然後選取 [**儲存**]。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則。

    ![設定伺服器層級 IP 防火牆規則](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>從 [伺服器總覽] 頁面

伺服器的 [總覽] 頁面隨即開啟。 它會顯示完整伺服器名稱（例如*mynewserver20170403.database.windows.net*），並提供進一步設定的選項。

1. 若要從這個頁面設定伺服器層級規則，請從左側的 [**設定**] 功能表中選取 [**防火牆**]。

2. 選取工具列上的 [**新增用戶端 IP** ]，以新增您所使用之電腦的 ip 位址，然後選取 [**儲存**]。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則。

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>使用 Transact-sql 來管理 IP 防火牆規則

| 目錄檢視或預存程式 | 層級 | 描述 |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |伺服器 |顯示目前的伺服器層級 IP 防火牆規則 |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |伺服器 |建立或更新伺服器層級 IP 防火牆規則 |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |伺服器 |移除伺服器層級 IP 防火牆規則 |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |資料庫 |顯示目前的伺服器層級 IP 防火牆規則 |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |資料庫 |建立或更新伺服器層級 IP 防火牆規則 |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |資料庫 |移除資料庫層級 IP 防火牆規則 |

下列範例會審查現有的規則、在*Contoso*伺服器上啟用 ip 位址範圍，並刪除 ip 防火牆規則：

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

接下來，新增伺服器層級 IP 防火牆規則。

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

若要刪除伺服器層級 IP 防火牆規則，請執行*sp_delete_firewall_rule*預存程式。 下列範例會刪除規則*ContosoFirewallRule*：

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>使用 PowerShell 管理伺服器層級 IP 防火牆規則

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有開發現在都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](/powershell/module/AzureRM.Sql/)。 Az 和 AzureRm 模組中命令的引數本質上完全相同。

| Cmdlet | 層級 | 描述 |
| --- | --- | --- |
| [New-azsqlserverfirewallrule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |伺服器 |返回目前的伺服器層級防火牆規則 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |伺服器 |建立新的伺服器層級防火牆規則 |
| [設定-New-azsqlserverfirewallrule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |伺服器 |更新現有伺服器層級防火牆規則的屬性 |
| [移除-New-azsqlserverfirewallrule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |伺服器 |移除伺服器層級防火牆規則 |

下列範例會使用 PowerShell 來設定伺服器層級 IP 防火牆規則：

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> 針對 $servername 指定伺服器名稱，而不是完整 DNS 名稱，例如指定**mysqldbserver**而非**mysqldbserver.database.windows.net**
>
> 如需快速入門內容中的 PowerShell 範例，請參閱[建立 DB-PowerShell](powershell-script-content-guide.md)和[使用 powershell 建立單一資料庫和設定伺服器層級 IP 防火牆規則](scripts/create-and-configure-database-powershell.md)。

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
> 針對 $servername 指定伺服器名稱，而不是完整 DNS 名稱，例如指定**mysqldbserver**而非**mysqldbserver.database.windows.net**
>
> 如需快速入門內容中的 CLI 範例，請參閱[建立 DB-Azure CLI](az-cli-script-samples-content-guide.md)和[建立單一資料庫和使用 Azure CLI 設定伺服器層級 IP 防火牆規則](scripts/create-and-configure-database-cli.md)。

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>使用 REST API 來管理伺服器層級 IP 防火牆規則

| API | 層級 | 描述 |
| --- | --- | --- |
| [列出防火牆規則](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |伺服器 |顯示目前的伺服器層級 IP 防火牆規則 |
| [建立或更新防火牆規則](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |伺服器 |建立或更新伺服器層級 IP 防火牆規則 |
| [刪除防火牆規則](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |伺服器 |移除伺服器層級 IP 防火牆規則 |
| [取得防火牆規則](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | 伺服器 | 取得伺服器層級 IP 防火牆規則 |

## <a name="troubleshoot-the-database-firewall"></a>針對資料庫防火牆進行疑難排解

當 Azure SQL Database 的存取行為不會如預期般運作時，請考慮下列幾點。

- **本機防火牆組態：**

  在您的電腦可以存取 Azure SQL Database 之前，您可能需要在電腦上為 TCP 連接埠 1433 建立防火牆例外狀況。 若要在 Azure 雲端界限內進行連線，您可能必須開啟其他埠。 如需詳細資訊，請參閱[ADO.NET 4.5 和 Azure SQL Database 的埠超過 1433](adonet-v12-develop-direct-route-ports.md)的「SQL Database：外部與內部」區段。

- **網路位址轉譯：**

  由於網路位址轉譯（NAT）的不同，電腦用來連線到 Azure SQL Database 的 IP 位址可能會與您電腦的 IP 設定中的 IP 位址不同。 若要查看您的電腦用來連線到 Azure 的 IP 位址：
    1. 登入入口網站。
    1. 在裝載您資料庫的伺服器上，移至 [**設定**] 索引標籤。
    1. **目前的用戶端 IP 位址**會顯示在 [**允許的 ip 位址**] 區段中。 針對 [**允許的 IP 位址**] 選取 [**新增**]，以允許這部電腦存取伺服器。

- **對允許清單所做的變更尚未生效：**

  Azure SQL Database 防火牆設定的變更最多可能會延遲5分鐘才會生效。

- **登入未獲授權，或使用了不正確的密碼：**

  如果登入沒有伺服器的許可權，或密碼不正確，則會拒絕與伺服器的連接。 建立防火牆設定只會讓用戶端有*機會*嘗試連接到您的伺服器。 用戶端仍然必須提供必要的安全性認證。 如需有關準備登入的詳細資訊，請參閱[控制和授與資料庫存取權](logins-create-manage.md)。

- **動態 IP 位址：**

  如果您有使用動態 IP 位址的網際網路連線，而且在通過防火牆時遇到問題，請嘗試下列其中一個解決方案：
  
  - 向您的網際網路服務提供者要求指派給存取伺服器之用戶端電腦的 IP 位址範圍。 將該 IP 位址範圍新增為 IP 防火牆規則。
  - 改為取得用戶端電腦的靜態 IP 位址。 將 IP 位址新增為 IP 防火牆規則。

## <a name="next-steps"></a>下一步

- 確認您的公司網路環境允許 Azure 資料中心所使用的計算 IP 位址範圍（包括 SQL 範圍）進行輸入通訊。 您可能必須將這些 IP 位址新增至允許清單。 請參閱[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。  
- 如需有關建立伺服器層級 IP 防火牆規則的快速入門，請參閱[在 Azure SQL Database 中建立單一資料庫](single-database-create-quickstart.md)。
- 如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 中資料庫的說明，請參閱[Azure SQL Database 的用戶端快速入門程式碼範例](connect-query-content-reference-guide.md#libraries)。
- 如需您可能需要開啟的其他埠的詳細資訊，請參閱[ADO.NET 4.5 和 SQL Database 的埠超過 1433](adonet-v12-develop-direct-route-ports.md)的「SQL Database：外部與內部」區段
- 如需 Azure SQL Database 安全性的總覽，請參閱[保護您的資料庫](security-overview.md)。

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
