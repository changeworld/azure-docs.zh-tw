---
title: 將維護配置移動到其他 Azure 區域
description: 瞭解如何將 VM 維護配置移動到其他 Azure 區域
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304455"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>將維護控制配置移到其他區域

請按照本文將維護控制配置移動到其他 Azure 區域。 出於多種原因，您可能需要移動配置。 例如，利用新區域、部署特定區域中可用的功能或服務、滿足內部策略和治理要求或回應容量規劃。

通過自訂維護配置進行維護控制，可以控制平臺更新如何應用於[Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)和[Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VM 以及 Azure 專用主機。 有幾個跨區域移動維護控制的方案：

- 要移動維護控制配置（而不是與配置關聯的資源），請按照本文中的說明操作。
- 要移動與維護配置關聯的資源，而不是配置本身，請按照[這些說明操作](move-region-maintenance-configuration-resources.md)。
- 要移動維護配置及其關聯的資源，請先按照本文中的說明操作。 然後，按照[這些說明](move-region-maintenance-configuration-resources.md)。

## <a name="prerequisites"></a>Prerequisites

在開始移動維護控制配置之前：

- 維護配置與 Azure VM 或 Azure 專用主機相關聯。 在開始之前，請確保 VM/主機資源存在於新區域中。
- 識別碼： 
    - 現有的維護控制配置。
    - 現有配置當前駐留在其中的資源組。 
    - 移動到新區域後要向其添加配置的資源組。 
    - 與要移動的維護配置關聯的資源。
    - 檢查新區域中的資源與當前維護配置關聯的資源相同。 配置在新區域中可以具有與舊區域相同的名稱，但這不是必需的。

## <a name="prepare-and-move"></a>準備和移動 

1. 檢索每個訂閱中的所有維護配置。 運行 CLI [az 維護配置清單](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list)命令以執行此操作，將$subId替換為訂閱 ID。

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 查看訂閱中配置記錄的返回表清單。 範例如下。 您的清單將包含特定環境的值。

    **名稱** | **位置** | **資源組**
    --- | --- | ---
    跳過維護 | eastus2 | 配置資源組
    點火演示 | eastus2 | 配置資源組
    預設維護配置 -eastus | eastus | 測試組態
    

3. 保存清單以供參考。 移動配置時，它可以説明您驗證所有內容是否已移動。
4. 作為參考，將每個配置/資源組映射到新區域中的新資源組。
5. 使用[PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)（或[CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)） 在新區域創建新的維護配置。
6. 使用[PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)將配置與新區域中的資源相關聯。


## <a name="verify-the-move"></a>驗證移動

移動配置後，將新區域中的配置和資源與您準備的表清單進行比較。


## <a name="clean-up-source-resources"></a>清理源資源

移動後，請考慮刪除源區域[PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)中的移動維護配置。


## <a name="next-steps"></a>後續步驟

如果需要移動與維護配置關聯的資源，請按照[這些說明](move-region-maintenance-configuration-resources.md)操作。 
