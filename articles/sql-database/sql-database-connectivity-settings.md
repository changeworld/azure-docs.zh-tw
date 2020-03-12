---
title: Azure SQL Database 和資料倉儲的連線能力設定
description: 本檔說明適用于 Azure SQL 的 TLS 版本選擇和 Proxy 與重新導向設定
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096668"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 連線能力設定
> [!NOTE]
> 本文適用於 Azure SQL Server，以及在 Azure SQL Server 上建立的 SQL Database 和 SQL 資料倉儲資料庫。 為了簡單起見，參考 SQL Database 和 SQL 資料倉儲時都會使用 SQL Database。

> [!IMPORTANT]
> 本文*不*適用**Azure SQL Database 受控執行個體**

本文介紹控制伺服器層級 Azure SQL Database 連線能力的設定。 這些設定適用于與伺服器相關聯的**所有**SQL Database 和 SQL 資料倉儲資料庫。

> [!NOTE]
> 套用這些設定後，它們會**立即生效**，如果您的用戶端不符合每個設定的需求，可能會導致連接中斷。

連線設定可從 [防火牆]**和 [虛擬網路**] 分頁存取，如下列螢幕擷取畫面所示：

 ![連線能力設定的螢幕擷取畫面][1]


## <a name="deny-public-network-access"></a>拒絕公用網路存取
在 Azure 入口網站中，當 [**拒絕公用網路存取**] 設定設為 **[是]** 時，只允許透過私人端點的連接。 當此設定設為 [**否**] 時，用戶端可以使用私用或公用端點進行連接。

將 [**拒絕公用網路存取**] 設定為 **[是]** 之後，使用公用端點的用戶端登入嘗試將會失敗，並出現下列錯誤：

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>透過 PowerShell 變更公用網路存取
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 下列腳本需要[Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 腳本示範如何 `Get` 並 `Set` 邏輯伺服器層級的**公用網路存取**屬性：

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>透過 CLI 變更公用網路存取
> [!IMPORTANT]
> 本節中的所有腳本都需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI
下列 CLI 腳本說明如何在 bash shell 中變更**公用網路存取**：

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>連線原則
連線[原則](sql-database-connectivity-architecture.md#connection-policy)會決定用戶端連接到 Azure SQL Server 的方式。 

## <a name="change-connection-policy-via-powershell"></a>透過 PowerShell 變更連線原則
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 下列腳本需要[Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 腳本說明如何使用 PowerShell 來變更連線原則：

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>透過 Azure CLI 變更連線原則
> [!IMPORTANT]
> 本節中的所有腳本都需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI
下列 CLI 腳本說明如何在 bash shell 中變更連線原則： 

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
下列 CLI 腳本顯示如何從 Windows 命令提示字元（已安裝 Azure CLI）變更連線原則。

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>後續步驟
- 如需有關 Azure SQL Database 中的連線能力的總覽，請參閱[AZURE SQL 連線架構](sql-database-connectivity-architecture.md)
- 如需如何變更 Azure SQL Database 伺服器的 Azure SQL Database 連線原則相關資訊，請參閱 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) \(英文\)。

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
