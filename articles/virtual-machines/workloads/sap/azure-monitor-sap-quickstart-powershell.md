---
title: 使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions
description: 使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: workloads
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.reviewer: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28b26b8d4d6acdffb4083d182c6a0ce0539080c9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957362"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions

本文說明如何使用 [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell 模組來建立 Azure Monitor for SAP Solutions 資源。

> [!CAUTION]
> Azure Monitor for SAP Solutions 目前處於公開預覽狀態。 所提供的這個預覽版本並沒有服務等級協定。 不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="requirements"></a>需求

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 如果您選擇使用 Cloud Shell，請參閱 [Azure Cloud Shell 的概觀](../../../cloud-shell/overview.md)以取得詳細資訊。

> [!IMPORTANT]
> **Az.HanaOnAzure** PowerShell 模組處於預覽狀態，因此您必須使用 `Install-Module` Cmdlet 來將其分開安裝。 在此 PowerShell 模組正式推出後，會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](../../../azure-resource-manager/management/overview.md)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會在指定的位置使用指定的名稱建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP 監視器

若要建立 SAP 監視器，請使用 [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) Cmdlet。 下列範例會針對指定的訂用帳戶、資源群組和資源名稱建立 SAP 監視器。

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

若要擷取 SAP 監視器的屬性，請使用 [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) Cmdlet。 下列範例會針對指定的訂用帳戶、資源群組和資源名稱取得 SAP 監視器的屬性。

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>提供者執行個體

若要建立提供者執行個體，請使用 [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) Cmdlet。 下列範例會針對指定的訂用帳戶、資源群組和資源名稱建立提供者執行個體。

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

若要擷取提供者執行個體的屬性，請使用 [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) Cmdlet。 下列範例會針對指定的訂用帳戶、資源群組、SapMonitor 名稱和資源名稱，取得提供者執行個體的屬性。

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>清除資源

如果不需要本文中建立的資源，您可以執行下列範例將其刪除。

### <a name="delete-the-provider-instance"></a>刪除提供者執行個體

若要移除提供者執行個體，請使用 [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) Cmdlet。 下列範例會針對指定的訂用帳戶、資源群組、SapMonitor 名稱和資源名稱，刪除其提供者執行個體。

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>刪除 SAP 監視器

若要移除 SAP 監視器，請使用 [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) Cmdlet。 下列範例會針對指定的訂用帳戶、資源群組和監視器名稱刪除 SAP 監視器。

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>刪除資源群組

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本文章範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Monitor for SAP Solutions](azure-monitor-overview.md)。