---
title: Azure SQL Database 和 Azure Synapse Analytics 的連線能力設定
description: 本文說明傳輸層安全性 (TLS) 版本選項以及 Proxy 與 Azure SQL Database 和 Azure Synapse Analytics 的重新導向設定。
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: e3422f468d1355245fb31e8f04d5f8625f583c37
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462186"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 連線能力設定
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文介紹的設定可控制與伺服器的連線，以進行 Azure SQL Database 和 Azure Synapse Analytics。 這些設定適用于與伺服器相關聯的所有 SQL Database 和 Azure Synapse Analytics 資料庫。

> [!IMPORTANT]
> 本文不適用於 Azure SQL 受控執行個體。

您可以從 [ **防火牆和虛擬網路** ] 畫面存取連線設定，如下列螢幕擷取畫面所示：

 ![[連線能力設定] 視窗的螢幕擷取畫面。][1]

> [!NOTE]
> 這些設定會在套用之後立即生效。 如果客戶不符合每個設定的需求，可能會遇到連線遺失的情況。

## <a name="deny-public-network-access"></a>拒絕公用網路存取

當 [ **拒絕公用網路存取** ] 設定為 **[是]** 時，只允許透過私人端點的連接。 若此設定為 [ **否** ] (預設值) ，客戶可以使用公用端點 (搭配 IP 型防火牆規則或以虛擬網路為基礎的防火牆規則) 或使用 (Azure Private Link) 的私人端點（如 [網路存取總覽](network-access-controls-overview.md)中所述）進行連線。

 ![顯示 [拒絕公用網路存取] 設定為 [是] 和 [拒絕公用網路存取] 設定為 [否] 時的 [連接] 的圖表。][2]

任何嘗試將 **拒絕公用網路存取權** 設定為 **[是]** ，而不在邏輯伺服器上有任何現有的私人端點時，會出現類似以下的錯誤訊息：  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> 若要在已設定私人端點的邏輯伺服器上定義虛擬網路防火牆規則，請將 [ **拒絕公用網路存取** ] 設定為 [ **否**]。

當 [ **拒絕公用網路存取** ] 設定為 **[是]** 時，只允許透過私人端點的連接。 透過公用端點的所有連線將會遭到拒絕，並出現類似以下的錯誤訊息：  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

當 [ **拒絕公用網路存取** ] 設定為 **[是]** 時，將會拒絕任何新增或更新防火牆規則的嘗試，並出現類似以下的錯誤訊息：

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>透過 PowerShell 變更公用網路存取

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 下列腳本需要 [Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 腳本顯示如何 `Get` 和 `Set` 伺服器層級的 **公用網路存取** 屬性：

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>透過 CLI 變更公用網路存取

> [!IMPORTANT]
> 本節中的所有腳本都需要 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

下列 CLI 腳本說明如何變更 Bash shell 中的 **公用網路存取** 設定：

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>最低 TLS 版本 

最基本的 [傳輸層安全性 (TLS) ](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 版本設定，可讓客戶選擇其 SQL database 所使用的 TLS 版本。

目前，我們支援 TLS 1.0、1.1 和 TLS 1.2。 設定最低 TLS 版本，確保能夠支援較新的 TLS 版本。 例如，選擇 1.1 以上的 TLS 版本，即表示只接受 TLS 1.1 和 1.2 的連線，而且會拒絕與 TLS 1.0 的連線。 在測試以確認應用程式確實支援後，建議您將最小的 TLS 版本設定為 1.2。 此版本包含舊版的弱點修正程式，而且是 Azure SQL Database 支援的最高 TLS 版本。

> [!IMPORTANT]
> 最基本 TLS 版本的預設值是允許所有版本。 在您強制執行 TLS 版本之後，就無法還原為預設值。

針對相依於舊版 TLS 的應用程式客戶，建議根據應用程式的需求來設定最低 TLS 版本。 針對仰賴應用程式使用未加密的連線進行連線的客戶，建議不要設定任何最低 TLS 版本。

如需詳細資訊，請參閱 [SQL Database 連接的 TLS 考慮](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

設定最小 TLS 版本之後，如果客戶使用的 TLS 版本低於伺服器的最小 TLS 版本，其登入嘗試將會失敗，並出現下列錯誤：

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>透過 PowerShell 設定最基本的 TLS 版本

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 下列腳本需要 [Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 腳本會示範如何 `Get` 與 `Set` 邏輯伺服器層級的 **最基本 TLS 版本** 屬性：

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>透過 Azure CLI 設定最基本的 TLS 版本

> [!IMPORTANT]
> 本節中的所有腳本都需要 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

下列 CLI 腳本說明如何變更 Bash shell 中的 **最基本 TLS 版本** 設定：

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>變更連接原則

[連接原則](connectivity-architecture.md#connection-policy) 會決定客戶連接 Azure SQL Database 的方式。

## <a name="change-the-connection-policy-via-powershell"></a>透過 PowerShell 變更連接原則

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 下列腳本需要 [Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 腳本顯示如何使用 PowerShell 來變更連線原則：

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>透過 Azure CLI 變更連接原則

> [!IMPORTANT]
> 本節中的所有腳本都需要 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

下列 CLI 腳本會示範如何變更 Bash shell 中的連接原則：

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>從 Windows 命令提示字元 Azure CLI

下列 CLI 腳本示範如何從 Windows 命令提示字元 (安裝) 的 Azure CLI 來變更連接原則：

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>後續步驟

- 如需如何在 Azure SQL Database 中運作的總覽，請參閱連線 [架構](connectivity-architecture.md)。
- 如需有關如何變更伺服器連線原則的詳細資訊，請參閱 [連結 [-原則](/cli/azure/sql/server/conn-policy)]。

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
