---
title: 在 Azure 中的 Windows VM 中運行 PowerShell 腳本
description: 本主題介紹如何使用運行命令功能在 Azure Windows 虛擬機器中運行 PowerShell 腳本
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749236"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>使用運行命令在 Windows VM 中運行 PowerShell 腳本

運行命令功能使用虛擬機器 （VM） 代理在 Azure Windows VM 中運行 PowerShell 腳本。 您可以使用這些腳本進行常規電腦或應用程式管理。 它們可以説明您快速診斷和修復 VM 訪問和網路問題，並將 VM 恢復到良好狀態。

 

## <a name="benefits"></a>優點

您可以通過多種方式訪問虛擬機器。 運行命令可以使用 VM 代理遠端在虛擬機器上運行腳本。 通過 Azure 門戶[、REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)或 Windows VM[的 PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)使用運行命令。

此功能在希望在虛擬機器中運行腳本的所有方案中都很有用。 它是疑難排解並修復由於網路或管理使用者配置不當而未打開 RDP 或 SSH 埠的虛擬機器的唯一方法之一。

## <a name="restrictions"></a>限制

使用 Run 命令時，以下限制適用：

* 輸出限制為最後 4，096 位元組。
* 運行腳本的最短時間約為 20 秒。
* 腳本在 Windows 上作為系統運行。
* 一次可以運行一個腳本。
* 不支援提示資訊 (互動模式) 的指令碼。
* 不能取消正在運行的腳本。
* 腳本可以運行的最大時間是 90 分鐘。 在那之後，它將超時。
* 需要有虛擬機器的輸出連線，才能傳回指令碼結果。

> [!NOTE]
> 要正常運行，運行命令需要連接到 Azure 公共 IP 位址（埠 443）。 如果擴展無法訪問這些終結點，腳本可能會成功運行，但不會返回結果。 如果要阻止虛擬機器上的流量，則可以使用[服務標記](../../virtual-network/security-overview.md#service-tags)使用`AzureCloud`標記允許流量到 Azure 公共 IP 位址。

## <a name="available-commands"></a>可用的命令

下表顯示 Windows 虛擬機器可用命令的清單。 您可以使用**RunPowerShellScript**命令運行所需的任何自訂腳本。 使用 Azure CLI 或 PowerShell 運行命令時，為`--command-id`或`-CommandId`參數提供的值必須是以下列出的值之一。 指定不是可用命令的值時，將收到此錯誤：

```error
The entity was not found in this Azure location
```

|**名稱**|**描述**|
|---|---|
|**RunPowerShellScript**|運行 PowerShell 腳本。|
|**EnableRemotePS**|設定機器啟用遠端 PowerShell。|
|**EnableAdminAccount**|檢查本地管理員帳戶是否已禁用，如果禁用，則檢查該帳戶。|
|**IPConfig**| 顯示綁定到 TCP/IP 的每個配接器的 IP 位址、子網路遮罩和預設閘道的詳細資訊。|
|**RDPSettings**|檢查登錄設定和網域原則設定。 如果電腦是域的一部分，或將設置修改為預設值，則建議策略操作。|
|**ResetRDPCert**|刪除綁定到 RDP 攔截器的 SSL 憑證，並將 RDP 攔截器安全性還原為預設值。 如果您發現憑證有任何問題，請使用此指令碼。|
|**SetRDPPort**|設置遠端桌面連線的預設或使用者指定的埠號。 為入站訪問埠啟用防火牆規則。|

## <a name="azure-cli"></a>Azure CLI

下面的示例使用[az vm 運行命令](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)命令在 Azure Windows VM 上運行 shell 腳本。

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure 入口網站

轉到[Azure 門戶](https://portal.azure.com)中的 VM，然後選擇 **"操作**"下的 **"運行"命令**。 您將看到要在 VM 上運行的可用命令的清單。

![命令清單](./media/run-command/run-command-list.png)

選擇要執行的命令。 某些命令可能具有可選或所需的輸入參數。 對於這些命令，參數將作為文字欄位顯示，以便您提供輸入值。 對於每個命令，可以通過展開**視圖腳本**來查看正在運行的腳本。 **RunPowerShellScript**不同于其他命令，因為它允許您提供自己的自訂腳本。

> [!NOTE]
> 內建命令是無法編輯的。

選擇該命令後，選擇 **"運行"** 以運行腳本。 腳本完成後，它將返回輸出視窗中的輸出和任何錯誤。 下列螢幕擷取畫面顯示執行 **RDPSettings** 命令的範例輸出。

![執行命令指令碼輸出](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

下面的示例使用[Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) Cmdlet 在 Azure VM 上運行 PowerShell 腳本。 此 Cmdlet 預期 `-ScriptPath` 參數中所參考的指令碼，位於 Cmdlet 執行所在位置的本機環境。

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>限制於執行命令的存取

列出運行命令或顯示命令的詳細資訊需要訂閱級別`Microsoft.Compute/locations/runCommands/read`的許可權。 內置[讀取器](../../role-based-access-control/built-in-roles.md#reader)角色和更高級別具有此許可權。

運行命令需要訂閱級別`Microsoft.Compute/virtualMachines/runCommand/action`的許可權。 [虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色和更高級別具有此許可權。

您可以使用其中一個[內置角色](../../role-based-access-control/built-in-roles.md)，也可以創建[自訂角色](../../role-based-access-control/custom-roles.md)來使用 Run 命令。

## <a name="next-steps"></a>後續步驟

要瞭解在 VM 中遠端運行腳本和命令的其他方法，請參閱[在 Windows VM 中運行腳本](run-scripts-in-vm.md)。
