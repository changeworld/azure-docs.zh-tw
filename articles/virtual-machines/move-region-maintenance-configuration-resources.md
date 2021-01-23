---
title: 將與維護設定相關聯的資源移至另一個區域
description: 瞭解如何將與 VM 維護設定相關聯的資源移至另一個 Azure 區域
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 88082c441dafdc7571f2b9775bfc07ebe3ca5aa4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730501"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>將維護控制設定中的資源移至另一個區域

遵循這篇文章，將與維護控制設定相關聯的資源移至不同的 Azure 區域。 您可能會因為許多原因而想要移動設定。 例如，若要利用新的區域，以部署特定區域中可用的功能或服務，以符合內部原則和治理需求，或為了回應容量規劃。

使用自訂維護設定的[維護控制](maintenance-control.md)，可讓您控制將平臺更新套用至 vm 的方式，以及 Azure 專用主機的應用程式。 有幾個案例可以跨區域移動維護控制：

- 若要移動與維護設定相關聯的資源，但不移動設定本身，請遵循這篇文章。
- 若要移動維護控制設定，而不是與設定相關聯的資源，請遵循下列 [指示](move-region-maintenance-configuration.md)。
- 若要移動維護設定和與其相關聯的資源，請先遵循下列 [指示](move-region-maintenance-configuration.md)。 然後，依照本文中的指示進行。

## <a name="prerequisites"></a>Prerequisites

開始移動與維護控制設定相關聯的資源之前：

- 開始之前，請確定您要移動的資源存在於新的區域中。
- 確認與您想要移動的 Azure Vm 和 Azure 專用主機相關聯的維護控制設定。 個別檢查每個資源。 目前沒有任何方法可以取出多個資源的設定。
- 在抓取資源的設定時：
    - 請確定您使用的是帳戶的訂用帳戶識別碼，而非 Azure 專用主機識別碼。
    - CLI：--output table 參數只用于可讀性，而且可以刪除或變更。
    - PowerShell： Format-Table Name 參數只用于可讀性，而且可以刪除或變更。
    - 如果您使用 PowerShell，當您嘗試列出沒有任何相關設定的資源設定時，就會收到錯誤。 此錯誤將類似于：「作業失敗，狀態：「找不到」。 詳細資料：404用戶端錯誤：找不到 url "。

    
## <a name="prepare-to-move"></a>準備移動

1. 開始之前，請先定義這些變數。 我們已提供每個範例。

    **變數** | **詳細資料** | **範例**
    --- | ---
    $subId | 包含維護設定之訂用帳戶的識別碼 | 「我們的訂用帳戶識別碼」
    $rsrcGroupName |  (Azure VM) 的資源組名 | VMResourceGroup
    $vmName | VM 資源名稱 |  MyVM
    $adhRsrcGroupName |   (專用主機的資源群組)  | "HostResourceGroup"
    $adh | 專用主機名稱 | Myhost 代表
    $adhParentName | 父資源名稱 | HostGroup
    
2. 使用 PowerShell [AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) 命令取得維護設定：

    - 針對 Azure 專用主機，請執行：
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - 針對 Azure Vm，請執行：

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. 使用 CLI [az 維護指派](/cli/azure/ext/maintenance/maintenance/assignment) 命令取出維護設定：

    - 針對 Azure 專用主機：

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - 針對 Azure Vm：

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>移動 

1. [請遵循下列指示](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) ，將 Azure vm 移至新的區域。
2. 移動資源之後，請視您是否移動維護設定，將維護設定重新套用至新區域中的資源。 您可以使用 [PowerShell](../virtual-machines/maintenance-control-powershell.md) 或 [CLI](../virtual-machines/maintenance-control-cli.md)將維護設定套用至資源。


## <a name="verify-the-move"></a>確認移動

確認新區域中的資源，並確認新區域中資源的相關設定。 

## <a name="clean-up-source-resources"></a>清除來源資源

移動之後，請考慮刪除來源區域中已移動的資源。


## <a name="next-steps"></a>後續步驟

如果您需要移動維護設定，請遵循下列 [指示](move-region-maintenance-configuration.md) 。 
