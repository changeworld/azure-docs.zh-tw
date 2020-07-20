---
title: PowerShell：建立受控執行個體
titleSuffix: Azure SQL Managed Instance
description: 此文章提供可建立受控執行個體的 Azure PowerShell 範例指令碼。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 455860b2ccdfe3188c4985b64bd76ce51ab991ca
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708459"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>使用 PowerShell 建立受控執行個體

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

此 PowerShell 指令碼範例會在新的虛擬網路中，於專用子網路內建立受控執行個體。 其也會設定虛擬網路的路由表與網路安全性群組。 一旦成功執行指令碼，便可從虛擬網路內或從內部部署環境存取受控執行個體。 請參閱[設定 Azure VM 以連線到 Azure SQL Database 受控執行個體](../connect-vm-instance-configure.md)與[設定從內部部署連線至 Azure SQL 受控執行個體的點對站連線](../point-to-site-p2s-configure.md)。

> [!IMPORTANT]
> 如需了解限制，請參閱[支援的區域](../resource-limits.md#supported-regions)和[支援的訂用帳戶類型](../resource-limits.md#supported-subscription-types)。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果選擇在本機安裝並使用 PowerShell，此教學課程需要 Azure PowerShell 1.4.0 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>清除部署

使用下列命令來移除資源群組及其所有相關聯的資源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列部分命令。 如需有關下表中已使用的和其他命令，請按一下特定命令文件的連結。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | 對虛擬網路新增子網路組態。 |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | 取得資源群組中的虛擬網路。 |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | 設定虛擬網路的目標狀態。 |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | 取得虛擬網路中的子網路。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | 為虛擬網路中的子網路設定目標狀態。 |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | 建立路由表。 |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | 取得路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | 設定路由表的目標狀態。 |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | 建立受控執行個體。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure SQL 受控執行個體 PowerShell 指令碼](../../database/powershell-script-content-guide.md)中找到其他 Azure SQL 受控執行個體 PowerShell 指令碼範例。
