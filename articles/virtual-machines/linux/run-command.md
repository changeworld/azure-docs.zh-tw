---
title: 在 Azure 上的 Linux VM 中執行 shell 文稿
description: 本主題介紹如何使用執行指令功能在 Azure Linux 虛擬機器中執行文稿
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758613"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>使用執行指令在 Linux VM 中執行 shell 文稿

執行命令功能使用虛擬機器 (VM) 代理在 Azure Linux VM 中執行 shell 文稿。 您可以使用這些文稿進行一般電腦或應用程式管理。 它們可以説明您快速診斷和修復 VM 訪問和網路問題,並將 VM 恢復到良好狀態。

## <a name="benefits"></a>優點

您可以通過多種方式訪問虛擬機器。 運行命令可以使用 VM 代理遠端在虛擬機器上執行文稿。 在 Linux VM 中,透過 Azure 門戶[、REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)或[Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)使用執行命令。

此功能在希望在虛擬機器中運行腳本的所有方案中都很有用。 它是排除故障並修復由於網路或管理使用者配置不當而未打開 RDP 或 SSH 埠的虛擬機器的唯一方法之一。

## <a name="restrictions"></a>限制

使用 Run 命令時,以下限制適用:

* 輸出限制為最後 4,096 位元組。
* 運行腳本的最短時間約為 20 秒。
* 腳本預設在 Linux 上作為提升用戶運行。
* 一次可以運行一個腳本。
* 不支援提示資訊 (互動模式) 的指令碼。
* 不能取消正在運行的腳本。
* 腳本可以運行的最大時間是 90 分鐘。 之後,腳本將超時。
* 需要有虛擬機器的輸出連線，才能傳回指令碼結果。

> [!NOTE]
> 要正常執行,執行命令需要連接到 Azure 公共 IP 位址(埠 443)。 如果擴展無法訪問這些終結點,腳本可能會成功運行,但不會返回結果。 如果要阻止虛擬機上的流量,則可以使用[服務標記](../../virtual-network/security-overview.md#service-tags)使用`AzureCloud`標記允許流量到 Azure 公共 IP 位址。

## <a name="available-commands"></a>可用的命令

下表顯示 Linux VM 的可用命令清單。 您可以使用**RunShellScript**命令執行所需的任何自訂文稿。 使用 Azure CLI 或 PowerShell`--command-id`執行`-CommandId`命令時,為或參數提供的值必須是以下列出的值之一。 指定不是可用指令的值時,將收到此錯誤:

```error
The entity was not found in this Azure location
```

|**名稱**|**說明**|
|---|---|
|**RunShellScript**|運行 Linux 外殼腳本。|
|**ifconfig**| 獲取所有網路介面的配置。|

## <a name="azure-cli"></a>Azure CLI

下面的範例使用[az vm 執行命令](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)命令在 Azure Linux VM 上運行 shell 腳本。

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> 要以其他使用者身份運行命令,請輸入`sudo -u`以指定使用者帳戶。

## <a name="azure-portal"></a>Azure 入口網站

跳到[Azure 門戶](https://portal.azure.com)的 VM,然後選擇 **「操作**」下的 **「執行」命令**。 您將看到要在 VM 上執行的可用命令的清單。

![命令清單](./media/run-command/run-command-list.png)

選擇要執行的命令。 某些命令可能具有可選或所需的輸入參數。 對這些指令,參數會為文字欄位顯示,以便您提供輸入值。 對於每個命令,可以通過展開**視圖腳本**來查看正在運行的腳本。 **RunShellScript**與其他命令不同,因為它允許您提供自己的自定義腳本。

> [!NOTE]
> 內建命令是無法編輯的。

選擇該命令後,選擇 **「運行」** 以運行文稿。 文稿完成後,它將返回輸出視窗中的輸出和任何錯誤。 下列螢幕擷取畫面顯示執行 **ifconfig** 命令的範例輸出。

![執行命令指令碼輸出](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

下面的範例使用[Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet 在 Azure VM 上運行 PowerShell 腳本。 此 Cmdlet 預期 `-ScriptPath` 參數中所參考的指令碼，位於 Cmdlet 執行所在位置的本機環境。

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>限制於執行命令的存取

列出運行命令或顯示命令的詳細資訊需要訂閱級別`Microsoft.Compute/locations/runCommands/read`的許可權。 內建[讀取器](../../role-based-access-control/built-in-roles.md#reader)角色和更高級別具有此許可權。

運行命令需要訂閱級別`Microsoft.Compute/virtualMachines/runCommand/action`的許可權。 [虛擬機參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色和更高級別具有此許可權。

您可以使用其中一個[內建角色](../../role-based-access-control/built-in-roles.md),也可以創建[自定義角色](../../role-based-access-control/custom-roles.md)來使用 Run 命令。

## <a name="next-steps"></a>後續步驟

要瞭解在 VM 中遠端執行文稿和指令的其他方法,請參閱[在 Linux VM 中執行文稿](run-scripts-in-vm.md)。
