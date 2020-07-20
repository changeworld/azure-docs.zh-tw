---
title: Azure 的預期狀態設定概觀
description: 了解如何將 Microsoft Azure 延伸模組處理常式用於 PowerShell 預期狀態設定 (DSC)。 本文包含先決條件、架構及 Cmdlet。
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 82d268eedd73b8de670da93ad3a601b5e75e6444
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82188530"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure 預期狀態設定延伸模組處理常式簡介

「Azure VM 代理程式」和相關的延伸模組是 Microsoft Azure 基礎結構服務的一部分。 VM 延伸模組是可延伸 VM 功能及簡化各種 VM 管理作業的軟體元件。

Azure Desired State Configuration （DSC）延伸模組的主要使用案例是將 VM 啟動至[Azure 自動化狀態設定（dsc）服務](../../automation/automation-dsc-overview.md)。
此服務提供的[優點](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service)包括持續管理 VM 設定，以及與其他操作工具（例如 Azure 監視）的整合。
使用延伸模組向服務註冊 VM，可提供彈性的解決方案，甚至可以跨 Azure 訂用帳戶運作。

您可以脫離 Automation DSC 服務來單獨使用 DSC 延伸模組。
不過，這只會將設定推送至 VM。
在 VM 中的本機以外，不會提供任何正在進行的報告。

本文提供兩種案例的相關資訊：使用 DSC 延伸模組來進行「自動化」上線，以及藉由使用 Azure SDK 來使用 DSC 延伸模組作為工具，以將設定指派給 VM。

## <a name="prerequisites"></a>必要條件

- **本機電腦**：若要與 Azure VM 延伸模組互動，您必須使用 Azure 入口網站或 Azure PowerShell SDK。
- **客體代理程式**：DSC 設定所設定的 Azure VM 必須是支援 Windows Management Framework (WMF) 4.0 或更新版本的 OS。 如需所支援 OS 版本的完整清單，請參閱 [DSC 延伸模組版本歷程記錄](../../automation/automation-dsc-extension-history.md) \(英文\)。

## <a name="terms-and-concepts"></a>詞彙和概念

本指南假設您已熟悉下列概念︰

- **設定**：DSC 設定文件。
- **節點**：DSC 設定的目標。 在本檔中， *node*一律是指 Azure VM。
- **設定資料**：具有設定之環境資料的 .psd1 檔案。

## <a name="architecture"></a>架構

Azure DSC 延伸模組會使用「Azure VM 代理程式」架構來傳遞、套用在 Azure VM 上執行的 DSC 組態，並針對這些組態提出報告。 DSC 延伸模組接受設定文件和一組參數。 如果未提供任何檔案，就會隨延伸模組內嵌一個[預設設定指令碼](#default-configuration-script)。 預設設定指令碼只用來在[本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)中設定中繼資料。

第一次呼叫延伸模組 時，會使用下列邏輯來安裝某個版本的 WMF︰

- 如果 Azure VM 作業系統是 Windows Server 2016，則不採取任何動作。 Windows Server 2016 已安裝最新版的 PowerShell。
- 如果已指定 **wmfVersion** 屬性，就會安裝該版本的 WMF，除非該版本與 VM 的 OS 不相容。
- 如果未指定任何 **wmfVersion** 屬性，則會安裝 WMF 的最新適用版本。

安裝 WMF 需要重新啟動。 重新啟動之後，延伸模組會下載 **modulesUrl** 屬性 (如有提供) 中所指定的 .zip 檔案。 如果此位置在 Azure Blob 儲存體中，您可以在 **sasToken** 屬性中指定 SAS 權杖來存取檔案。 下載並解壓縮 .zip 之後，在**configurationFunction**中定義的設定函式會執行以產生一個 mof （[受控物件格式](https://docs.microsoft.com/windows/win32/wmisdk/managed-object-format--mof-)）檔案。 接著，擴充功能就會使用產生的 .mof 檔來執行 `Start-DscConfiguration -Force`。 延伸模組會擷取輸出並將其寫入至 Azure 狀態通道。

### <a name="default-configuration-script"></a>預設設定指令碼

Azure DSC 延伸模組包含預設設定指令碼，可在將 VM 上線至 Azure Automation DSC 服務時使用。 指令碼參數會對齊[本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)的可設定屬性。 如需了解指令碼參數，請參閱[採用 Azure Resource Manager 範本的預期狀態設定延伸模組](dsc-template.md)中的[預設設定指令碼](dsc-template.md#default-configuration-script)。 如需完整的指令碼，請參閱 [GitHub 中的 Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true)。

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>向 Azure 自動化狀態設定（DSC）服務註冊的資訊

使用 DSC 擴充功能向狀態設定服務註冊節點時，將需要提供三個值。

- RegistrationUrl-Azure 自動化帳戶的 HTTPs 位址
- RegistrationKey-用來向服務註冊節點的共用密碼
- NodeConfigurationName-要從服務提取以設定伺服器角色的節點設定（MOF）的名稱

您可以在 Azure 入口網站中看到這項資訊，或者您可以使用 PowerShell。

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

針對節點設定名稱，請確定 Azure 狀態設定中有節點設定。  如果不存在，延伸模組部署將會傳回失敗。  也請確定您使用的是*節點*設定的名稱，而不是設定。
設定會定義在用[來編譯節點設定（MOF 檔案）](https://docs.microsoft.com/azure/automation/automation-dsc-compile)的腳本中。
名稱一律會是設定，後面接著句點 `.` 和 `localhost` 或特定電腦名稱稱。

## <a name="dsc-extension-in-resource-manager-templates"></a>Resource Manager 範本中的 DSC 延伸模組

在大多數案例中，Resource Manager 部署範本是與 DSC 延伸模組搭配運作的預期方式。 如需如何在 Resource Manager 部署範本中包含 DSC 延伸模組的資訊和範例，請參閱專用文件頁面[採用 Azure Resource Manager 範本的預期狀態設定延伸模組](dsc-template.md)。

## <a name="dsc-extension-powershell-cmdlets"></a>DSC 延伸模組 PowerShell Cmdlet

用來管理 DSC 延伸模組的 PowerShell Cmdlet 最適合用於互動式疑難排解和資訊收集案例。 您可以使用這些 Cmdlet 來封裝、發佈及監視 DSC 延伸模組部署。 DSC 延伸模組的 Cmdlet 尚未更新，無法與[預設設定指令碼](#default-configuration-script)搭配運作。

**Publish-AzVMDscConfiguration** Cmdlet 會接受設定檔、掃描其中是否有相依的 DSC 資源，然後建立 .zip 檔案。 .zip 檔案包含設定及制定設定所需的 DSC 資源。 此 Cmdlet 也可以使用 *-OutputArchivePath* 參數在本機建立套件。 否則，此 Cmdlet 會將 .zip 檔案發佈至 Blob 儲存體，然後使用 SAS 權杖來保護它。

此 Cmdlet 所建立的 .ps1 設定指令碼在位於封存資料夾根目錄的 .zip 檔案中。 模組資料夾是放在資源的封存資料夾中。

**Set-AzVMDscExtension** Cmdlet 會將 PowerShell DSC 延伸模組所需的設定插入虛擬機器設定物件中。

**Get-AzVMDscExtension** Cmdlet 會擷取特定虛擬機器的 DSC 延伸模組狀態。

**Get-AzVMDscExtensionStatus** Cmdlet 會擷取 DSC 延伸模組處理常式所制定之 DSC 設定的狀態。 此動作可在單一 VM 上執行，也可在一組 VM 上執行。

**Remove-AzVMDscExtension** Cmdlet 會從特定虛擬機器中移除延伸模組處理常式。 此 Cmdlet「不會」** 移除設定、將 WMF 解除安裝，或變更已在 VM 上套用的設定。 它只會移除延伸模組處理常式。

Resource Manager DSC 延伸模組 Cmdlet 相關的重要資訊：

- Azure Resource Manager Cmdlet 是同步的。
- *ResourceGroupName**VMName**ArchiveStorageAccountName**Version* 及 *Location* 全都是必要參數。
- *ArchiveResourceGroupName* 是選擇性參數。 當您儲存體帳戶所屬的資源群組與建立 VM 的資源群組不同時，您可以指定此參數。
- 使用 **AutoUpdate** 參數以在有最新版本可用時，自動將延伸模組處理常式更新至最新版本。 此參數有可能導致虛擬機器在新版 WMF 發行時重新啟動。

### <a name="get-started-with-cmdlets"></a>開始使用 Cmdlet

Azure DSC 延伸模組可以使用 DSC 設定文件，在部署期間直接設定 Azure VM。 此步驟不會向「自動化」註冊節點。 該節點「不會」** 受到集中管理。

下列範例說明一個簡單的設定範例。 在本機將設定儲存為 iisInstall.ps1。

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

下列命令會將 iisInstall.ps1 腳本放在指定的 VM 上。 這些命令也會執行設定，然後回報狀態。

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

Azure CLI 可以用來將 DSC 延伸模組部署到現有的虛擬機器。

若為執行 Windows 的虛擬機器：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

若為執行 Linux 的虛擬機器：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure 入口網站功能

在入口網站中設定 DSC：

1. 前往 VM。
2. 在 [設定]**** 底下，選取 [延伸模組]****。
3. 在建立的新頁面中，選取 [新增]****，然後選取 [PowerShell 預期狀態設定]****。
4. 在延伸模組資訊頁面底端，按一下 [建立]****。

入口網站會收集以下輸入資訊：

- **設定模組或指令碼**：此為必要欄位 (此表單尚未針對[預設設定指令碼](#default-configuration-script)進行更新)。 設定模組和指令碼需要一個包含設定指令碼的 .ps1 檔案，或一個 .ps1 設定指令碼位於根目錄的 .zip 檔案。 如果您使用 .zip 檔案，所有相依資源都必須包含在該 .zip 檔案的模組資料夾中。 您可以使用 Azure PowerShell SDK 所包含的 **Publish-AzureVMDscConfiguration -OutputArchivePath** Cmdlet 來建立 .zip 檔案。 此 .zip 檔案會上傳到您的使用者 Blob 儲存體並受到 SAS 權杖保護。

- 設定**的模組完整名稱**：您可以在 ps1 檔案中包含多個設定函式。 請輸入設定 .ps1 指令碼的名稱，後面跟著 \\ 和設定函式的名稱。 例如，如果 .ps1 指令碼的名稱為 configuration.ps1，而設定為 **IisInstall**，則請輸入 **configuration.ps1\IisInstall**。

- **設定引數**︰如果設定函式接受引數，請以 **argumentName1=value1,argumentName2=value2** 格式在這裡輸入引數。 此格式與 PowerShell Cmdlet 或 Resource Manager 範本中接受設定引數時所採用的格式不同。

- 設定**資料 .psd1**檔：您的設定需要 .psd1 中的設定資料檔案。請使用此欄位來選取資料檔案，並將它上傳至您的使用者 blob 儲存體。 此設定資料檔在 Blob 儲存體中會受到 SAS 權杖保護。

- **WMF 版本**指定應該安裝在您虛擬機器上的 Windows Management Framework (WMF) 版本。 將此屬性設定為 latest 會安裝最新版的 WMF。 此屬性目前只有下列可能值：4.0、5.0、5.1 及 latest。 這些可能的值可能會更新。 預設值為 **latest**。

- **資料收集**：決定延伸模組是否會收集遙測資料。 如需詳細資訊，請參閱 [Azure DSC 延伸模組集合](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) \(英文\)。

- **版本**：指定要安裝的 DSC 延伸模組版本。 如需版本的相關資訊，請參閱 [DSC 延伸模組版本歷程記錄](/powershell/scripting/dsc/getting-started/azuredscexthistory) (英文)。

- **自動升級次要版本**：此欄位會對應至 Cmdlet 中的 **AutoUpdate** 參數，並允許延伸模組在安裝時自動更新為最新版本。 [是]**** 會指示延伸模組在處理延伸模組常式時，要使用最新版本，[否]**** 將強制安裝指定的 [版本]****。 未選取 [是]**** 亦未選取 [否]****，等同選取 [否]****。

## <a name="logs"></a>記錄

延伸模組的記錄會儲存在下列位置：`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>後續步驟

- 如需有關 PowerShell DSC 的詳細資訊，請移至 [PowerShell 文件中心](/powershell/scripting/dsc/overview/overview)。
- 查看[適用於 DSC 延伸模組的 Resource Manager 範本](dsc-template.md)。
- 如需更多您可以使用 PowerShell DSC 進行管理的功能，以及更多 DSC 資源，請瀏覽 [PowerShell 資源庫](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)。
- 如需有關將敏感性參數傳遞到設定中的詳細資料，請參閱 [使用 DSC 延伸模組處理常式來安全地管理認證](dsc-credentials.md)。
