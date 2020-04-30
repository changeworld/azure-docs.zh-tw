---
title: 在 Azure 上的 Linux VM 中執行 shell 腳本
description: 本主題說明如何使用執行命令功能，在 Azure Linux 虛擬機器中執行腳本
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758613"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>使用執行命令在 Linux VM 中執行 shell 腳本

執行命令功能會使用虛擬機器（VM）代理程式，在 Azure Linux VM 內執行 shell 腳本。 您可以使用這些腳本進行一般電腦或應用程式管理。 它們可協助您快速診斷和修復 VM 存取和網路問題，並讓 VM 恢復正常狀態。

## <a name="benefits"></a>優點

您可以透過多種方式來存取虛擬機器。 執行命令可以使用 VM 代理程式，從遠端在您的虛擬機器上執行腳本。 您可以透過 Linux Vm 的 Azure 入口網站、 [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)或[Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)使用執行命令。

這項功能在您想要在虛擬機器中執行腳本的所有情況下都很有用。 這是疑難排解和補救未開啟 RDP 或 SSH 埠的虛擬機器的其中一種方式，因為網路或系統管理使用者設定不正確。

## <a name="restrictions"></a>限制

當您使用執行命令時，適用下列限制：

* 輸出限制為最後4096個位元組。
* 執行腳本的最短時間約為20秒。
* 根據預設，腳本會在 Linux 上以提高許可權的使用者身分執行。
* 您可以一次執行一個腳本。
* 不支援提示資訊 (互動模式) 的指令碼。
* 您無法取消執行中的腳本。
* 腳本可執行檔時間上限為90分鐘。 之後，腳本就會超時。
* 需要有虛擬機器的輸出連線，才能傳回指令碼結果。

> [!NOTE]
> 若要正確運作，執行命令需要連線（埠443）至 Azure 公用 IP 位址。 如果擴充功能無法存取這些端點，腳本可能會順利執行，但不會傳回結果。 如果您要封鎖虛擬機器上的流量，您可以使用[服務](../../virtual-network/security-overview.md#service-tags)標籤，以允許使用`AzureCloud`標記的流量傳送至 Azure 公用 IP 位址。

## <a name="available-commands"></a>可用的命令

下表顯示 Linux VM 的可用命令清單。 您可以使用**RunShellScript**命令來執行您想要的任何自訂腳本。 當您使用 Azure CLI 或 PowerShell 來執行命令時，您為`--command-id`或`-CommandId`參數提供的值必須是下列其中一個列出的值。 當您指定的值不是可用的命令時，您會收到下列錯誤：

```error
The entity was not found in this Azure location
```

|**名稱**|**描述**|
|---|---|
|**RunShellScript**|執行 Linux shell 腳本。|
|**ifconfig**| 取得所有網路介面的設定。|

## <a name="azure-cli"></a>Azure CLI

下列範例會使用[az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)命令在 AZURE Linux vm 上執行 shell 腳本。

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> 若要以不同的使用者身分執行命令`sudo -u` ，請輸入以指定使用者帳戶。

## <a name="azure-portal"></a>Azure 入口網站

移至[Azure 入口網站](https://portal.azure.com)中的 VM，然後選取 [**作業**] 底下的 [**執行命令**]。 您會看到可在 VM 上執行的可用命令清單。

![命令清單](./media/run-command/run-command-list.png)

選擇要執行的命令。 某些命令可能會有選擇性或必要的輸入參數。 對於這些命令，參數會顯示為文字欄位，以供您提供輸入值。 針對每個命令，您可以藉由展開**view script**來查看正在執行的腳本。 **RunShellScript**與其他命令不同，因為它可讓您提供自己的自訂腳本。

> [!NOTE]
> 內建命令是無法編輯的。

選擇命令之後，請選取 [**執行**] 來執行腳本。 腳本完成之後，它會在 [輸出] 視窗中傳回輸出和任何錯誤。 下列螢幕擷取畫面顯示執行 **ifconfig** 命令的範例輸出。

![執行命令指令碼輸出](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

下列範例會使用[AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) Cmdlet，在 Azure VM 上執行 PowerShell 腳本。 此 Cmdlet 預期 `-ScriptPath` 參數中所參考的指令碼，位於 Cmdlet 執行所在位置的本機環境。

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>限制於執行命令的存取

列出執行命令或顯示命令的詳細資料需要訂用帳戶`Microsoft.Compute/locations/runCommands/read`層級的許可權。 內建的[讀取](../../role-based-access-control/built-in-roles.md#reader)者角色和較高的層級具有此許可權。

執行命令需要訂用`Microsoft.Compute/virtualMachines/runCommand/action`帳戶層級的許可權。 「[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)」角色和較高層級具有此許可權。

您可以使用其中一個[內建角色](../../role-based-access-control/built-in-roles.md)，或建立[自訂角色](../../role-based-access-control/custom-roles.md)以使用執行命令。

## <a name="next-steps"></a>後續步驟

若要瞭解在您的 VM 中遠端執行腳本和命令的其他方式，請參閱[在 LINUX VM 中執行腳本](run-scripts-in-vm.md)。
