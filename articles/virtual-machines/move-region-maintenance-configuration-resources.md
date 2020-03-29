---
title: 將與維護配置關聯的資源移到其他區域
description: 瞭解如何將與 VM 維護配置關聯的資源移動到其他 Azure 區域
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304442"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>將維護控制配置中的資源移動到其他區域

請按照本文將與維護控制配置關聯的資源移動到其他 Azure 區域。 出於多種原因，您可能需要移動配置。 例如，利用新區域、部署特定區域中可用的功能或服務、滿足內部策略和治理要求或回應容量規劃。

通過自訂維護配置進行維護控制，可以控制平臺更新如何應用於[Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)和[Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VM 以及 Azure 專用主機。 有幾個跨區域移動維護控制的方案：

- 要移動與維護配置關聯的資源，而不是配置本身，請按照本文操作。
- 要移動維護控制配置，但不移動與配置關聯的資源，請按照[這些說明操作](move-region-maintenance-configuration.md)。
- 要移動維護配置和與其關聯的資源，請首先按照[這些說明操作](move-region-maintenance-configuration.md)。 然後，按照本文中的說明操作。

## <a name="prerequisites"></a>Prerequisites

在開始移動與維護控制配置關聯的資源之前：

- 在開始之前，請確保要移動的資源存在於新區域中。
- 驗證與要移動的 Azure VM 和 Azure 專用主機關聯的維護控制配置。 單獨檢查每個資源。 當前無法檢索多個資源的配置。
- 檢索資源的配置時：
    - 請確保對帳戶使用訂閱 ID，而不是使用 Azure 專用主機識別碼。
    - CLI：--輸出表參數僅用於可讀性，可以刪除或更改。
    - PowerShell：格式表名稱參數僅用於可讀性，可以刪除或更改。
    - 如果使用 PowerShell，則如果嘗試列出沒有任何關聯配置的資源的配置，則會收到錯誤。 該錯誤將類似于："操作失敗狀態：'未找到'。 詳細資訊：404 用戶端錯誤：未找到 url"。

    
## <a name="prepare-to-move"></a>準備移動

1. 在開始之前，定義這些變數。 我們為每個公司提供了一個示例。

    **變數** | **詳細資料** | **範例**
    --- | ---
    $subId | 包含維護配置的訂閱 ID | "我們的訂閱 ID"
    $rsrcGroupName | 資源組名稱（Azure VM） | "VM 資源組"
    $vmName | VM 資源名稱 |  "myVM"
    $adhRsrcGroupName |  資源組（專用主機） | "主機資源組"
    $adh | 專用主機名稱 | "我的主機"
    $adhParentName | 父資源名稱 | "東道主群"
    
2. 要使用 PowerShell[獲取-AZ 配置分配](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)命令檢索維護配置，請使用以下命令：

    - 對於 Azure 專用主機，運行：
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - 對於 Azure VM，運行：

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. 要使用 CLI [az 維護分配](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)命令檢索維護配置，請使用以下命令：

    - 對於 Azure 專用主機：

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - 對於 Azure VM：

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>移動 

1. [按照這些說明](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)將 Azure VM 移動到新區域。
2. 移動資源後，根據需要將維護配置重新應用於新區域中的資源，具體取決於您是否移動了維護配置。 您可以使用[PowerShell](../virtual-machines/maintenance-control-powershell.md)或[CLI](../virtual-machines/maintenance-control-cli.md)將維護配置應用於資源。


## <a name="verify-the-move"></a>驗證移動

驗證新區域中的資源，並驗證新區域中的資源的關聯配置。 

## <a name="clean-up-source-resources"></a>清理源資源

移動後，請考慮刪除源區域中的移動資源。


## <a name="next-steps"></a>後續步驟

如果需要移動維護配置，請按照[這些說明](move-region-maintenance-configuration.md)操作。 
