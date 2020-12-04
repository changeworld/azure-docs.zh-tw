---
title: 如何使用多租用戶主機權限在 Azure 上部署 Windows 10
description: 瞭解如何使用多租使用者主機許可權將內部部署授權帶到 Azure，以充分發揮您的 Windows 軟體保證優勢。
author: xujing
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 101f2cfe57624502764d145351a6343cfdd2a334
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572859"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>如何使用多租用戶主機權限在 Azure 上部署 Windows 10 
對於每位使用者都具有 Windows 10 企業版 E3/E5 或每位使用者都具有 Windows 虛擬桌面存取 (使用者訂用帳戶授權或附加元件使用者訂用帳戶授權) 的客戶，適用於 Windows 10 的多租用戶主機權限可讓您將 Windows 10 授權帶到雲端，並在 Azure 上執行 Windows 10 虛擬機器，而不必付費取得其他授權。 如需詳細資訊，請參閱[適用於 Windows 10 的多租用戶主機](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)。

> [!NOTE]
> 本文會說明如何實作 Azure Marketplace 上適用於 Windows 10 專業版桌面版映像的授權權益。
> - 針對 Azure Marketplace 上適用於 MSDN 訂閱的 Windows 7、8.1、10 企業版 (x64) 映像，請參閱 [Azure 中的 Windows 用戶端開發/測試案例](client-images.md)
> - 若要了解 Windows Server 授權權益，請參閱 [Windows Server 映像的 Azure Hybrid Use Benefit](hybrid-use-benefit-licensing.md)。
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>從 Azure Marketplace 部署 Windows 10 映像 
針對 PowerShell、CLI 和 Azure Resource Manager 範本部署，您可以使用下列 publishername、供應專案、sku 來找到 Windows 10 映射。

| OS  |      PublisherName      |  供應項目 | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="qualify-for-multi-tenant-hosting-rights"></a>符合多租使用者裝載許可權的資格 
若要符合多租使用者裝載許可權，並在 Azure 使用者上執行 Windows 10 映射必須有下列其中一個訂用帳戶： 

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 A3/A5 
-   Windows 10 企業版 E3/E5
-   Windows 10 教育版 A3/A5 
-   Windows VDA E3/E5


## <a name="uploading-windows-10-vhd-to-azure"></a>將 Windows 10 VHD 上傳到 Azure
如果您要上傳一般化的 Windows 10 VHD，請注意 Windows 10 未預設啟用內建的 Administrator 帳戶。 若要啟用內建的 Administrator 帳戶，請在自訂指令碼擴充功能中加入下列命令。

```powershell
Net user <username> /active:yes
```

下列 PowerShell 程式碼片段會將所有系統管理員帳戶標示為作用中，包括內建的系統管理員。 這個範例適用於不知道內建系統管理員使用者名稱的情況。
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
其他資訊： 
* [如何將 VHD 上傳至 Azure](upload-generalized-managed.md)
* [如何準備要上傳至 Azure 的 Windows VHD](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>使用多租用戶主機權限部署 Windows 10
確定您已 [安裝並設定最新的 Azure PowerShell](/powershell/azure/)。 備妥 VHD 之後，請使用 `Add-AzVhd` Cmdlet，將 VHD 上傳到 Azure 儲存體帳戶，如下所示：

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**使用 Azure Resource Manager 範本部署進行部署** Resource Manager 範本內可指定另外的 `licenseType` 參數。 您可以閱讀更多有關 [撰寫 Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)的資訊。 將 VHD 上傳至 Azure 之後，請編輯 Resource Manager 範本以將授權類型納入計算提供者，之後再照常部署範本即可：
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**透過 PowerShell 部署** 透過 PowerShell 部署 Windows Server VM 時，您會有另外的 `-LicenseType` 參數。 將 VHD 上傳至 Azure 之後，使用 `New-AzVM` 建立 VM 並指定授權類型，如下所示：
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>確認您的 VM 可享受授權權益
透過 PowerShell 或 Resource Manager 部署方法部署 VM 之後，請依下列方式使用 `Get-AzVM` 來驗證授權類型：
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Windows 10 的輸出類似下列範例，並且會有正確的授權類型：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

下列 VM 在部署時未提供 Azure Hybrid Use Benefit 授權 (例如直接從 Azure 資源庫部署 VM)，您可看出此輸出的差異：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>關於加入 Azure AD 的其他資訊
>[!NOTE]
>Azure 會使用內建的 Administrator 帳戶佈建所有 Windows VM，但此帳戶無法用來加入 AAD。 例如，[設定] > [帳戶] > [存取公司或學校資源] > [+連線] 不會有作用。 您必須建立第二個 Administrator 帳戶並以此帳戶的身分登入，才能手動加入 Azure AD。 您也可以使用布建套件來設定 Azure AD，請使用 *下一* 節中的連結來深入瞭解。
>
>

## <a name="next-steps"></a>後續步驟
- 深入了解如何[設定 Windows 10 的 VDA](/windows/deployment/vda-subscription-activation)
- 深入了解[適用於 Windows 10 的多租用戶主機](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)
