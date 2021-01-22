---
title: NVIDIA GPU 驅動程式擴充功能 - Azure Linux VM
description: 用於在執行 Linux 的 N 系列計算虛擬機器上安裝 NVIDIA GPU 驅動程式的 Microsoft Azure 擴充功能。
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: a241086e6a590096cf40cbdb7a84838b14889f73
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678283"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>適用於 Linux 的 NVIDIA GPU 驅動程式擴充功能

## <a name="overview"></a>概觀

這個擴充功能可在 Linux N 系列虛擬機器上安裝 NVIDIA GPU 驅動程式。 視虛擬機器系列而定，擴充功能會安裝 CUDA 或 GRID 驅動程式。 若您使用此擴充功能安裝 NVIDIA 驅動程式，即表示您接受並同意 [NVIDIA End-User License Agreement](https://go.microsoft.com/fwlink/?linkid=874330) (NVIDIA 使用者授權合約) 的條款。 在安裝過程中，VM 可能會重新開機以便完成驅動程式設定。

如需手動安裝驅動程式的指示和目前支援的版本，請參閱[這裡](../linux/n-series-driver-setup.md)。
也可使用擴充功能在 [Windows N 系列虛擬機器](hpccompute-gpu-windows.md)上安裝 NVIDIA GPU 驅動程式。

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

此擴充功能支援下列 OS 發行版，視特定 OS 版本的驅動程式支援而定。

| 散發 | 版本 |
|---|---|
| Linux：Ubuntu | 16.04 LTS、18.04 LTS |
| Linux：Red Hat Enterprise Linux | 7.3、7.4、7.5、7.6、7.7、7。8 |
| Linux：CentOS | 7.3、7.4、7.5、7.6、7.7、7。8 |

### <a name="internet-connectivity"></a>網際網路連線

適用於 NVIDIA GPU 驅動程式的 Microsoft Azure 擴充功能會要求目標 VM 需連線到網際網路並擁有存取權。

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
| type | NvidiaGpuDriverLinux | 字串 |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>設定

所有設定都是選用的。 預設行為是如果驅動程式安裝未要求，則不更新核心，並會安裝最新支援的驅動程式和 CUDA 工具組 (若適用)。

| 名稱 | 描述 | 預設值 | 有效的值 | 資料類型 |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | 即使驅動程式安裝不需要，也會更新核心 | false | true、false | boolean |
| driverVersion | NV：GRID 驅動程式版本<br> NC/ND：CUDA 工具組版本。 系統會自動安裝所選 CUDA 的最新驅動程式。 | 最新 | 支援的驅動程式版本[清單](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) | 字串 |
| installCUDA | 安裝 CUDA 工具組。 只與 NC/ND 系列 VM 相關。 | true | true、false | boolean |


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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

下列範例會鏡像上述 Azure Resource Manager 和 PowerShell 範例。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

下列範例也會新增兩個選擇性的自訂設定，作為非預設驅動程式安裝的範例。 具體來說，它會將作業系統核心更新為最新版本，並安裝特定的 CUDA 工具組版本驅動程式。 同樣地，請注意，[--settings] 是選擇性和預設值。 請注意，更新核心可能會增加延伸模組的安裝時間。 此外，選擇特定 (較舊的) CUDA tolkit 版本可能不一定會與較新的核心相容。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
  }'
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

擴充功能執行輸出會記錄至下列檔案。 請參閱此檔案來追蹤 (任何長時間執行的) 安裝的狀態，以及針對任何失敗進行疑難排解的狀態。

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>結束代碼

| 結束碼 | 意義 | 可能的動作 |
| :---: | --- | --- |
| 0 | 作業已順利完成 |
| 1 | 擴充功能的使用方式不正確 | 檢查執行輸出記錄 |
| 10 | 適用於 Hyper-V 和 Azure 的 Linux Integration Services 無法使用或未安裝 | 檢查 lspci 輸出 |
| 11 | 在此 VM 大小上找不到 NVIDIA GPU | 使用[支援的 VM 大小和 OS](../linux/n-series-driver-setup.md) |
| 12 | 不支援的映像供應項目 |
| 13 | 不支援的 VM 大小 | 請使用 N 系列 VM 進行部署 |
| 14 | 作業失敗 | 檢查執行輸出記錄 |


### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Linux 功能](features-linux.md)。

如需 N 系列虛擬機器的詳細資訊，請參閱 [GPU 最佳化的虛擬機器大小](../sizes-gpu.md)。
