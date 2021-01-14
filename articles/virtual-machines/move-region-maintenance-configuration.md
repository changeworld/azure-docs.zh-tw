---
title: 將維護設定移至另一個 Azure 區域
description: 瞭解如何將 VM 維護設定移至另一個 Azure 區域
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 91a6adecc9cf0db56fa4c433f388b05aa1bdef6a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202907"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>將維護控制設定移至另一個區域

遵循這篇文章，將維護控制設定移至不同的 Azure 區域。 您可能會因為許多原因而想要移動設定。 例如，若要利用新的區域，以部署特定區域中可用的功能或服務，以符合內部原則和治理需求，或為了回應容量規劃。

使用自訂維護設定的[維護控制](maintenance-control.md)，可讓您控制將平臺更新套用至 vm 的方式，以及 Azure 專用主機的應用程式。 有幾個案例可以跨區域移動維護控制：

- 若要移動維護控制設定，而不是與設定相關聯的資源，請遵循本文中的指示。
- 若要移動與維護設定相關聯的資源，但不移動設定本身，請遵循下列 [指示](move-region-maintenance-configuration-resources.md)。
- 若要移動維護設定和與其相關聯的資源，請先依照本文中的指示進行。 然後，請遵循下列 [指示](move-region-maintenance-configuration-resources.md)。

## <a name="prerequisites"></a>先決條件

開始移動維護控制設定之前：

- 維護設定與 Azure Vm 或 Azure 專用主機相關聯。 開始之前，請先確定 VM/主機資源存在於新的區域中。
- 識別碼： 
    - 現有的維護控制設定。
    - 現有設定目前所在的資源群組。 
    - 移至新區域之後將新增設定的資源群組。 
    - 與您想要移動的維護設定相關聯的資源。
    - 檢查新區域中的資源是否與目前的維護設定相關聯。 這些設定在新區域中的名稱可能會與舊的相同，但不是必要的。

## <a name="prepare-and-move"></a>準備及移動 

1. 取得每個訂用帳戶中的所有維護設定。 執行 CLI [az 維護設定清單](/cli/azure/ext/maintenance/maintenance/configuration#ext-maintenance-az-maintenance-configuration-list) 命令來執行此動作，以您的訂用帳戶識別碼取代 $subId。

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 查看訂用帳戶內所傳回的設定記錄資料表清單。 以下為範例。 您的清單將會包含您特定環境的值。

    **名稱** | **位置** | **資源群組**
    --- | --- | ---
    略過維護 | eastus2 | 設定-資源群組
    IgniteDemoConfig | eastus2 | 設定-資源群組
    defaultMaintenanceConfiguration-eastus | eastus | 測試-設定
    

3. 儲存您的清單以供參考。 當您移動設定時，它可協助您確認所有專案都已移動。
4. 作為參考，將每個設定/資源群組對應至新區域中的新資源群組。
5. 使用 [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)或 [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)在新區域中建立新的維護設定。
6. 使用 [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)或 [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)將設定與新區域中的資源產生關聯。


## <a name="verify-the-move"></a>確認移動

移動設定之後，請將新區域中的設定和資源與您備妥的資料表清單進行比較。


## <a name="clean-up-source-resources"></a>清除來源資源

移動之後，請考慮刪除來源區域、 [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)或 [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)中已移動的維護設定。


## <a name="next-steps"></a>後續步驟

如果您需要移動與維護設定相關聯的資源，請遵循下列 [指示](move-region-maintenance-configuration-resources.md) 。 
