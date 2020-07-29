---
title: 在 Azure 的 Linux VM 中執行 Shell 指令碼
description: 本主題描述如何使用執行命令功能在 Azure Linux 虛擬機器中執行指令碼
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 7e8ccc832cdf12176cd88cce0157c08d8bf92507
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372581"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>使用執行命令在 Linux VM 中執行 Shell 指令碼

執行命令功能會使用虛擬機器 (VM) 代理程式在 Azure Linux VM 中執行 Shell 指令碼。 您可以使用這些指令碼，進行一般電腦或應用程式管理。 這些指令碼有助於快速診斷和修復 VM 存取與網路問題，並讓 VM 恢復正常狀態。

## <a name="benefits"></a>優點

您可以透過多種方式來存取虛擬機器。 執行命令可以使用虛擬機器代理程式，在虛擬機器上遠端執行指令碼。 您可透過 Azure 入口網站、[REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) 或適用於 Linux VM 的 [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)來使用 [執行] 命令。

這項功能在您想要在虛擬機器中執行指令碼的所有情況下都很有用。 這是針對因網路或管理使用者設定不當而未開啟 RDP 或 SSH 連接埠的虛擬機器，進行疑難排解和修復的唯一方法。

## <a name="restrictions"></a>限制

當您使用執行命令時，有下列限制：

* 輸出僅限於最後 4,096 個位元組。
* 執行指令碼的最短時間是大約 20 秒。
* 根據預設，執行指令碼的身分為 Linux 上提高權限的使用者。
* 您一次只能執行一個指令碼。
* 不支援提示資訊 (互動模式) 的指令碼。
* 您無法取消執行中的指令碼。
* 指令碼可以執行的最長時間是 90 分鐘。 指令碼會在這個時間之後逾時。
* 需要有虛擬機器的輸出連線，才能傳回指令碼結果。

> [!NOTE]
> 「執行命令」需要連線 (連接埠 443) 到 Azure 公用 IP 位址，才能正常運作。 如果擴充功能無法存取這些端點，指令碼可能會執行成功，但不會傳回結果。 如果您要封鎖虛擬機器上的流量，可以使用[服務標籤](../../virtual-network/security-overview.md#service-tags)，以便利用 `AzureCloud` 標籤來允許送至 Azure 公用 IP 位址的流量。

## <a name="available-commands"></a>可用的命令

下表顯示 Linux VM 的可用命令清單。 您可使用 **RunShellScript** 命令執行想要的任何自訂指令碼。 當您使用 Azure CLI 或 PowerShell 來執行命令時，為 `--command-id` 或 `-CommandId` 參數提供的值必須是下列其中一個列出的值。 當您指定不是可用命令的值時，會收到此錯誤：

```error
The entity was not found in this Azure location
```

|**名稱**|**說明**|
|---|---|
|**RunShellScript**|執行 Linux Shell 指令碼。|
|**ifconfig**| 取得所有網路介面的設定。|

## <a name="azure-cli"></a>Azure CLI

下列範例使用 [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 命令來在 Azure Linux VM 上執行 Shell 指令碼。

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "apt-get update && apt-get install -y nginx"
```

> [!NOTE]
> 若要以不同的使用者身分執行命令，請輸入 `sudo -u` 以指定使用者帳戶。

## <a name="azure-portal"></a>Azure 入口網站

前往 [Azure 入口網站](https://portal.azure.com) 中的 VM，並選取 [作業] 下的 [執行命令]。 您會看到可在虛擬機器上執行之命令的清單。

![命令清單](./media/run-command/run-command-list.png)

選擇要執行的命令。 有些命令可能會有選擇性或必要的輸入參數。 對於這些命令，參數會顯示為可讓您提供輸入值的文字欄位。 您可展開 [檢視指令碼] 來檢視每個命令正在執行的指令碼。 **RunShellScript** 不同於其他命令，因為其可供提供自己的自訂指令碼。

> [!NOTE]
> 內建命令是無法編輯的。

選擇命令之後，請選取 [執行]，以執行指令碼。 指令碼完成後，其會在輸出視窗中傳回輸出和所有錯誤。 下列螢幕擷取畫面顯示執行 **ifconfig** 命令的範例輸出。

![執行命令指令碼輸出](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

下列範例使用 [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) Cmdlet，在 Azure VM 上執行 PowerShell 指令碼。 此 Cmdlet 預期 `-ScriptPath` 參數中所參考的指令碼，位於 Cmdlet 執行所在位置的本機環境。

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>限制於執行命令的存取

列出執行命令或顯示命令的詳細資料需要 `Microsoft.Compute/locations/runCommands/read` 權限。 內建[讀者](../../role-based-access-control/built-in-roles.md#reader)角色和較高層級具備此權限。

執行命令需要 `Microsoft.Compute/virtualMachines/runCommand/action` 權限。 [虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色和較高層級具有此權限。

您可以使用其中一個[內建角色](../../role-based-access-control/built-in-roles.md)或建立[自訂角色](../../role-based-access-control/custom-roles.md)，以使用執行命令。

## <a name="next-steps"></a>後續步驟

請參閱[在 Linux VM 中執行指令碼](run-scripts-in-vm.md)，了解如何以其他方式從遠端在 VM 中執行指令碼和命令。
