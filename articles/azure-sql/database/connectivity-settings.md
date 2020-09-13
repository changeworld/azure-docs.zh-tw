---
title: Azure SQL Database 和資料倉儲的連線設定
description: 本檔說明 Azure SQL Database 和 Azure Synapse Analytics 的 (TLS) 版本選擇和 Proxy 與重新導向設定之間的傳輸層安全性
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/06/2020
ms.openlocfilehash: f664ffbfc9aa38dcf8eb7736b28613efb95bde63
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438172"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 連線能力設定
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文介紹的設定可控制與伺服器的連線，以進行 Azure SQL Database 和 Azure Synapse Analytics。 這些設定適用于與伺服器相關聯的 **所有** SQL Database 和 Azure Synapse 資料庫。

> [!IMPORTANT]
> 本文 *不適用於* **Azure SQL 受控執行個體**

您可以從 [ **防火牆和虛擬網路** ] 畫面存取連線設定，如下列螢幕擷取畫面所示：

 ![連線能力設定的螢幕擷取畫面][1]

> [!NOTE]
> 套用這些設定之後，這些設定會 **立即生效** ，如果用戶端不符合每個設定的需求，可能會導致用戶端連線中斷。

## <a name="deny-public-network-access"></a>拒絕公用網路存取

當 [ **拒絕公用網路存取** ] 設定設為 **[是]** 時，只允許透過私人端點的連接。 當此設定設為 [ **否** ] (預設) 時，用戶端可以使用公用端點進行連線 (以 IP 為基礎的防火牆規則、以 VNET 為基礎的防火牆規則) 或私人端點 (使用 Private Link) （如 [網路存取總覽](network-access-controls-overview.md)中所述）。 

 ![與拒絕公用網路存取的連線螢幕擷取畫面][2]

任何嘗試將 [ **拒絕公用網路存取** ] 設定設為 **[是]** ，而不在邏輯伺服器上有任何現有的私人端點時，會出現類似以下的錯誤訊息：  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

當 [ **拒絕公用網路存取** ] 設定設為 **[是]** 時，只允許透過私人端點的連線，而且會拒絕透過公用端點的所有連線，並出現類似以下的錯誤訊息：  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

當 [ **拒絕公用網路存取** ] 設定設為 **[是]** 時，將會拒絕任何新增或更新防火牆規則的嘗試，並出現類似以下的錯誤訊息：

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>透過 PowerShell 變更公用網路存取

> [!IMPORTANT]
> Azure SQL Database 仍支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需瞭解這些 Cmdlet，請參閱[AzureRM。](https://docs.microsoft.com/powershell/module/AzureRM.Sql/) Az 模組和 AzureRm 模組中命令的引數本質上相同。 下列腳本需要 [Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 腳本顯示如何 `Get` 和 `Set` 伺服器層級的 **公用網路存取** 屬性：

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled"
```

## <a name="change-public-network-access-via-cli"></a>透過 CLI 變更公用網路存取

> [!IMPORTANT]
> 本節中的所有腳本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

下列 CLI 腳本說明如何變更 bash shell 中的 **公用網路存取** ：

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>基本 TLS 版本 

最基本的 [傳輸層安全性 (TLS) ](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 版本設定，可讓客戶控制其 Azure SQL Database 所使用的 TLS 版本。

目前，我們支援 TLS 1.0、1.1 和1.2。 設定最短的 TLS 版本，可確保支援後續、較新的 TLS 版本。 例如，選擇大於1.1 的 TLS 版本。 表示只接受具有 TLS 1.1 和1.2 的連線，而且會拒絕 TLS 1.0。 在測試以確認您的應用程式支援該應用程式之後，建議您將最基本的 TLS 版本設定為1.2，因為它包含舊版中找到的弱點修正，而且是 Azure SQL Database 中支援的最高 TLS 版本。

> [!IMPORTANT]
> 最基本的 TLS 版本預設為允許所有版本。 不過，一旦您強制執行 TLS 的版本，就無法還原為預設值。

如果客戶的應用程式依賴舊版的 TLS，建議您根據應用程式的需求來設定最基本的 TLS 版本。 針對依賴應用程式使用未加密連線來連線的客戶，我們建議不要設定任何基本的 TLS 版本。

如需詳細資訊，請參閱 [SQL Database 連接的 TLS 考慮](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

設定最小 TLS 版本之後，如果用戶端使用的 TLS 版本低於伺服器的最小 TLS 版本，其登入嘗試將會失敗，並出現下列錯誤：

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>透過 PowerShell 設定基本的 TLS 版本

> [!IMPORTANT]
> Azure SQL Database 仍支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需瞭解這些 Cmdlet，請參閱[AzureRM。](https://docs.microsoft.com/powershell/module/AzureRM.Sql/) Az 模組和 AzureRm 模組中命令的引數本質上相同。 下列腳本需要 [Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 腳本會示範如何 `Get` 與 `Set` 邏輯伺服器層級的 **最基本 TLS 版本** 屬性：

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>透過 Azure CLI 設定基本的 TLS 版本

> [!IMPORTANT]
> 本節中的所有腳本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

下列 CLI 腳本說明如何變更 bash shell 中的 **最基本 TLS 版本** 設定：

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>變更連接原則

[連接原則](connectivity-architecture.md#connection-policy) 會決定用戶端連線至 Azure SQL Database 的方式。


## <a name="change-connection-policy-via-powershell"></a>透過 PowerShell 變更連接原則

> [!IMPORTANT]
> Azure SQL Database 仍支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需瞭解這些 Cmdlet，請參閱[AzureRM。](https://docs.microsoft.com/powershell/module/AzureRM.Sql/) Az 模組和 AzureRm 模組中命令的引數本質上相同。 下列腳本需要 [Azure PowerShell 模組](/powershell/azure/install-az-ps)。

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

## <a name="change-connection-policy-via-azure-cli"></a>透過 Azure CLI 變更連接原則

> [!IMPORTANT]
> 本節中的所有腳本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

下列 CLI 腳本會示範如何變更 bash shell 中的連接原則：

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

下列 CLI 腳本會示範如何從 Windows 命令提示字元 (使用) 安裝 Azure CLI 來變更連接原則。

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>接下來的步驟

- 如需如何在 Azure SQL Database 中運作的總覽，請參閱連線 [架構](connectivity-architecture.md)
- 如需有關如何變更伺服器連線原則的詳細資訊，請參閱 [連結 [-原則](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)]。

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
