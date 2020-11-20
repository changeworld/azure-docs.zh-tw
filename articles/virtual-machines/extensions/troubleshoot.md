---
title: 針對 Windows VM 延伸模組失敗進行疑難排解
description: 了解如何針對 Azure Windows VM 擴充功能的失敗進行疑難排解
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bca826cda8dfe47c341886faaf4a0d66f09d37d2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966338"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>針對 Azure Windows VM 擴充功能的失敗進行疑難排解
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>檢視擴充功能狀態
Azure Resource Manager 範本可以從 Azure PowerShell 執行。 一旦執行範本之後，就可以從 Azure 資源總管或命令列工具檢視延伸模組狀態。

範例如下：

Azure PowerShell：

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

以下是範例輸出：

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>針對擴充功能失敗進行疑難排解

### <a name="rerun-the-extension-on-the-vm"></a>在 VM 上重新執行擴充功能
如果您使用自訂指令碼擴充功能在 VM 上執行指令碼，有時候可能會遇到雖然成功建立了 VM 但指令碼卻失敗的錯誤。 在這樣的情況下，若要從此錯誤中復原，建議您移除延伸模組並再次重新執行範本。
請注意：未來將增強這項功能，以移除對解除安裝延伸模組的需求。

#### <a name="remove-the-extension-from-azure-powershell"></a>從 Azure PowerShell 移除擴充功能
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

一旦移除了延伸模組，範本就可以重新執行並在 VM 上執行指令碼。

### <a name="trigger-a-new-goalstate-to-the-vm"></a>觸發新的 Goalstate 設定至 VM
您可能會注意到延伸模組尚未執行，或因為遺失「Windows Azure CRP 憑證產生器」而無法執行， (該憑證用來保護擴充功能受保護的設定) 的傳輸安全。
從虛擬機器內重新開機 Windows 來賓代理程式，將會自動重新產生該憑證：
- 開啟工作管理員
- 移至 [詳細資料] 索引標籤
- 找出 WindowsAzureGuestAgent.exe 進程
- 以滑鼠右鍵按一下，然後選取 [結束工作]。 此程式將會自動重新開機


您也可以藉由執行「空的更新」來觸發新的 Goalstate 設定至 VM：

Azure PowerShell：

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Azure CLI：

```azurecli
az vm update -g <rgname> -n <vmname>
```

如果「空的更新」無法運作，您可以從 Azure 管理入口網站將新的空資料磁片新增至 VM，並在稍後新增憑證後將其移除。
