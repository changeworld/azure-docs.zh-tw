---
title: Azure SQL 資料庫和資料倉儲的連接設置
description: 本文檔介紹 TLS 版本選擇和代理與 Azure SQL 重定向設置
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366082"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 連接設置
> [!NOTE]
> 本文適用於 Azure SQL Server，以及在 Azure SQL Server 上建立的 SQL Database 和 SQL 資料倉儲資料庫。 為了簡單起見，參考 SQL Database 和 SQL 資料倉儲時都會使用 SQL Database。

> [!IMPORTANT]
> 本文*不適用於* **Azure SQL 資料庫託管實例**

本文介紹了在伺服器級別控制與 Azure SQL 資料庫的連接的設置。 這些設置適用于與伺服器關聯的**所有**SQL 資料庫和 SQL 資料倉儲資料庫。

> [!NOTE]
> 應用這些設置後，它們**將立即生效**，如果用戶端不符合每個設置的要求，則可能導致連接丟失。

可通過**防火牆和虛擬網路**邊欄選項卡訪問連接設置，如下圖所示：

 ![連接設置的螢幕截圖][1]


## <a name="deny-public-network-access"></a>拒絕公共網路訪問
在 Azure 門戶中，當 **"拒絕公共網路訪問**"設置設置為 **"是**"時，只允許通過專用終結點進行連接。 當此設置設置為 **"否"** 時，用戶端可以使用私有終結點或公共終結點進行連接。

設置 **"拒絕公用網路訪問****為是**"後，使用公共終結點的用戶端登錄嘗試將失敗，出現以下錯誤：

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>通過 PowerShell 更改公共網路訪問
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但所有後續開發都針對 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。 以下腳本需要[Azure PowerShell 模組](/powershell/azure/install-az-ps)。

以下 PowerShell 腳本顯示了如何在`Get`邏輯`Set`伺服器級別使用**公共網路訪問**屬性：

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>通過 CLI 更改公共網路訪問
> [!IMPORTANT]
> 本節中的所有腳本都需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>在 bash 外殼中的 Azure CLI
以下 CLI 腳本演示如何在 bash shell 中更改**公共網路訪問**：

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>連線原則
[連接策略](sql-database-connectivity-architecture.md#connection-policy)確定用戶端如何連接到 Azure SQL Server。 

## <a name="change-connection-policy-via-powershell"></a>通過 PowerShell 更改連接策略
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但所有後續開發都針對 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。 以下腳本需要[Azure PowerShell 模組](/powershell/azure/install-az-ps)。

以下 PowerShell 腳本演示如何使用 PowerShell 更改連接策略：

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

## <a name="change-connection-policy-via-azure-cli"></a>通過 Azure CLI 更改連接策略
> [!IMPORTANT]
> 本節中的所有腳本都需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>在 bash 外殼中的 Azure CLI
以下 CLI 腳本演示如何在 bash shell 中更改連接策略： 

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>來自 Windows 命令提示符的 Azure CLI
以下 CLI 腳本演示如何從 Windows 命令提示符（安裝了 Azure CLI）更改連接策略。

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>後續步驟
- 有關連接在 Azure SQL 資料庫中工作原理的概述，請參閱[Azure SQL 連接體系結構](sql-database-connectivity-architecture.md)
- 如需如何變更 Azure SQL Database 伺服器的 Azure SQL Database 連線原則相關資訊，請參閱 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) \(英文\)。

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
