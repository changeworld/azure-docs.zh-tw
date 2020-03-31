---
title: 從 Azure 開發人員測試實驗室中的另一個實驗室導入虛擬機器
description: 本文介紹如何將虛擬機器從其他實驗室導入到 Azure DevTest 實驗室中的當前實驗室中。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759511"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>從 Azure 開發人員測試實驗室中的另一個實驗室導入虛擬機器
本文提供有關如何將虛擬機器從其他實驗室導入實驗室的資訊。

## <a name="scenarios"></a>案例
下面是一些需要將 VM 從一個實驗室導入另一個實驗室的情況：

- 團隊中的一個人正在遷移到企業內的另一個組，並希望將開發人員桌面帶到新團隊的 DevTest 實驗室。
- 該組已達到[訂閱級別配額](../azure-resource-manager/management/azure-subscription-service-limits.md)，並希望將團隊拆分為幾個訂閱
- 公司正在轉向快速路由（或其他一些新的網路拓撲），團隊希望移動虛擬機器以使用此新基礎架構

## <a name="solution-and-constraints"></a>解決方案和條件約束
此功能使您能夠將一個實驗室（源）中的 VM 導入到另一個實驗室（目標）。 您可以選擇為進程中的目標 VM 指定新名稱。 導入過程包括所有依賴項，如磁片、計畫、網路設置等。

該過程確實需要一些時間，並受以下因素的影響：

- 連接到源電腦的磁片的數量/大小（因為它是複製操作而不是移動操作）
- 到目的地的距離（例如，美國東部區域到東南亞）。

該過程完成後，源虛擬機器將保持關閉狀態，並且新虛擬機器在目標實驗室中運行。

在計畫將 VM 從一個實驗室導入另一個實驗室時，需要注意兩個關鍵約束：

- 支援跨訂閱和區域導入虛擬機器，但訂閱必須與同一 Azure 活動目錄租戶關聯。
- 在源實驗室中，虛擬機器不得處於可聲明狀態。
- 您是源實驗室中的 VM 擁有者，也是目標實驗室中實驗室的擁有者。
- 目前，此功能僅通過電源殼和 REST API 支援。

## <a name="use-powershell"></a>使用 PowerShell
從[GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)下載導入虛擬機器.ps1 檔。 可以使用該腳本將源實驗室中的單個 VM 或所有 VM 導入目標實驗室。

### <a name="use-powershell-to-import-a-single-vm"></a>使用 PowerShell 導入單個 VM
執行此 powershell 腳本需要標識源 VM 和目標實驗室，並可以選擇提供用於目的電腦的新名稱：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>使用 PowerShell 導入源實驗室中的所有 VM
如果未指定源虛擬機器，腳本將自動導入 DevTest 實驗室中的所有 VM。  例如：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>使用 HTTP REST 導入 VM
REST 調用很簡單。 您可以提供足夠的資訊來標識源和目標資源。 請記住，操作發生在目標實驗室資源上。

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
