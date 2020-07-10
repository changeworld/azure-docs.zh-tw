---
title: 針對 Windows VM 擴充功能失敗進行疑難排解
description: 了解如何針對 Azure Windows VM 擴充功能的失敗進行疑難排解
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 2fa87e860d0f5f5117840b9e230e383cdd6aae7c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187552"
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

