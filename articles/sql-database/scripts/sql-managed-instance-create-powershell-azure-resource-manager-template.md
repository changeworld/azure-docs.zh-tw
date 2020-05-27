---
title: 範本範例 - 在 Azure SQL Database 中建立受控執行個體
description: 使用此 Azure PowerShell 範例指令碼在 Azure SQL Database 中建立受控執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: eccc3dcb2731183719833ae99f475eb27e6a05c1
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774514"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>使用 PowerShell 與 Azure Resource Manager 範本在 Azure SQL Database 中建立受控執行個體

您可以使用 Azure PowerShell 程式庫和 Azure Resource Manager 範本建立 Azure SQL Database 受控執行個體。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果選擇在本機安裝和使用 PowerShell，此教學課程需要 AZ PowerShell 1.4.0 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

Azure PowerShell 命令可以使用預先定義的 Azure Resource Manager 範本啟動部署。 範本中可指定下列屬性：

- 執行個體名稱
- SQL 系統管理員使用者名稱和密碼。
- 執行個體的大小 (核心數目和儲存體大小上限)。
- 將放置執行個體的 VNet 和子網路。
- 執行個體的伺服器層級定序 (預覽)。

之後將無法變更執行個體名稱、SQL 系統管理員使用者名稱、VNet/子網路和定序。 您可以變更其他執行個體屬性。

## <a name="prerequisites"></a>Prerequisites

此範例假設您已經為受控執行個體[建立有效的網路環境](../sql-database-managed-instance-create-vnet-subnet.md)或[修改現有的 VNet](../sql-database-managed-instance-configure-vnet-subnet.md)。 此範例會使用 Cmdlet [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)，因此請確定您已安裝下列 PowerShell 模組：

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

下列內容應放置在代表範本的檔案內，此範本將用來建立執行個體：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

我們假設已正確設定子網路的 Azure VNet 已存在。 如果您沒有正確設定的子網路，請使用不同的 [Azure 資源管理範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)來準備網路環境，此動作可以獨立執行或包含在此範本中。

將此檔案的內容儲存成 .json 檔案，並將檔案路徑放入下列 PowerShell 指令碼中，然後變更指令碼中的物件名稱：

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

成功執行指令碼後，即可從所有 Azure 服務和所設定的 IP 位址存取 SQL Database。

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。
