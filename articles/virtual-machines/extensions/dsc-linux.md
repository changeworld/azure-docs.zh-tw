---
title: 適用于 Linux 的 Azure DSC 擴充功能
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383432"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>適用于 Linux 的 DSC 擴充功能（Microsoft.ostcextensions.customscriptforlinux. DSCForLinux）

Desired State Configuration （DSC）是一個管理平臺，可讓您使用設定即程式碼來管理 IT 和開發基礎結構。

> [!NOTE]
> 適用于 linux 的 DSC 擴充功能和[linux 的 Azure 監視器虛擬機器擴充](/azure/virtual-machines/extensions/oms-linux)功能目前有衝突，且在並存設定中不受支援。 請勿在相同的 VM 上一起使用這兩個解決方案。

DSCForLinux 延伸模組是由 Microsoft 發行並受到支援。 此擴充功能可在 Azure 虛擬機器上安裝 OMI 和 DSC 代理程式。 DSC 延伸模組也可以執行下列動作：


- 向 Azure 自動化帳戶註冊 Linux VM，以從 Azure 自動化服務（Register ExtensionAction）提取設定。
- 將 MOF 設定推送至 Linux VM （Push ExtensionAction）。
- 將中繼 MOF 設定套用至 Linux VM 以設定提取伺服器，以便提取節點設定（提取 ExtensionAction）。
- 將自訂 DSC 模組安裝到 Linux VM （安裝 ExtensionAction）。
- 從 Linux VM 移除自訂 DSC 模組（移除 ExtensionAction）。

 

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

DSC Linux 延伸模組可支援所有 [Azure 背書的 Linux 散發套件](/azure/virtual-machines/linux/endorsed-distros)，但下列項目除外：

| 散發 | 版本 |
|---|---|
| Debian | 所有版本 |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>網際網路連線

DSCForLinux 擴充功能會要求目標虛擬機器連線到網際網路。 例如，Register 延伸模組需要與 Automation 服務的連線。 針對其他動作（例如提取、提取、安裝），需要連線到 Azure 儲存體和 GitHub。 這取決於客戶所提供的設定。

## <a name="extension-schema"></a>擴充功能結構描述

### <a name="public-configuration"></a>公用組態

以下是所有受支援的公用組態參數：

* `FileUri`：（選擇性，字串） MOF 檔案、中繼 MOF 檔案或自訂資源 zip 檔案的 uri。
* `ResourceName`：（選擇性，字串）自訂資源模組的名稱。
* `ExtensionAction`：(選擇性，字串) 指定擴充功能的作用。 有效的值為 Register、Push、Pull、Install 和 Remove。 如果未指定，預設會將其視為推送動作。
* `NodeConfigurationName`：（選擇性，字串）要套用的節點設定名稱。
* `RefreshFrequencyMins`：（選擇性，int）指定 DSC 嘗試從提取伺服器取得設定的頻率（以分鐘為單位）。 
       如果提取伺服器上的設定與目標節點上的配置不同，則會將它複製到擱置的存放區並套用。
* `ConfigurationMode`：(選擇性，字串) 指定 DSC 套用組態的方式。 有效值為 ApplyOnly、ApplyAndMonitor 和 ApplyAndAutoCorrect。
* `ConfigurationModeFrequencyMins`：(選擇性，int) 指定 DSC 確認組態處於適當狀態的頻率 (以分鐘為單位)。

> [!NOTE]
> 如果您使用的版本早于2.3，模式參數會與 ExtensionAction 相同。 模式似乎是多載的詞彙。 為了避免混淆，會從2.3 版開始使用 ExtensionAction。 為了顧及回溯相容性，此擴充功能同時支援模式和 ExtensionAction。 
>

### <a name="protected-configuration"></a>受保護的組態

以下是所有受支援和保護的組態參數：

* `StorageAccountName`：（選擇性，字串）包含檔案的儲存體帳戶名稱
* `StorageAccountKey`：（選擇性，字串）包含檔案之儲存體帳戶的金鑰
* `RegistrationUrl`：（選擇性，字串） Azure 自動化帳戶的 URL
* `RegistrationKey`：（選擇性，字串） Azure 自動化帳戶的存取金鑰


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

PowerShell 格式
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>將 MOF 設定檔（在 Azure 儲存體帳戶中）套用至 VM

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

PowerShell 格式
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


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>將 MOF 設定檔（在公用儲存體中）套用至 VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell 格式
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>將中繼 MOF 設定檔（在 Azure 儲存體帳戶中）套用至 VM

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

PowerShell 格式
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
PowerShell 格式
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>將自訂資源模組（Azure 儲存體帳戶中的 zip 檔案）安裝到 VM
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

PowerShell 格式
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>將自訂資源模組（公用存放裝置中的 zip 檔案）安裝到 VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell 格式
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
PowerShell 格式
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 當您部署一或多個需要部署後設定的虛擬機器時（例如上架至 Azure 自動化）時，範本是理想的選擇。 

範例 Resource Manager 範本為 [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) 和 [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu)。

如需 Azure Resource Manager 範本的詳細資訊，請參閱[編寫 Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)。


## <a name="azure-cli-deployment"></a>Azure CLI 部署

### <a name="use-azure-cliazure-cli"></a>使用 [Azure CLI] [Azure-CLI]
部署 DSCForLinux 擴充功能之前，請先根據第3節中的不同案例設定您的 `public.json` 和 `protected.json`。

#### <a name="classic"></a>傳統

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

傳統部署模式也稱為 Azure 服務管理模式。 您可以執行下列命令以切換至該模式：
```
$ azure config mode asm
```

您可以藉由執行下列程式來部署 DSCForLinux 延伸模組：
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

您可以藉由執行下列程式來部署 DSCForLinux 延伸模組：
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> 在 Azure Resource Manager 模式中，`azure vm extension list` 目前無法使用。
>

### <a name="use-azure-powershellazure-powershell"></a>使用 [Azure PowerShell] [Azure-PowerShell]

#### <a name="classic"></a>傳統

您可以藉由執行下列動作，在 Azure 服務管理模式中登入您的 Azure 帳戶：

```powershell>
Add-AzureAccount
```

並藉由執行下列程式來部署 DSCForLinux 延伸模組：

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

根據上一節中的不同案例，變更 $privateConfig 和 $publicConfig 的內容。
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

您可以藉由執行下列動作，以 Azure Resource Manager 模式登入您的 Azure 帳戶：

```powershell>
Login-AzAccount
```

若要深入瞭解如何搭配 Azure Resource Manager 使用 Azure PowerShell，請參閱[使用 Azure PowerShell 來管理 Azure 資源](../../azure-resource-manager/management/manage-resources-powershell.md)。

您可以藉由執行下列程式來部署 DSCForLinux 延伸模組：

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

根據上一節中的不同案例，變更 $privateConfig 和 $publicConfig 的內容。
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

您可以從 Azure 入口網站和使用 Azure CLI，抓取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

錯誤碼：51代表不支援的散發或不支援的擴充功能動作。
在某些情況下，當電腦中已有較高版本的 OMI 時，DSC Linux 擴充功能無法安裝 OMI。 [錯誤回應：(000003) 不允許降級]



### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，請洽詢[MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 azure 專家。 或者，您也可以提出 Azure 支援事件。 前往[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。 如需關於使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Linux 功能](features-linux.md)。
