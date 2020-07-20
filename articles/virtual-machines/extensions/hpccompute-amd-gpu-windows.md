---
title: AMD GPU 驅動程式擴充功能-Azure Windows Vm
description: Microsoft Azure 延伸模組，用於在執行 Windows 的 NVv4 系列 Vm 上安裝 AMD GPU 驅動程式。
services: virtual-machines-windows
documentationcenter: ''
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: cbba0401815f6754939cdaeb6e7343cf085dff68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736963"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>適用于 Windows 的 AMD GPU 驅動程式擴充功能

本文概要說明在 Windows [NVv4 系列](https://docs.microsoft.com/azure/virtual-machines/nvv4-series)vm 上部署 AMD GPU 驅動程式的 VM 擴充功能。 當您使用此延伸模組安裝 AMD 驅動程式時，即表示您接受並同意[AMD 使用者授權合約](https://amd.com/radeonsoftwarems)的條款。 在安裝過程中，VM 可能會重新開機以便完成驅動程式設定。

如需手動安裝驅動程式的指示和目前支援的版本，請參閱[這裡](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup)。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統

此擴充功能支援下列作業系統：

| 散發 | 版本 |
|---|---|
| Windows 10 EMS | 組建 1903 |
| Windows 10 | 組建1809 |
| Windows Server 2016 | 核心 |
| Windows Server 2019 | 核心 |

### <a name="internet-connectivity"></a>網際網路連線

適用于 AMD GPU 驅動程式的 Microsoft Azure 擴充功能會要求目標 VM 必須連線到網際網路並擁有存取權。

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
| type | AmdGpuDriverWindows | 字串 |
| typeHandlerVersion | 1.0 | int |


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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
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

擴充功能執行輸出會記錄至下列目錄︰

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>錯誤碼

| 錯誤碼 | 意義 | 可能的動作 |
| :---: | --- | --- |
| 0 | 作業已順利完成 |
| 1 | 作業成功。 需要重新開機。 |
| 100 | 不支援或無法完成作業。 | 可能原因：PowerShell 版本不受支援、VM 大小不是 N 系列 VM、資料下載失敗。 請檢查記錄檔，以判斷錯誤的成因。 |
| 240、840 | 作業逾時。 | 重試作業。 |
| -1 | 發生例外狀況。 | 請檢查記錄檔，以判斷例外狀況的成因。 |
| -5x | 作業因重新開機擱置而中斷。 | 重新啟動 VM。 重新開機後將會繼續安裝。 解除安裝應以手動方式叫用。 |


### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Windows 功能](features-windows.md)。

如需 N 系列虛擬機器的詳細資訊，請參閱 [GPU 最佳化的虛擬機器大小](../windows/sizes-gpu.md)。
