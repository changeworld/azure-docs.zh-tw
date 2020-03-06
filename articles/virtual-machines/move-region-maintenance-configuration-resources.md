---
title: 將與維護設定相關聯的資源移至另一個區域
description: 瞭解如何將與 VM 維護設定相關聯的資源移至另一個 Azure 區域
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304442"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>將維護控制設定中的資源移至另一個區域

請遵循這篇文章，將與維護控制設定相關聯的資源移到不同的 Azure 區域。 基於許多原因，您可能會想要移動設定。 例如，若要利用新的區域，可在特定區域中部署可用的功能或服務，以符合內部原則和治理需求，或為了回應容量規劃。

維護控制（使用自訂維護設定）可讓您控制如何將平臺更新套用至[Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)和[Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) Vm，以及 Azure 專用主機。 跨區域移動維護控制的案例有好幾種：

- 若要移動與維護設定相關聯的資源，而不是設定本身，請遵循這篇文章。
- 若要移動維護控制設定，而不是與設定相關聯的資源，請遵循[這些指示](move-region-maintenance-configuration.md)。
- 若要移動維護設定和其相關聯的資源，請先遵循[這些指示](move-region-maintenance-configuration.md)。 然後，依照這篇文章中的指示進行。

## <a name="prerequisites"></a>Prerequisites

開始移動與維護控制設定相關聯的資源之前：

- 開始之前，請確定您要移動的資源存在於新的區域中。
- 驗證與您想要移動的 Azure Vm 和 Azure 專用主機相關聯的維護控制設定。 個別檢查每個資源。 目前沒有任何方法可以抓取多個資源的設定。
- 抓取資源的設定時：
    - 請確定您使用的是帳戶的訂用帳戶識別碼，而不是 Azure 專用主機識別碼。
    - CLI：--output table 參數僅用於可讀性，而且可以刪除或變更。
    - PowerShell：格式資料表名稱參數僅用於可讀性，而且可以刪除或變更。
    - 如果您使用 PowerShell，如果您嘗試列出沒有任何相關設定之資源的設定，就會收到錯誤。 錯誤會類似：「作業失敗，狀態：「找不到」。 詳細資料：404用戶端錯誤：找不到 url」。

    
## <a name="prepare-to-move"></a>準備移動

1. 開始之前，請先定義這些變數。 我們已提供每個的範例。

    **變數** | **詳細資料** | **範例**
    --- | ---
    $subId | 包含維護設定之訂用帳戶的識別碼 | 「我們的訂用帳戶識別碼」
    $rsrcGroupName | 資源組名（Azure VM） | VMResourceGroup
    $vmName | VM 資源名稱 |  MyVM
    $adhRsrcGroupName |  資源群組（專用主機） | "HostResourceGroup"
    $adh | 專用主機名稱 | Myhost 代表
    $adhParentName | 父資源名稱 | HostGroup
    
2. 若要使用 PowerShell [AZConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)命令來取得維護設定：

    - 若為 Azure 專用主機，請執行：
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - 針對 Azure Vm，請執行：

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. 若要使用 CLI [az 維護指派](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)命令來取出維護設定：

    - 適用于 Azure 專用主機：

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - 針對 Azure Vm：

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>移動 

1. [遵循這些指示](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)，將 Azure vm 移至新的區域。
2. 資源移動之後，視您是否移動維護設定而定，將維護設定重新套用至新區域中的資源。 您可以使用[PowerShell](../virtual-machines/maintenance-control-powershell.md)或[CLI](../virtual-machines/maintenance-control-cli.md)將維護設定套用至資源。


## <a name="verify-the-move"></a>驗證移動

確認新區域中的資源，並確認新區域中資源的相關設定。 

## <a name="clean-up-source-resources"></a>清除來源資源

移動之後，請考慮刪除來源區域中已移動的資源。


## <a name="next-steps"></a>後續步驟

如果您需要移動維護設定，請遵循[這些指示](move-region-maintenance-configuration.md)。 
