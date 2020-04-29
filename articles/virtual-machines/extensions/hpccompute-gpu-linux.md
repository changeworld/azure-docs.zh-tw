---
title: NVIDIA GPU 驅動程式擴充功能-Azure Linux Vm
description: 用於在執行 Linux 的 N 系列計算虛擬機器上安裝 NVIDIA GPU 驅動程式的 Microsoft Azure 擴充功能。
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: e7f6653043d46925d6a4c35eedaf81224ea6c36d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415795"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>適用於 Linux 的 NVIDIA GPU 驅動程式擴充功能

## <a name="overview"></a>總覽

這個擴充功能可在 Linux N 系列虛擬機器上安裝 NVIDIA GPU 驅動程式。 視虛擬機器系列而定，擴充功能會安裝 CUDA 或 GRID 驅動程式。 若您使用此擴充功能安裝 NVIDIA 驅動程式，即表示您接受並同意 [NVIDIA End-User License Agreement](https://go.microsoft.com/fwlink/?linkid=874330) (NVIDIA 使用者授權合約) 的條款。 在安裝過程中，VM 可能會重新開機以便完成驅動程式設定。

如需手動安裝驅動程式和目前支援版本的指示，請參閱[這裡](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)。
也可使用擴充功能在 [Windows N 系列虛擬機器](hpccompute-gpu-windows.md)上安裝 NVIDIA GPU 驅動程式。

## <a name="prerequisites"></a>先決條件

### <a name="operating-system"></a>作業系統

此擴充功能支援下列 OS 發行版，視特定 OS 版本的驅動程式支援而定。

| 散發 | 版本 |
|---|---|
| Linux：Ubuntu | 16.04 LTS、18.04 LTS |
| Linux：Red Hat Enterprise Linux | 7.3、7.4、7.5、7.6 |
| Linux：CentOS | 7.3、7.4、7.5、7.6 |

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
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>屬性

| Name | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | 字串 |
| type | NvidiaGpuDriverLinux | 字串 |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>設定

所有設定都是選用的。 預設行為是如果驅動程式安裝未要求，則不更新核心，並會安裝最新支援的驅動程式和 CUDA 工具組 (若適用)。

| Name | 描述 | 預設值 | 有效的值 | 資料類型 |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | 即使驅動程式安裝不需要，也會更新核心 | false | true、false | boolean |
| driverVersion | NV：GRID 驅動程式版本<br> NC/ND：CUDA 工具組版本。 系統會自動安裝所選 CUDA 的最新驅動程式。 | 最新 | 方格： "430.30"、"418.70"、"410.92"、"410.71"、"390.75"、"390.57"、"390.42"<br> CUDA："10.0.130"、"9.2.88"、"9.1.85" | 字串 |
| installCUDA | 安裝 CUDA 工具組。 只與 NC/ND 系列 VM 相關。 | true | true、false | boolean |


## <a name="deployment"></a>部署


### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本 

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 部署一或多部需要部署後設定的虛擬機器時，很適合使用範本。

虛擬機器擴充功能的 JSON 設定可以巢狀方式置於虛擬機器資源內部，或放在 Resource Manager JSON 範本的根目錄或最上層。 JSON 設定的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/resource-manager-template-child-resource.md)。 

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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

下列範例反映上述 Azure Resource Manager 和 PowerShell 範例，而且還新增自訂設定作為非預設驅動程式安裝的範例。 具體來說，它會更新 OS 核心並安裝特定 CUDA 工具組版的驅動程式。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.2 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "9.1.85", \
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

擴充功能執行輸出會記錄至下列檔案︰

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

如果您在本文中有任何需要協助的地方，您可以與[MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 azure 專家聯繫。 或者，您可以提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Linux 功能](features-linux.md)。

如需 N 系列虛擬機器的詳細資訊，請參閱 [GPU 最佳化的虛擬機器大小](../linux/sizes-gpu.md)。
