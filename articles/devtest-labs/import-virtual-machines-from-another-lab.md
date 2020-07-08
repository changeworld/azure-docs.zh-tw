---
title: 從 Azure DevTest Labs 的另一個實驗室匯入虛擬機器
description: 本文說明如何將虛擬機器從另一個實驗室匯入 Azure DevTest Labs 中的目前實驗室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85475929"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>從 Azure DevTest Labs 的另一個實驗室匯入虛擬機器
本文提供如何將虛擬機器從另一個實驗室匯入實驗室的相關資訊。

## <a name="scenarios"></a>案例
以下是您需要將 Vm 從一個實驗室匯入至另一個實驗室的一些案例：

- 小組中的個人正在移至企業內的另一個群組，並想要將開發人員桌面帶到新團隊的 DevTest Labs。
- 群組已達到訂用帳戶[層級配額](../azure-resource-manager/management/azure-subscription-service-limits.md)，而且想要將小組分割成幾個訂閱
- 公司正轉向 Express Route （或其他一些新的網路拓撲），而小組想要移動虛擬機器以使用這個新的基礎結構

## <a name="solution-and-constraints"></a>解決方案和條件約束
這項功能可讓您將一個實驗室（來源）中的 Vm 匯入至另一個實驗室（目的地）。 您可以選擇性地在進程中為目的地 VM 提供新名稱。 匯入套裝程式含所有相依性，例如磁片、排程、網路設定等等。

此程式需要一些時間，並受到下列因素的影響：

- 連接至來源電腦的磁片數目/大小（因為它是複製作業，而不是移動作業）
- 與目的地的距離（例如，美國東部區域到東南亞）。

程式完成後，來源虛擬機器會保持關閉狀態，而新的虛擬機器會在目的地實驗室中執行。

規劃將 Vm 從一個實驗室匯入至另一個實驗室時，有兩個重要的條件約束需要注意：

- 支援跨訂用帳戶和跨區域的虛擬機器匯入，但訂用帳戶必須與相同的 Azure Active Directory 租使用者相關聯。
- 虛擬機器在來源實驗室中不得處於可宣告狀態。
- 您是來源實驗室中的 VM 擁有者，而在目的地實驗室中是實驗室的擁有者。
- 目前，只有透過 Powershell 和 REST API 才支援這項功能。

## <a name="use-powershell"></a>使用 PowerShell
從[GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)下載 ImportVirtualMachines.ps1 檔案。 您可以使用腳本，將來源實驗室中的單一 VM 或所有 Vm 匯入至目的地實驗室。

### <a name="use-powershell-to-import-a-single-vm"></a>使用 PowerShell 匯入單一 VM
執行此 powershell 腳本需要識別來源 VM 和目的地實驗室，並選擇性地提供要用於目的地電腦的新名稱：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>使用 PowerShell 匯入來源實驗室中的所有 Vm
如果未指定來源虛擬機器，腳本會自動匯入 DevTest Labs 中的所有 Vm。  例如：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>使用 HTTP REST 匯入 VM
REST 呼叫很簡單。 您會提供足夠的資訊來識別來源和目的地資源。 請記住，此作業會在目的地實驗室資源上進行。

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [設定實驗室的原則](devtest-lab-set-lab-policy.md)
- [常見問題集](devtest-lab-faq.md)
