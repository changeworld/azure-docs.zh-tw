---
title: 使用 PowerShell 在 Azure 雲端服務中啟用診斷 | Microsoft Docs
description: 瞭解如何使用 PowerShell 從具有 Azure 診斷擴充功能的 Azure 雲端服務收集診斷資料。
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: tagore
ms.openlocfilehash: e3c4beac5bd88ba0bbefa91fc83976416b24d2fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88141069"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>使用 PowerShell 在 Azure 雲端服務中啟用診斷
您可以使用 Azure 診斷延伸模組，從雲端服務收集診斷資料 (例如應用程式記錄、效能計數器等)。 本文描述如何使用 PowerShell 啟用雲端服務的 Azure 診斷延伸模組。  如需這篇文章所需要的必要條件，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/) 。

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>啟用診斷延伸模組做為部署雲端服務的一部分
此方法適用於可以啟用診斷延伸模組做為雲端服務佈署一部分的連續整合類型案例。 建立新的雲端服務部署時，您可以藉由將 *ExtensionConfiguration* 參數傳遞至 get-azuredeployment Cmdlet 來啟用診斷擴充 [功能](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-3.7.0) 。 *ExtensionConfiguration* 參數接受以 [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) Cmdlet 建立的診斷組態陣列。

下列範例示範如何為某個雲端服務 (其中的 WebRole 和 WorkerRole 各自擁有不同的診斷組態) 啟用診斷。

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

如果診斷組態檔以某個儲存體帳戶名稱指定 `StorageAccount` 元素，則 `New-AzureServiceDiagnosticsExtensionConfig` Cmdlet 將會自動使用該儲存體帳戶。 但該儲存體帳戶所屬的訂用帳戶，必須與雲端服務部署時所屬的訂用帳戶相同，這個方法才有作用。

從 Azure SDK 2.6 開始，由 MSBuild 發佈目標輸出所產生的擴充設定檔，會包含以服務組態檔 (.cscfg) 中所指定的診斷組態字串為基礎的儲存體帳戶名稱。 下列指令碼示範在部署雲端服務時，如何從發佈目標輸出來剖析擴充設定檔，以及如何設定每個角色的診斷擴充。

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online 使用類似的方法，自動部署具有診斷延伸模組的雲端服務。 請參閱 [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) 來取得完整的範例。

如果您未在診斷組態中指定 `StorageAccount`，則需要將 *StorageAccountName* 參數傳入 Cmdlet。 如果指定了 *StorageAccountName* 參數，則此 Cmdlet 一律會使用參數中指定的儲存體帳戶，而不是在診斷設定檔中指定的儲存體帳戶。

如果診斷儲存體帳戶和雲端服務分別屬於不同的訂用帳戶，您必須明確地將 *StorageAccountName* 和 *StorageAccountKey* 參數傳入 Cmdlet。 當診斷儲存體帳戶位於相同的訂用帳戶中時，不需要 *StorageAccountKey* 參數，因為 Cmdlet 會在啟用診斷擴充功能時自動查詢並設定金鑰值。 不過，如果診斷儲存體帳戶位於不同的訂用帳戶中，則 Cmdlet 可能無法自動取得金鑰，而您必須透過 *StorageAccountKey* 參數明確指定金鑰。

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>在現有的雲端服務上啟用診斷延伸模組
您可以使用 [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) Cmdlet，在執行中的雲端服務上啟用或更新診斷組態。

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>取得目前的診斷延伸模組組態
使用 [Get AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) Cmdlet 取得雲端服務目前的診斷組態。

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>移除診斷延伸模組
若要關閉雲端服務上的診斷功能，您可以使用 [set-azureservicediagnosticsextension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) Cmdlet。

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

如果您在未使用*role*參數的情況下使用*set-azureservicediagnosticsextension*或*new-azureservicediagnosticsextensionconfig*來啟用診斷擴充功能，則可以在不使用*role*參數的情況下使用*set-azureservicediagnosticsextension*移除擴充功能。 如果啟用延伸模組時使用了 *Role* 參數，則移除延伸模組時也必須使用該參數。

若要從每個個別的角色移除診斷延伸模組：

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>後續步驟
* 如需使用 Azure 診斷和其他技術疑難排解問題的詳細指引，請參閱 [在 Azure 雲端服務和虛擬機器中啟用診斷](cloud-services-dotnet-diagnostics.md)。
* [診斷組態結構描述](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) 說明診斷延伸模組的各種 XML 組態選項。
* 若要了解如何啟用虛擬機器的診斷延伸模組，請參閱 [使用 Azure 資源管理員範本建立具有監控和診斷功能的 Windows 虛擬機器](../virtual-machines/windows/extensions-diagnostics-template.md)



