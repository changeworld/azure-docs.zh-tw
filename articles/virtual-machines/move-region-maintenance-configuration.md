---
title: 將維護設定移至另一個 Azure 區域
description: 瞭解如何將 VM 維護設定移至另一個 Azure 區域
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304455"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>將維護控制設定移至另一個區域

請遵循這篇文章，將維護控制設定移至不同的 Azure 區域。 基於許多原因，您可能會想要移動設定。 例如，若要利用新的區域，可在特定區域中部署可用的功能或服務，以符合內部原則和治理需求，或為了回應容量規劃。

維護控制（使用自訂維護設定）可讓您控制如何將平臺更新套用至[Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)和[Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) Vm，以及 Azure 專用主機。 跨區域移動維護控制的案例有好幾種：

- 若要移動維護控制設定，而不是與設定相關聯的資源，請遵循這篇文章中的指示。
- 若要移動與維護設定相關聯的資源，而不是設定本身，請遵循[這些指示](move-region-maintenance-configuration-resources.md)。
- 若要移動維護設定和其相關聯的資源，請先依照這篇文章中的指示進行。 然後，遵循[這些指示](move-region-maintenance-configuration-resources.md)。

## <a name="prerequisites"></a>Prerequisites

開始移動維護控制設定之前：

- 維護設定會與 Azure Vm 或 Azure 專用主機相關聯。 開始之前，請確定 VM/主機資源存在於新的區域中。
- 識別碼： 
    - 現有的維護控制設定。
    - 現有設定目前所在的資源群組。 
    - 移至新區域之後，將新增設定的資源群組。 
    - 與您要移動的維護設定相關聯的資源。
    - 檢查新區域中的資源是否與目前維護設定相關聯。 這些設定在新區域中的名稱可能會與舊的不同，但這不是必要的。

## <a name="prepare-and-move"></a>準備和移動 

1. 取得每個訂用帳戶中的所有維護設定。 執行 CLI [az 維護 configuration list](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list)命令以進行這種動作，將 $subId 取代為您的訂用帳戶識別碼。

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 檢查訂用帳戶內的設定記錄傳回的資料表清單。 範例如下。 您的清單會包含您的特定環境值。

    **名稱** | **位置** | **資源群組**
    --- | --- | ---
    略過維護 | eastus2 | 設定-資源-群組
    IgniteDemoConfig | eastus2 | 設定-資源-群組
    defaultMaintenanceConfiguration-eastus | eastus | 測試-設定
    

3. 儲存您的清單以供參考。 當您移動設定時，它可協助您確認所有專案都已移動。
4. 做為參考，請將每個設定/資源群組對應至新區域中的新資源群組。
5. 使用[PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)，在新的區域中建立新的維護設定。
6. 使用[PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)，將設定與新區域中的資源建立關聯。


## <a name="verify-the-move"></a>驗證移動

移動設定之後，請將新區域中的設定和資源與您準備的資料表清單進行比較。


## <a name="clean-up-source-resources"></a>清除來源資源

移動之後，請考慮刪除來源區域、 [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)或[CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)中已移動的維護設定。


## <a name="next-steps"></a>後續步驟

如果您需要移動與維護設定相關聯的資源，請遵循[這些指示](move-region-maintenance-configuration-resources.md)。 
