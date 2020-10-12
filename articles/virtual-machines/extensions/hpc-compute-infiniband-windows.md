---
title: 全像驅動程式擴充功能-Azure Windows Vm
description: 在執行 Windows 的 H 和 N 系列計算 Vm 上安裝可感知驅動程式的 Microsoft Azure 延伸模組。
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: ccc9df8078bb7fec8be7d72b0ae18ed416bb10ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096165"
---
# <a name="infiniband-driver-extension-for-windows"></a>適用于 Windows 的可駕駛驅動程式擴充功能

此延伸模組會針對非 SR-IOV 啟用的) 和 OFED 驅動 (程式，安裝適用于啟用 sr-iov) 的驅動程式 (， ( [H 系列](../sizes-hpc.md) 和 [N 系列](../sizes-gpu.md) vm （執行 Windows）) ' r ' 大小。 根據 VM 系列，此延伸模組會安裝適用于 Connect X NIC 的驅動程式。

此外，也可以使用擴充功能來安裝適用于 [Linux vm](hpc-compute-infiniband-linux.md)的不適合驅動程式。

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

此擴充功能支援下列 OS 發行版，視特定 OS 版本的驅動程式支援而定。

| 散發 | 版本 |
|---|---|
| Windows 10 | 核心 |
| Windows Server 2019 | 核心 |
| Windows Server 2016 | 核心 |
| Windows Server 2012 R2 | 核心 |
| Windows Server 2012 | 核心 |

### <a name="internet-connectivity"></a>網際網路連線

您必須將目標 VM 連接到網際網路，並可存取網際網路，才能使用的「使用」的 Microsoft Azure 擴充功能驅動程式。

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 會顯示擴充功能的結構描述。

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>屬性

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | 字串 |
| type | InfiniBandDriverWindows | 字串 |
| typeHandlerVersion | 1.2 | int |



## <a name="deployment"></a>部署


### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本 

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後設定的虛擬機器時，很適合使用範本。

虛擬機器擴充功能的 JSON 設定可以巢狀方式置於虛擬機器資源內部，或放在 Resource Manager JSON 範本的根目錄或最上層。 JSON 設定的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/templates/child-resource-name-type.md)。 

下列範例假設擴充功能以巢狀方式置於虛擬機器資源內部。 在巢狀處理擴充資源時，JSON 會放在虛擬機器的 `"resources": []` 物件中。

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>將擴充功能新增至虛擬機器擴展集

下列範例會在名為 *>myvmss* 的資源群組中部署的現有虛擬機器擴展集（名為 *myResourceGroup*的資源群組）中，將最新版本 1.2 InfiniBandDriverWindows 擴充功能安裝在所有支援 RDMA 的 vm 上：

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell 和 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請執行下列命令。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案。 請參閱此檔案來追蹤安裝的狀態，以及針對任何失敗進行疑難排解。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>結束代碼

下表根據延伸模組安裝程式的結束代碼，描述其意義和建議的動作。

| 錯誤碼 | 意義 | 可能的動作 |
| :---: | --- | --- |
| 0 | 作業已順利完成 |
| 3010 | 作業成功。 需要重新開機。 |
| 100 | 不支援或無法完成作業。 | 可能的原因： PowerShell 版本不受支援、VM 大小不是啟用空間的 VM，無法下載資料。 請檢查記錄檔，以判斷錯誤的成因。 |
| 240、840 | 作業逾時。 | 重試作業。 |
| -1 | 發生例外狀況。 | 請檢查記錄檔，以判斷例外狀況的成因。 |

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家。 或者，您也可以透過 [Azure 支援網站](https://azure.microsoft.com/support/options/)提出支援事件。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需 ( 的「r」大小) 的詳細資訊，請參閱 [H 系列](../sizes-hpc.md) 和 [N 系列](../sizes-gpu.md) vm。

> [!div class="nextstepaction"]
> [深入瞭解 Linux Vm 擴充功能和功能](features-linux.md)