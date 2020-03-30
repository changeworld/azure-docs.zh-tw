---
title: 從 Azure 中的範本創建 Windows VM
description: 使用 Resource Manager 範本和 PowerShell 輕鬆地建立新的 Windows VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74065556"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>利用 Resource Manager 範本建立 Windows 虛擬機器

瞭解如何使用 Azure 資源管理器範本和 Azure 雲外殼中的 Azure PowerShell 創建 Windows 虛擬機器。 本文中使用的範本使用單個子網在新虛擬網路中部署運行 Windows Server 的單個虛擬機器。 有關創建 Linux 虛擬機器，請參閱[如何使用 Azure 資源管理器範本創建 Linux 虛擬機器](../linux/create-ssh-secured-vm-from-template.md)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

創建 Azure 虛擬機器通常包括兩個步驟：

- 建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 資源群組必須在虛擬機器之前建立。
- 建立虛擬機器。

下面的示例從[Azure 快速啟動範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)創建 VM。 以下是範本的複本：

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

要運行 PowerShell 腳本，請選擇 **"嘗試"** 以打開 Azure 雲外殼。 要粘貼腳本，請按右鍵 shell，然後選擇 **"粘貼**：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

如果選擇在本地安裝和使用 PowerShell 而不是從 Azure 雲外殼安裝和使用，本教程需要 Azure PowerShell 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

在先前範例中，您已指定存放在 GitHub 中的範本。 您也可以下載或建立範本，並用 `--template-file` 參數指定本機路徑。

以下是一些其他資源：

- 若要了解如何開發 Resource Manager 範本，請參閱 [Azure Resource Manager 文件](/azure/azure-resource-manager/)。
- 要查看 Azure 虛擬機器架構，請參閱[Azure 範本引用](/azure/templates/microsoft.compute/allversions)。
- 要查看更多虛擬機器範本示例，請參閱[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器

上一個腳本中的最後一個 PowerShell 命令顯示虛擬機器名稱。 要連接到虛擬機器，請參閱[如何連接到運行 Windows 的 Azure 虛擬機器並登錄](./connect-logon.md)。

## <a name="next-steps"></a>後續步驟

- 如果部署發生問題，您可以查看[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](../../resource-manager-common-deployment-errors.md)。
- 請參閱[使用 Azure PowerShell 模組建立和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，以了解如何建立和管理虛擬機器。

若要深入了解如何建立範本，請針對您部署的資源類型檢視 JSON 語法和屬性：

- [微軟.網路/公共IP位址](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
