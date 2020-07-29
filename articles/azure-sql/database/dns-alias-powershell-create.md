---
title: DNS 別名（PowerShell & Azure CLI）
description: PowerShell 和 Azure CLI Cmdlet 可讓您將新的用戶端連線重新導向至 Azure 中不同的 SQL server，而不必接觸任何用戶端設定。
keywords: dns sql database
ms.custom: seo-lt-2019 sqldbrb=1
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 3acbdd5c933bc2010a26e2039ece378774055d30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84038759"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>適用於 Azure SQL Database 之 DNS 別名的 PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文提供 PowerShell 腳本，示範如何管理裝載您 Azure SQL Database 之[SQL server](logical-servers.md)的 DNS 別名。

> [!NOTE]
> 本文已更新為使用 Azure PowerShell Az 模組或 Azure CLI。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。
>
> 若要深入瞭解 Az 模組和 AzureRM 相容性，請參閱[Azure PowerShell az 模組簡介](/powershell/azure/new-azureps-module-az)。 如需安裝指示，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)或[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="dns-alias-in-connection-string"></a>連接字串中的 DNS 別名

若要連接[邏輯 SQL server](logical-servers.md)，SQL SERVER MANAGEMENT STUDIO （SSMS）之類的用戶端可以提供 DNS 別名名稱，而不是真正的伺服器名稱。 在下列伺服器字串範例中，any-unique-alias-name** 別名會取代四個節點伺服器字串中第一個以點分隔的節點：

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>必要條件

如果您想要執行本文中提供的 PowerShell 示範指令碼，則適用下列必要條件：

- Azure 訂用帳戶和帳戶：如需免費試用，請參閱[azure 試用](https://azure.microsoft.com/free/)
- 兩個伺服器

## <a name="example"></a>範例

下列程式碼範例一開始會將常值指派給數個變數。

若要執行程式碼，請編輯預留位置值，以符合您系統中的實際值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用的 Cmdlet 如下所示：

- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)：在 Azure SQL Database 服務系統中建立 DNS 別名。 別名指的是伺服器1。
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)：取得並列出指派給伺服器1的所有別名。
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias)：修改別名已設定為參考的伺服器名稱（從伺服器1到伺服器2）。
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)：使用別名的名稱，移除伺服器2中的別名。

若要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

`Get-Module -ListAvailable Az`在*powershell \_ise.exe*中使用，以尋找版本。

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用的命令如下所示：

- [az sql server dns 別名 create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)：建立伺服器的 dns 別名。 別名指的是伺服器1。
- [az sql server dns 別名 show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)：取得並列出指派給伺服器1的所有別名。
- [az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias)：修改別名已設定為參考的伺服器名稱（從伺服器1到伺服器2）。
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)：使用別名的名稱，移除伺服器2中的別名。

若要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>後續步驟

如需 SQL Database 的 DNS 別名功能的完整說明，請參閱[Azure SQL Database 的 dns 別名](../../sql-database/dns-alias-overview.md)。
