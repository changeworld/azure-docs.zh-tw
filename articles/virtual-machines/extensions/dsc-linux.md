---
title: Linux 的 Azure DSC 擴展
description: 安裝 OMI 和 DSC 套件，以允許使用「期望的狀態設定」來設定 Azure Linux VM。
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250617"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Linux 的 DSC 擴展（微軟.OSTC 擴展.DSCForLinux）

所需的狀態配置 （DSC） 是一個管理平臺，您可以使用該平臺以代碼配置來管理 IT 和開發基礎結構。

> [!NOTE]
> Linux 的 DSC 擴展和[Linux 的 Azure 監視器虛擬機器擴展](/azure/virtual-machines/extensions/oms-linux)當前存在衝突，在並行配置中不受支援。 不要在同一 VM 上同時使用這兩個解決方案。

DSCForLinux 擴展由微軟發佈和支援。 此擴充功能可在 Azure 虛擬機器上安裝 OMI 和 DSC 代理程式。 DSC 擴展還可以執行以下操作：


- 將 Linux VM 註冊到 Azure 自動化帳戶，以便從 Azure 自動化服務（註冊擴展操作）中提取配置。
- 將 MOF 配置推送到 Linux VM（推送擴展操作）。
- 將元 MOF 配置應用於 Linux VM 以配置拉取伺服器以拉取節點配置（拉擴展操作）。
- 將自訂 DSC 模組安裝到 Linux VM（安裝擴展操作）。
- 從 Linux VM 中刪除自訂 DSC 模組（刪除擴展操作）。

 

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

DSC Linux 延伸模組可支援所有 [Azure 背書的 Linux 散發套件](/azure/virtual-machines/linux/endorsed-distros)，但下列項目除外：

| 散發 | 版本 |
|---|---|
| Debian | 所有版本 |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>網際網路連線

DSCForLinux 擴展要求目標虛擬機器連接到互聯網。 例如，註冊擴展需要連接到自動化服務。 對於其他操作（如拉取、拉取、安裝）需要連接到 Azure 存儲和 GitHub。 這取決於客戶提供的設置。

## <a name="extension-schema"></a>擴充功能結構描述

### <a name="public-configuration"></a>公用組態

以下是所有受支援的公用組態參數：

* `FileUri`：（可選，字串）MOF 檔案的 uri、元 MOF 檔案或自訂資源 ZIP 檔案。
* `ResourceName`：（可選，字串）自訂資源模組的名稱。
* `ExtensionAction`：(選擇性，字串) 指定擴充功能的作用。 有效值為"註冊、推送、拉取、安裝"和"刪除"。 如果未指定，預設情況下將被視為推送操作。
* `NodeConfigurationName`：（可選，字串）要應用的節點配置的名稱。
* `RefreshFrequencyMins`：（可選，int） 指定 DSC 嘗試從拉取伺服器獲取配置的頻率（以分鐘）表示。 
       如果拉取伺服器上的配置與目標節點上的當前配置不同，則將其複製到掛起的存儲並應用。
* `ConfigurationMode`：(選擇性，字串) 指定 DSC 套用組態的方式。 有效值為"僅應用、應用和監視"以及應用和自動校正。
* `ConfigurationModeFrequencyMins`：(選擇性，int) 指定 DSC 確認組態處於適當狀態的頻率 (以分鐘為單位)。

> [!NOTE]
> 如果使用的版本早于 2.3，則模式參數與擴展操作相同。 模式似乎是一個重載的術語。 為了避免混淆，從版本 2.3 開始使用擴展操作。 為了顧及回溯相容性，此擴充功能同時支援模式和 ExtensionAction。 
>

### <a name="protected-configuration"></a>受保護的組態

以下是所有受支援和保護的組態參數：

* `StorageAccountName`：（可選，字串）包含檔的存儲帳戶的名稱
* `StorageAccountKey`：（可選，字串）包含檔的存儲帳戶的鍵
* `RegistrationUrl`：（可選，字串）Azure 自動化帳戶的 URL
* `RegistrationKey`：（可選，字串）Azure 自動化帳戶的訪問金鑰


## <a name="scenarios"></a>案例

### <a name="register-an-azure-automation-account"></a>註冊 Azure 自動化帳戶
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

電源外殼格式
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>將 MOF 設定檔（在 Azure 存儲帳戶中）應用於 VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

電源外殼格式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>將 MOF 設定檔（在公共存儲中）應用於 VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

電源外殼格式
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>將元 MOF 設定檔（在 Azure 存儲帳戶中）應用於 VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

電源外殼格式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>將中繼 MOF 組態檔 (位於公用儲存體中) 套用至 VM
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
電源外殼格式
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>將自訂資源模組（Azure 存儲帳戶中的 ZIP 檔案）安裝到 VM
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

電源外殼格式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>將自訂資源模組（公共存儲中的 ZIP 檔案）安裝到 VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
電源外殼格式
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>從 VM 中移除自訂資源模組
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
電源外殼格式
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 當您部署一個或多個需要部署後配置的虛擬機器（如載入 Azure 自動化）時，範本是理想的選擇。 

範例 Resource Manager 範本為 [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) 和 [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu)。

有關 Azure 資源管理器範本的詳細資訊，請參閱創作[Azure 資源管理器範本](../../azure-resource-manager/templates/template-syntax.md)。


## <a name="azure-cli-deployment"></a>Azure CLI 部署

### <a name="use-azure-cliazure-cli"></a>使用 [Azure CLI][azure-cli]
在部署 DSCForLinux 擴展之前，請`public.json``protected.json`根據第 3 節中的不同方案配置 和。

#### <a name="classic"></a>傳統

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

經典部署模式也稱為 Azure 服務管理模式。 您可以執行下列命令以切換至該模式：
```
$ azure config mode asm
```

您可以通過運行以下功能部署 DSCForLinux 擴展：
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

若要了解最新的可用擴充功能版本，請執行：
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
您可以執行下列命令，以切換至 Azure Resource Manager 模式：
```
$ azure config mode arm
```

您可以通過運行以下功能部署 DSCForLinux 擴展：
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> 在 Azure 資源管理器模式下`azure vm extension list`，現在不可用。
>

### <a name="use-azure-powershellazure-powershell"></a>使用 [Azure 電源外殼][azure 電源外殼]

#### <a name="classic"></a>傳統

您可以通過運行：

```powershell>
Add-AzureAccount
```

並通過運行 DSCForLinux 擴展：

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

根據上一節中的不同方案更改$privateConfig和$publicConfig的內容。
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

您可以通過運行：

```powershell>
Login-AzAccount
```

要瞭解有關如何在 Azure 資源管理器中使用 Azure PowerShell 的更多資訊，請參閱[使用 Azure PowerShell 管理 Azure 資源](../../azure-resource-manager/management/manage-resources-powershell.md)。

您可以通過運行以下功能部署 DSCForLinux 擴展：

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

根據上一節中的不同方案更改$privateConfig和$publicConfig的內容。
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

有關擴展部署狀態的資料可以從 Azure 門戶和使用 Azure CLI 檢索。 要查看給定 VM 的擴展的部署狀態，請使用 Azure CLI 運行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

錯誤代碼：51 表示不支援的分發或不支援的擴展操作。
在某些情況下，當電腦中已存在更高版本的 OMI 時，DSC Linux 擴展無法安裝 OMI。 [錯誤回應：(000003) 不允許降級]



### <a name="support"></a>支援

如果本文中的任何一點都需要更多説明，請與[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家聯繫。 或者，您可以提交 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選擇 **"獲取支援**"。 有關使用 Azure 支援的資訊，請閱讀[Microsoft Azure 支援常見問題解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Linux 功能](features-linux.md)。
