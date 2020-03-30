---
title: 用於 DNS 別名的電源外殼
description: PowerShell Cmdlet（如 New-AzSqlServerDNSAlias）使您能夠將新的用戶端連接重定向到其他 Azure SQL 資料庫伺服器，而無需觸摸任何用戶端配置。
keywords: dns sql database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420395"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>適用於 Azure SQL Database 之 DNS 別名的 PowerShell

本文提供的 PowerShell 指令碼會示範如何管理 Azure SQL Database 的 DNS 別名。

> [!NOTE]
> 本文已更新為使用 Azure PowerShell Az 模組或 Azure CLI。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。
>
> 要瞭解有關 Az 模組和 AzureRM 相容性的更多資訊，請參閱[介紹 Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az)。 有關安裝說明，請參閱[安裝 Azure 電源外殼](/powershell/azure/install-az-ps)或[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="dns-alias-in-connection-string"></a>連接字串中的 DNS 別名

若要連線至特定 Azure SQL Database 伺服器，SQL Server Management Studio (SSMS) 等用戶端可以提供 DNS 別名名稱，而不必提供真實的伺服器名稱。 在下列伺服器字串範例中，any-unique-alias-name** 別名會取代四個節點伺服器字串中第一個以點分隔的節點：

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Prerequisites

如果您想要執行本文中提供的 PowerShell 示範指令碼，則適用下列必要條件：

- 對於免費試用版，Azure 訂閱和帳戶請參閱[Azure 試用版](https://azure.microsoft.com/free/)
- 兩個 Azure SQL 資料庫伺服器

## <a name="example"></a>範例

以下代碼示例首先將文本值分配給多個變數。

要運行代碼，請編輯預留位置值以匹配系統中的實際值。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用的釐米數如下：

- [New-AzSqlServerDNSAlias：](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)在 Azure SQL 資料庫服務系統中創建 DNS 別名。 別名是指資料庫伺服器 1。
- [獲取 AzSqlServerDNSAlias：](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)獲取並列出分配給 SQL DB 伺服器 1 的所有別名。
- [設置-AzSqlServerDNSAlias：](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias)修改別名配置為引用的伺服器名稱，從伺服器 1 到伺服器 2。
- [刪除-AzSqlServerDNSAlias：](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)使用別名的名稱從資料庫伺服器 2 中刪除別名。

若要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

在`Get-Module -ListAvailable Az` *powershell\_ise.exe 中*用於查找版本。

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

使用的命令如下：

- [az sql 伺服器 dns 別名在](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)Azure SQL 資料庫服務系統中創建 DNS 別名。 別名是指資料庫伺服器 1。
- [az sql 伺服器 dns 別名顯示](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)： 獲取並列出分配給 SQL DB 伺服器 1 的所有別名。
- [az sql 伺服器 dns 別名集](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias)： 修改別名配置為引用的伺服器名稱，從伺服器 1 到伺服器 2。
- [az sql 伺服器 dns 別名刪除](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)： 使用別名的名稱從資料庫伺服器 2 中刪除別名。

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

有關 SQL 資料庫的 DNS 別名功能的完整說明，請參閱[Azure SQL 資料庫的 DNS 別名](dns-alias-overview.md)。
