---
title: Azure Desired State Configuration 擴充功能處理常式
description: 在 Azure VM 上使用 DSC 擴充功能上傳並套用 PowerShell DSC 設定
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 592c731d1851ac36cf9b57864750df0603b6c3fd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383239"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC 延伸模組

## <a name="overview"></a>概觀

Microsoft 已發佈並支援適用於 Windows 的 PowerShell DSC 擴充功能。 此擴充功能會在 Azure VM 上，上傳並套用 PowerShell DSC 設定。 DSC 擴充功能會呼叫 PowerShell DSC，以便在 VM 上套用所收到的 DSC 設定。 本文件詳述適用於 Windows 的 DSC 虛擬機器擴充功能所支援的平台、組態和部署選項。

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

DSC 擴充功能支援下列作業系統

Windows Server 2019、Windows Server 2016、Windows Server 2012R2、Windows Server 2012、Windows Server 2008 R2 SP1、Windows 用戶端 7/8.1/10

### <a name="internet-connectivity"></a>網際網路連線

適用于 Windows 的 DSC 擴充功能會要求目標虛擬機器能夠與 Azure 通訊，以及設定套件（.zip 檔案）的位置（如果它儲存在 Azure 外部的位置）。 

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 Azure Resource Manager 範本中 DSC 擴充功能設定部分的結構描述。 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| publisher | Microsoft.Powershell.DSC | 字串 |
| type | DSC | 字串 |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>設定屬性值

| 名稱 | 資料類型 | 描述
| ---- | ---- | ---- |
| settings.wmfVersion | 字串 | 指定應該安裝於 VM 的 Windows Management Framework 版本。 將此屬性設定為 'latest' 將會安裝最新版的 WMF。 此屬性目前只有下列可能值：‘4.0’、‘5.0’ 及 ‘latest’。 這些可能的值可能會更新。 預設值為 ‘latest’。 |
| settings.configuration.url | 字串 | 指定要從中下載 DSC 組態 zip 檔的 URL 位置。 如果提供的 URL 需要 SAS 權杖才能存取，您必須將 protectedSettings.configurationUrlSasToken 屬性設定為 SAS 權杖的值。 如果已定義 settings.configuration.script 和/或 settings.configuration.function，則需要這個屬性。
| settings.configuration.script | 字串 | 指定指令碼的檔案名稱，其中包含 DSC 組態的定義。 此指令碼必須位於從 configuration.url 屬性所指定的 URL 下載之 zip 檔案的根資料夾中。 如果已定義 settings.configuration.url 和/或 settings.configuration.script，則需要這個屬性。
| settings.configuration.function | 字串 | 指定 DSC 組態的名稱。 命名的組態必須包含在 configuration.script 所定義的指令碼中。 如果已定義 settings.configuration.url 和/或 settings.configuration.function，則需要這個屬性。
| settings.configurationArguments | 集合 | 定義任何您想要傳遞至 DSC 組態的參數。 此屬性將不會經過加密。
| settings.configurationData.url | 字串 | 指定 URL，從中下載您的組態資料 (.pds1) 檔案以做為 DSC 組態的輸入。 如果提供的 URL 需要 SAS 權杖才能存取，您必須將 protectedSettings.configurationDataUrlSasToken 屬性設定為 SAS 權杖的值。
| settings.privacy.dataEnabled | 字串 | 啟用或停用遙測收集。 此屬性只有下列可能值：‘Enable’、‘Disable’ 或 $null。 將此屬性保留空白或 null 將會啟用遙測
| settings.advancedOptions.forcePullAndApply | Bool | 這項設定是設計用來增強使用擴充功能向 Azure 自動化 DSC 註冊節點的經驗。  如果值是 `$true`，延伸模組會等待第一次執行從服務提取的設定，然後才傳回成功/失敗。  如果將此值設定為 $false，延伸模組傳回的狀態只會參考是否已成功向 Azure 自動化狀態設定註冊節點，而且在註冊期間將不會執行節點設定。
| settings.advancedOptions.downloadMappings | 集合 | 定義下載相依性 (例如 WMF 和 .NET) 的替代位置

### <a name="protected-settings-property-values"></a>受保護的設定屬性值

| 名稱 | 資料類型 | 描述
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | 字串 | 定義任何您想要傳遞至 DSC 組態的參數。 此屬性將會經過加密。 |
| protectedSettings.configurationUrlSasToken | 字串 | 指定 SAS 權杖，以存取 configuration.url 所定義的 URL。 此屬性將會經過加密。 |
| protectedSettings.configurationDataUrlSasToken | 字串 | 指定 SAS 權杖，以存取 configurationData.url 所定義的 URL。 此屬性將會經過加密。 |


## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。
部署一或多部需要部署後設定的虛擬機器時，很適合使用範本。
您可以在[Azure 快速入門資源庫](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91)中，找到包含適用于 WINDOWS 的 DSC 擴充功能的範例 Resource Manager 範本。

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

系統便會下載擴充功能套件，並部署至 Azure VM 上的這個位置
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

延伸模組狀態檔案包含 [子狀態] 和 [狀態成功/錯誤] 代碼，以及每個擴充功能執行的詳細錯誤和描述。
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

擴充功能輸出記錄會記錄到下列目錄：

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>錯誤碼及其意義

| 錯誤碼 | 意義 | 可能的動作 |
| :---: | --- | --- |
| 1000 | 一般錯誤 | 擴充功能記錄中的特定例外狀況會提供此錯誤的訊息 |
| 52 | 擴充功能安裝錯誤 | 特定例外狀況會提供此錯誤的訊息 |
| 1002 | Wmf 安裝錯誤 | 安裝 WMF 時發生錯誤。 |
| 1004 | Zip 套件無效 | Zip 無效；解壓縮 Zip 時發生錯誤 |
| 1100 | 引數錯誤 | 表示使用者所提供的輸入有問題。 特定例外狀況會提供此錯誤的訊息|


### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
